# Engineering Runtime Log — Publication Workspace Smoke, Timer Arbitration and Explicit Storage Choice

**Date:** 2026-10-09  
**Status:** Public engineering note — runtime correction applied  
**Related public implementation:** `wilderruiz/ZomniverseGitPet`  
**Public project surface:** `wilderruiz/zomniverse-project`

---

## 1. Runtime smoke exposed two real integration defects

The first Windows runtime smoke of the review-first publication workspace proved the architectural separation, but it also exposed two integration defects that were not visible from static inspection alone.

### Defect A — toolbar flicker

After `Public Get` was introduced, the toolbar intermittently switched between the new publication controls and the older standalone-project controls.

Observed behavior:

```text
Public Get ↓ / Open public
        ↓
legacy Get ↓ / Send ↑ briefly returns
        ↓
Public Get ↓ / Open public returns
        ↓
repeat
```

The underlying problem was not rendering performance. It was **two independent WinForms timers asserting ownership over the same toolbar controls**.

The older `StandaloneProjectPublishingUiRuntime` wakes approximately every 650 ms and restores the logical-project controls. The newer publication compatibility runtime wakes approximately every 500 ms and hides those controls in favor of the review-first publication controls.

That created an ownership race:

```text
legacy timer                    publication timer
    │                                │
    ├─ show scoped Get/Send           │
    │                                ├─ hide scoped Get/Send
    │                                ├─ show Public Get/Open
    ├─ show scoped Get/Send           │
    │                                ├─ hide scoped Get/Send
    ▼                                ▼
               visible flicker
```

### Defect B — implicit OneDrive/Documents location

The first implementation derived the publication checkout from Windows `MyDocuments`.

On the smoke-test machine, `MyDocuments` is redirected through OneDrive, producing a path similar to:

```text
H:\OneDrive\Documents\ZomniverseGitPet\PublicationWorkspaces\zomniverse-project
```

That path was technically valid but violated the intended UX rule: **a new independent project checkout should not be silently placed on a drive or cloud-synchronized folder without the user's explicit choice.**

---

## 2. Correction: one runtime owns the toolbar at a time

The fix does not delete or weaken the original standalone logical-project runtime.

Instead, publication mode now temporarily suppresses the legacy standalone UI timer while `wilderruiz/zomniverse-project` is active. When the user switches to another project, the legacy timer is resumed.

This preserves both behaviors:

```text
NORMAL SCOPED LOGICAL PROJECT
  StandaloneProjectPublishingUiRuntime owns Get/Send

ZOMNIVERSE PUBLICATION WORKSPACE
  PublicationWorkspaceCompatibilityRuntime owns Public Get/Open
```

Selected public implementation:

```csharp
private static void SetLegacyStandaloneTimerSuppressed(bool suppress)
{
    if (suppress)
    {
        if (_legacyTimerSuppressed) return;
        var timer = GetLegacyStandaloneTimer();
        if (timer is null) return;

        timer.Stop();
        _legacyTimerSuppressed = true;
        return;
    }

    ResumeLegacyStandaloneTimer();
}

private static void ResumeLegacyStandaloneTimer()
{
    if (!_legacyTimerSuppressed) return;

    var timer = GetLegacyStandaloneTimer();
    timer?.Start();
    _legacyTimerSuppressed = false;
}

private static System.Windows.Forms.Timer? GetLegacyStandaloneTimer() =>
    typeof(StandaloneProjectPublishingUiRuntime)
        .GetField(
            "_timer",
            BindingFlags.Static | BindingFlags.NonPublic)?
        .GetValue(null) as System.Windows.Forms.Timer;
```

The important engineering point is that the fix establishes **runtime ownership**, rather than creating a faster timer and attempting to win the race more often.

---

## 3. Correction: explicit workspace selection

`Public Get` no longer assumes Documents, OneDrive, or any other storage root.

On the first use for a branch, ZGitPet opens a native folder chooser and asks where the independent public checkout should live.

The selection rules are:

```text
user selects existing Git checkout
        ↓
use that checkout directly

user selects ordinary parent folder
        ↓
use/create <selected>/zomniverse-project
```

Selected public implementation:

```csharp
private static string? ChooseWorkspacePath(IWin32Window owner)
{
    var userProfile = Environment.GetFolderPath(
        Environment.SpecialFolder.UserProfile);

    using var picker = new FolderBrowserDialog
    {
        Description =
            "Choose where ZGitPet should keep the PUBLIC " +
            "zomniverse-project checkout. Choose a parent folder " +
            "and GitPet will use/create a zomniverse-project folder " +
            "inside it. You may also select an existing " +
            "zomniverse-project Git checkout directly.",
        UseDescriptionForTitle = true,
        ShowNewFolderButton = true,
        InitialDirectory = Directory.Exists(userProfile)
            ? userProfile
            : string.Empty
    };

    if (picker.ShowDialog(owner) != DialogResult.OK ||
        string.IsNullOrWhiteSpace(picker.SelectedPath))
        return null;

    var selected = Path.GetFullPath(picker.SelectedPath);

    if (Directory.Exists(Path.Combine(selected, ".git")))
        return selected;

    return Path.Combine(selected, "zomniverse-project");
}
```

No repository file records this private machine-specific location.

---

## 4. Local preference persistence without repository pollution

After a successful synchronization, the chosen checkout path is stored only in the user's local application-data area.

Conceptually:

```text
%LOCALAPPDATA%
  ZomniverseGitPet\
    PublicationWorkspaces\
      zomniverse-project-<branch-hash>.path.txt
```

This keeps the public repository portable while allowing later `Public Get` and `Open public` actions to return to the same explicitly selected checkout.

Selected implementation:

```csharp
private static void SaveWorkspacePath(string branch, string workspace)
{
    var file = GetWorkspacePreferenceFile(branch);
    Directory.CreateDirectory(Path.GetDirectoryName(file)!);
    File.WriteAllText(
        file,
        Path.GetFullPath(workspace),
        Encoding.UTF8);
}

private static string GetWorkspacePreferenceFile(string branch)
{
    var root = Path.Combine(
        Environment.GetFolderPath(
            Environment.SpecialFolder.LocalApplicationData),
        "ZomniverseGitPet",
        "PublicationWorkspaces");

    var branchKey = Convert.ToHexString(
        SHA256.HashData(
            Encoding.UTF8.GetBytes(
                branch.Trim().ToLowerInvariant())))
        .ToLowerInvariant()[..12];

    return Path.Combine(
        root,
        $"zomniverse-project-{branchKey}.path.txt");
}
```

This is deliberately **local state**, not publication state.

---

## 5. Existing accidental checkout is not deleted

The earlier OneDrive/Documents checkout is intentionally left untouched.

Deleting it automatically would violate the same conservative principle used throughout ZGitPet: the application should not destroy a Git workspace merely because its default location policy changed.

The user can choose to:

- keep that checkout by selecting its parent/existing checkout during the next `Public Get`;
- choose a completely different drive/folder;
- manually remove the earlier checkout after verifying it is no longer needed.

---

## 6. The clean-workspace guard remains in force

Explicit path selection does not weaken synchronization safety.

Before resetting the public checkout to the fetched public branch, ZGitPet still requires:

```text
git status --porcelain
        ↓
empty output only
        ↓
fetch / checkout / reset allowed
```

If a local agent has edited the public checkout, `Public Get` stops instead of overwriting that work.

This preserves the review-first workflow:

```text
Public Get
   ↓
local agent work
   ↓
review
   ↓
commit/publish deliberately
```

---

## 7. Why this runtime correction is architecturally important

The smoke test demonstrated a broader principle relevant to multi-agent development tooling:

> A safe logical separation is not enough if two runtime controllers still believe they own the same UI or filesystem decision.

The corrected implementation establishes explicit ownership at three levels:

```text
UI ownership
  one timer/controller owns publication controls

filesystem ownership
  the user chooses the public checkout location

publication ownership
  the public repository remains independent from private source
```

That is more robust than merely hiding controls or changing a default path.

---

## 8. Corrected smoke checklist

The next Windows smoke should verify:

```text
1. open ZOMNIVERSE-PROJECT
2. wait at least 10 seconds
3. confirm Public Get/Open do not flicker with legacy Get/Send
4. press Public Get with no saved workspace preference
5. confirm a folder chooser appears BEFORE cloning
6. choose a non-OneDrive location
7. confirm zomniverse-project is cloned/synchronized there
8. confirm Open public opens that exact location
9. restart ZGitPet and verify the chosen path is remembered locally
10. make an uncommitted public-workspace edit
11. verify Public Get refuses to overwrite it
12. switch to another ordinary scoped logical project
13. verify its original standalone Get/Send controls resume normally
```

---

## 9. Public/private disclosure boundary

This note includes substantial code because the implementation shown comes from the **public ZomniverseGitPet repository** and helps demonstrate the engineering complexity surrounding safe research/publication workflows.

It does not expose private Zomniverse application source, private model orchestration, unpublished research algorithms, credentials, datasets, or deployment secrets.

The publication boundary remains governed by `WHAT_IS_PUBLISHED_HERE.md`.

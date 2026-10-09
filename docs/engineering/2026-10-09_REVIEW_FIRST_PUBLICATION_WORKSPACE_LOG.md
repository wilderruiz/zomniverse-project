# Engineering Log — Review-First Publication Workspace

**Date:** 2026-10-09  
**Status:** Public engineering note  
**Related repositories:**

- `wilderruiz/zomniverse-project` — curated public Zomniverse publication repository
- `wilderruiz/ZomniverseGitPet` — public desktop Git guardian used to manage project histories
- private Zomniverse source repository — intentionally not reproduced here

---

## 1. Why this change was needed

Zomniverse uses two repositories for two different purposes:

```text
PRIVATE ZOMNIVERSE SOURCE
  active application development
  private research implementation
  internal architecture
  unpublished work

PUBLIC ZOMNIVERSE-PROJECT
  reviewed public project material
  public technical documentation
  publication-linked material
  selected engineering notes
```

Originally, ZGitPet treated the public project repository as a **standalone logical-project mirror**. That was safe for scoped publishing, but it assumed that the online project and the selected private source scope represented the same file set.

That assumption no longer matches the publication model.

The public repository now contains deliberately public-only files such as:

```text
WHAT_IS_PUBLISHED_HERE.md
README.md
docs/TECHNOLOGY.md
docs/engineering/...
```

Those files should not be copied automatically into the private application repository merely because a user presses **Get**.

The revised rule is:

> **private development → review → deliberate public publication**

not:

> **private source scope ↔ automatic public mirror**

---

## 2. The engineering problem

The older standalone-project flow intentionally isolated histories:

```text
private parent repository
        │
        │ selected logical scope
        ▼
ZGItPet isolated publishing workspace
        │
        ▼
standalone GitHub repository
```

`Get` performed the inverse operation only after checking that each incoming file remained inside the configured private-project scope.

That safety block was correct for the old model.

The problem was not the path check itself. The problem was that the public Zomniverse repository had evolved into a different class of project: an **independent publication workspace**.

Removing the scope guard globally would have weakened ZGitPet for every other logical project. Instead, the new implementation preserves the old scoped-mirror mode while adding a review-first compatibility path for the Zomniverse public repository.

---

## 3. New behavior

When the active standalone link targets:

```text
wilderruiz/zomniverse-project
```

ZGItPet now suppresses the old scoped-mirror **Get** and **Send** actions and exposes:

```text
Public Get ↓
Open public
```

`Public Get` maintains an independent local checkout under the user's Documents folder:

```text
Documents/
  ZomniverseGitPet/
    PublicationWorkspaces/
      zomniverse-project/
```

The important guarantee is:

```text
Public Get
   ↓
fetch/clone public repository
   ↓
update independent publication checkout
   ↓
verify WHAT_IS_PUBLISHED_HERE.md when present
   ↓
private Zomniverse working tree remains untouched
```

Local agents such as Codex can then work directly in this public checkout instead of relying on a hidden mirror of the private source repository.

---

## 4. Safety behavior

The publication workspace is intentionally conservative.

Before updating an existing checkout, ZGitPet runs a normal Git cleanliness check:

```text
git status --porcelain
```

If local changes exist, `Public Get` stops. It does not merge, reset, or overwrite agent work automatically.

Only a clean publication workspace is synchronized:

```text
git fetch --prune origin <branch>
git checkout -B <branch> refs/remotes/origin/<branch>
git reset --hard refs/remotes/origin/<branch>
```

This reset is safe only because the operation first proves that the publication checkout has no local changes.

The legacy private-parent scope is not modified by these commands.

---

## 5. Selected implementation code

The implementation lives in the public `ZomniverseGitPet` repository as:

```text
src/ZomniverseGitPet/PublicationWorkspaceCompatibilityRuntime.cs
```

The following excerpts are included here to make the engineering complexity visible without publishing private Zomniverse application code.

### 5.1 Runtime activation

```csharp
[ModuleInitializer]
internal static void InitializeModule()
{
    Application.Idle += StartWhenReady;
}

private static void StartWhenReady(object? sender, EventArgs e)
{
    if (_timer is not null) return;

    _config = typeof(StandaloneProjectPublishingUiRuntime)
        .GetField("_config", BindingFlags.Static | BindingFlags.NonPublic)?
        .GetValue(null) as AppConfig;

    if (_config is null) return;

    Application.Idle -= StartWhenReady;
    _timer = new System.Windows.Forms.Timer { Interval = 500 };
    _timer.Tick += async (_, _) => await TickAsync();
    _timer.Start();
}
```

This lets the compatibility layer attach after the normal GitPet runtime has initialized, without changing the existing scoped-publishing code path for unrelated projects.

### 5.2 Publication-mode routing

```csharp
var project = _config.GetActiveProject();
var link = StandaloneProjectPublishing.GetLink(_config);
var publicationMode = project is not null &&
                      link is not null &&
                      IsTargetPublicationRemote(link.RemoteUrl);

if (publicationMode)
{
    oldGet.Visible = false;
    oldGet.Enabled = false;
    oldSend.Visible = false;
    oldSend.Enabled = false;

    publicGet.Visible = true;
    publicOpen.Visible = true;
}
```

The old Send action is intentionally hidden because rebuilding the public repository from the private project scope would contradict the review-first publication boundary.

### 5.3 Clean-workspace guard

```csharp
var dirty = await RunGitAsync(
    workspace,
    ["status", "--porcelain"],
    TimeSpan.FromSeconds(30));

if (!dirty.Success)
    return (false, "GitPet could not inspect the public publication checkout.");

if (!string.IsNullOrWhiteSpace(dirty.Output))
{
    return (false,
        "The public publication workspace has local changes. " +
        "GitPet will not overwrite or merge them automatically.");
}
```

This is important for agent workflows: local agent edits are treated as work to review, not disposable state.

### 5.4 Bounded Git execution

```csharp
private static async Task<(bool Success, int ExitCode, string Output)> RunGitAsync(
    string workingDirectory,
    IReadOnlyList<string> arguments,
    TimeSpan timeout)
{
    var startInfo = new ProcessStartInfo
    {
        FileName = "git",
        WorkingDirectory = workingDirectory,
        RedirectStandardOutput = true,
        RedirectStandardError = true,
        UseShellExecute = false,
        CreateNoWindow = true
    };

    foreach (var argument in arguments)
        startInfo.ArgumentList.Add(argument);

    using var process = new Process { StartInfo = startInfo };
    process.Start();

    var stdout = process.StandardOutput.ReadToEndAsync();
    var stderr = process.StandardError.ReadToEndAsync();
    using var cts = new CancellationTokenSource(timeout);

    try
    {
        await process.WaitForExitAsync(cts.Token);
    }
    catch (OperationCanceledException)
    {
        try { process.Kill(entireProcessTree: true); } catch { }
        return (false, -1,
            $"Git command timed out after {timeout.TotalSeconds:0} seconds.");
    }

    var output = ((await stdout) + Environment.NewLine + (await stderr)).Trim();
    return (process.ExitCode == 0, process.ExitCode, output);
}
```

This avoids shell-string construction and gives each Git operation an explicit timeout.

---

## 6. Why the original block was not simply deleted

A tempting fix would have been to remove the old rule that incoming paths must be inside the configured private project scope.

That would have been the wrong change.

The scope check protects legitimate standalone logical projects from receiving unrelated files into their parent repositories. Removing it globally would trade one workflow problem for a broader safety regression.

The new implementation therefore follows this rule:

```text
SCOPED MIRROR PROJECT
  keep old scope enforcement

ZOMNIVERSE PUBLICATION WORKSPACE
  keep an independent local checkout
  do not copy public-only files into private source
```

---

## 7. Agent workflow after the change

A local coding/documentation agent can now use the public repository directly:

```text
ZGItPet: Public Get
        ↓
independent public checkout
        ↓
local Codex / other agent edits
        ↓
review Git diff
        ↓
commit deliberately
        ↓
publish to GitHub
```

A remote agent can continue editing the GitHub public repository directly. Later `Public Get` operations synchronize those reviewed online changes into the independent local publication checkout rather than into the private Zomniverse application tree.

---

## 8. Public/private boundary

This engineering note deliberately exposes the workflow and selected code from the **public ZGitPet tool**.

It does **not** publish:

- private Zomniverse backend source;
- unpublished algorithms;
- private model prompts;
- credentials or endpoints;
- internal datasets;
- deployment secrets;
- collaborator-restricted material.

The public engineering story can therefore show meaningful implementation complexity while preserving the research and product boundary defined in `WHAT_IS_PUBLISHED_HERE.md`.

---

## 9. Current status

The first compatibility implementation was committed to `wilderruiz/ZomniverseGitPet` on 2026-10-09.

The intended next validation is a local Windows smoke test:

```text
1. open ZOMNIVERSE-PROJECT in ZGitPet
2. confirm legacy scoped Get/Send are replaced for this public repository
3. press Public Get
4. verify independent checkout is created/updated
5. verify WHAT_IS_PUBLISHED_HERE.md is present
6. verify private Zomniverse tree remains unchanged
7. create a local agent edit inside the publication checkout
8. verify a later Public Get refuses to overwrite the dirty checkout
```

This note records the design and implementation direction; runtime behavior should still be verified in the installed desktop build before treating the change as fully closed.

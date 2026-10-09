# Engineering Log — Unified Git Repository Relocation

**Date:** 2026-10-09  
**Status:** Public engineering note  
**Related public tool:** `wilderruiz/ZomniverseGitPet`

## Problem

ZGItPet originally had two overlapping local-repository location workflows:

```text
Projects → Manage projects → <project> → Reassign / move folder…
```

and a later top-level cross-drive command:

```text
Move repository…
```

The first path could reassign an existing Git checkout and attempted same-volume relocation with `Directory.Move`, but it explicitly rejected cross-volume moves. The later command added cross-drive support through a verified copy transaction, but duplicated the UI and left same-drive relocation on a separate implementation.

Runtime testing showed that this split was confusing and that one repository-location command should own both cases.

## New model

The project submenu keeps one action:

```text
Reassign / move folder…
```

The selected destination determines the operation:

```text
existing Git checkout
        ↓
reassign the GitPet project registration only

empty normal folder
        ↓
move the whole local repository
```

The top-level duplicate move command is no longer created.

## One move transaction for every drive

Both same-drive and cross-drive moves now use the same conservative transaction:

```text
source repository
    ↓
copy complete repository to empty destination
    ↓
verify destination Git root
    ↓
verify HEAD matches
    ↓
verify complete porcelain working-tree state matches
    ↓
persist all affected GitPet project registrations
    ↓
remove old source folder
```

This intentionally avoids relying on `Directory.Move` for same-drive relocation. The same verification behavior is therefore used regardless of whether source and destination are on the same Windows volume.

The copy includes the `.git` directory, tracked files, untracked files, local branches, local commits, remotes, and uncommitted working-tree state.

No commit, pull, push, reset, or GitHub mutation is part of repository relocation.

## Failure behavior

The source repository is not deliberately removed unless the destination has passed Git verification and the new GitPet registrations have been persisted.

If the final old-folder deletion fails, GitPet keeps the verified new checkout as the registered location and leaves the old folder as an additional copy instead of risking data loss.

A non-empty non-Git destination is rejected rather than merged with repository contents.

## Implementation

The public implementation is in:

```text
src/ZomniverseGitPet/CrossDriveRepositoryMoveRuntime.cs
```

The compatibility runtime patches the existing project-management menu rather than introducing a second competing location command. This preserves the original project-management surface while replacing its older relocation behavior with the verified unified transaction.

## Validation still required

The code has been committed, but the merged UI/runtime path should be smoked in the Windows DEV build for both cases:

```text
1. existing Git checkout → reassign only
2. empty folder on same drive → verified repository move
3. empty folder on another drive → verified repository move
4. non-empty non-Git folder → reject safely
5. failed copy/verification → source remains authoritative
```

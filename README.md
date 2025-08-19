# 💪📜 `work-notes`: A *"Dotfiles Manager"* For Work-Related Notes

This repository contains work-related notes.

<img height="350" alt="venn diagram. left 'dotfiles-style co-location', right 'knowledge-bases', center 'work-notes lives here'" src="https://github.com/user-attachments/assets/a9638a1a-d4e1-4e23-9f70-35ad83a704f3" />

Its purpose is to:
- **Keep all work-related notes together** in a single Git repository, and
- Provide a mechanism to **co-locate them with the actual work directories**,
  so they can be easily accessed from an IDE or terminal.
  - This is done with symbolic links, managed by **[GNU
    Stow](https://www.gnu.org/software/stow/).**

> Labor · Labor · Labor · Labor · Labor · Labor.
>
> Dixit · Mihi · Necesse · Esse
>
> Labor · Labor · Labor · Labor · Labor · Labor.
>
> Vidit · Me · Facientem · Meum
>
> Lutum · Lutum · Lutum · Lutum · Lutum · Lutum.
> 
> *- "Work" by Rihanna (2016), translated to Latin.*

## Is this tool for me?

If you answer "yes" to the following questions, then this tool might be for
you:

- Do you use the UNIX terminal?
- Do you want to keep your work-related notes as text files?
- Do you want to keep them all together under source control?
- Do you also want to co-locate them with your work directories/repos?

## Dependencies

- Bash,
- [GNU Stow](https://www.gnu.org/software/stow/), and
- [fzf](https://github.com/junegunn/fzf) (optional, only for the `work-notes`
  script).

## Overview

The notes are files stored in a `./base` directory tree, and GNU Stow symlinks
them in the corresponding `~/work/<client>/<project>` directories.

The installation target is `~/work`, but this can be changed in the `install`
script. 

In principle, this is the same as the tried-and-true formula of having a
version-controlled *dotfiles* directory, and using GNU Stow to symlink it to
the home directory, but applied to the problem of work-related notes.

In other words: Just like a *dotfiles manager* is little more than a GNU Stow
wrapper with some dotfile-specific functionality, `work-notes` is just a GNU
Stow wrapper with some work-note-specific functionality.

While this is not strictly limited to "work-related" notes, the term was
picked because:
- For generic notes, or "knowledge base" management, you usually don't have to
  co-locate the notes anywhere else.
- For personal projects, or projects in which there is just one or two people
  on the same remote repository, the presence of an actual uncommitted notes
  file in the directory is not much of a problem.
- This pattern is most useful for "work-related" projects where you have
  multiple people working on the same repository, you don't want to
  accidentally commit your personal notes, and you also don't want to alter
  the `.gitignore` file just to fit your personal needs.
- It's the actual use-case that made me create this tool.

The examples below assume that this repo is located on `~/work/notes`, but
this is not a requirement. (GNU Stow assumes by default that the target is the
parent directory of the repository, so this fits.)

## Structure

All notes live inside the `./base` directory, which mirrors the structure of
the target `~/work`.

### Sample `work-notes` directory structure

```
~/work/notes/
├── base/
│   └── <client-name>/
│       ├── <project-name>/
│       │   └── notes.md
│       └── notes.md
├── install*
├── new-work-notes*
└── work-notes*
```

### Sample target directory structure

```
~/work/
└── <client-name>/
    ├── <project-name>/
    │   ├── notes.md (SYMLINK to ~/work/notes/...)
    │   └── project repo files...
    └── notes.md (SYMLINK to ~/work/notes/...)
```

The actual directory structure can be anything, but a recommendation is to
have the following kinds of notes, depending on the work requirements:
- `~/work/<client-name>/notes.md`
- `~/work/<client-name>/<project-name>/notes.md`
- `~/work/<client-name>/<project-name>/<repo-name>/notes.md`
- `~/work/<client-name>/<repo-name>/notes.md`
  - This one is useful when a client has a few repos, and there's no need to
    group them by project.

## Setup

- Install GNU Stow if not already installed:
  - MacOS: `brew install stow`
  - Linux: `sudo apt-get install stow`
  - Arch (btw): `sudo pacman -S stow`
- Clone this repository to `~/work/notes`.
  - **Note:** If actually using it for work-related notes, make sure to keep
    it offline, or make it private. And naturally, care must be taken to
    comply with any contractual requirements to purge work-related information
    after your work concludes.
- Create directories in `base/` matching those of your work directory.
- Add your notes in the corresponding directories.
- Run `./install` to create the symlinks in the corresponding `~/work`
  directories.
- Optionally run `make` to install the `work-notes` script to your `$PATH`.
  (See `Makefile` for details.)

## Workflow & Scripts

### `install`

This script runs `stow base` to create or update the symlinks in the `~/work`
directory. Run it after adding new note files to create the corresponding
symlinks.

### `new-work-notes <path>`

Creates a new note file and its parent directories within the `base`
structure.

- To create a default `notes.md`:
  ```bash
  # Creates base/client-x/project-y/notes.md
  new-work-notes client-x/project-y
  ```
- To create a note file with a custom name:
  ```bash
  # Creates base/client-x/project-y/meetings.md
  new-work-notes client-x/project-y/meetings.md
  ```

After creating a new note, run `./install` to link it. **Note:** This is not
done automatically so that the user can review that the file was created as
intended before linking it.

### `work-notes <prefix>`

Launches an `fzf` fuzzy-finder TUI scoped to your notes directory (`./base`).
If an optional `[prefix]` is provided, it's used as the initial search query.
It opens it in your default `$EDITOR`.

```bash
# Finds and opens base/client-x/project-y/notes.md
work-notes client-x/proj
```

## Git Integration

To prevent these symlinked note files from appearing in your project
repositories' `git status`, add them to a global gitignore file.

## Best practices

When using `work-notes`, your work directory contains only symlinks to your
notes. This implies the following:

#### If you want to edit a note...
Do it as usual: `vim ~/work/some-project/notes.md`. It will follow the symlink
and edit the source file.

#### If you want to add, delete, or move notes...
You will need to do it in the source folder, and then run `./install` to sync
the changes. The symlinks for the deleted file will be removed from your work
folder.

## F.A.Q.

#### Why use GNU Stow?

I used Stow to save a little bit of development time. Stow already comes with
good verbose logging, fails with an error message if conflicts are detected,
and removes dead symlinks on update. Basically, it was convenient to build on
top of something that I didn't have to debug.

#### Can I change the `work-notes` folder name?

Yes. Just rename it. Everything will keep working fine - the scripts don't
rely on a specific folder name.

## Prior art
- https://dev.to/futurice/how-i-handle-my-notes-with-symbolic-links-594j
- [Dotfiles Linked To Higher OKRs in Placebo-Controlled
  Study](https://www.youtube.com/watch?v=dQw4w9WgXcQ)

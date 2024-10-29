# can-i-beat-claude
Can I create a better `find` command than Claude can?

Claude created the following to find all "GitHub workflows" directories in a
given directory hierarchy:

```
#!/bin/bash

# Find all git repositories in current directory and subdirectories
find . -type d -name ".git" | while read gitdir; do
    # Get the repository directory (parent of .git)
    repo_dir="$(dirname "$gitdir")"
    
    # Check if .github/workflows exists
    if [ -d "${repo_dir}/.github/workflows" ]; then
        # Do something ...
        echo ${repo_dir}
    fi
done
```

This means that every sub-directory of a git repository will be traversed as
well.

When Claude was asked to refine this, it provided:

```
find . \
    -type d \( \
        -name "node_modules" -o \
        -name ".git" -o \
        -name "venv" -o \
        -name "__pycache__" -o \
        -name "build" -o \
        -name "dist" \
    \) -prune \
    -o -type d -name ".github" -print \
    | while read githubdir; do
        if [ -d "${githubdir}/workflows" ]; then
            # Get the repository directory (parent of .github)
            repo_dir="$(dirname "$githubdir")"
            echo ${repo_dir}
        fi
    done
```

This is much better because the .git subdirectory won't be considered, along
with a host of other directories typically ignored, but the git repo directory
itself would still be traversed.

The algorithm should be find all the capital letter repositories below in the
most efficient manner:

```
  a
    dir1
      dir2
        file1
        dir3
          file1
          file2
    dir4
      A              # Should be found:  a/dir4/A
        .git
        .github
          workflows
  b
    node_modules
    dir5
      .git
      this.py
      __pycache__
        *.pyc
      dir6
        ...          # huge sub-directory hierarchy
      dir7
        ...
      dir8
        ...
  C                  # Should be found:  C
    .git
    .github
      workflows
    dir9
      ...            # huge sub-directory hierarchy
```

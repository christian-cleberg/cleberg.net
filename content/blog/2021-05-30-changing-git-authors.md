+++
date = 2021-05-30
title = "Changing Git Authors"
description = "Learn how to haphazardly replace Git authors and/or emails within the Git history."
+++

## Changing Git Author/Email Based on Previously Committed Email

Here's the dilemma: You've been committing changes to your git repository with
an incorrect name or email (or multiple repositories) and now you want to fix
it. Luckily there's a semi-reliable way to fix that. While I have never
experienced issues with this method, some people have warned that it can mess
with historical hashes and integrity of commits, so use this method only if
you're okay accepting that risk.

Okay, let's create the bash script:

```sh
nano change_git_authors.sh
```

The following information can be pasted directly into your bash script. The only
changes you need to make are to the following variables:

-   `OLD_EMAIL`
-   `CORRECT_NAME`
-   `CORRECT_EMAIL`

```sh
#!/bin/sh

# List all sub-directories in the current directory
for dir in */
do
    # Remove the trailing "/"
    dir=${dir%*/}
    # Enter sub-directory
    cd $dir

    git filter-branch --env-filter '

    OLD_EMAIL="old@example.com"
    CORRECT_NAME="your-new-name"
    CORRECT_EMAIL="new@example.com"

    if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
    then
        export GIT_COMMITTER_NAME="$CORRECT_NAME"
        export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
    fi
    if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
    then
        export GIT_AUTHOR_NAME="$CORRECT_NAME"
        export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
    fi
    ' --tag-name-filter cat -- --branches --tags

    git push --force --tags origin 'refs/heads/*'

    cd ..
done
```

Finally, save the bash script and make it executable.

```sh
chmod a+x change_git_authors.sh
```

Now you can run the script and should see the process begin.

```sh
./change_git_authors.sh
```

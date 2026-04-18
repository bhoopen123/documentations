
### Fun fact
GitHub’s Logo is named as `Invertocat`. 
Its mascot representation is `Octocat` (a cat with octopus like tentacles).

```
pwd 
// The pwd command is a Unix/Linux shell command that stands for “print working directory.”

```

Example,

```
MINGW64 /d/GIT/projects
$ pwd
/d/GIT/projects
```

## Setup the Project Folder

```
pwd
mkdir projects
cd projects
pwd
```

## Git Configuration

```
git version
git config --global user.name "Bhoopendra Sharma"
git config --global user.email "bhoopendra.s@dummy.com"
git config --global --list
```

## Git `merge` vs `rebase` vs `squash commit`

**Git merge** pulls in the latest changes from `main` into the feature branch, creating a new "merge commit" in the process that  ties together the histories of both branches.
Its like creating a knot in the rope that shows where the branches joined.

```
    Feature branch ----> A  -> B  -> C
                        |
main ---------> C0  -> c1  -> D  -> E
```

With Git Merge: 
![Git Merge](/git-github/images/git-merge.png)

**Git rebase** changes the base of our feature branch to the latest commits on `main` and then replays our changes from there. It gives us a clean, straight forward commit history, which many people prefer.
"Git rebase and fast-forward merge".
We use git rebase to move our feature branch's changes onto the tip of `main` and then perform a fast-forward merge.
This is like straightening out the rope so it's all in one line.

```
    Feature branch ----> A  -> B  -> c
                        |
main ---------> C0  -> c1  -> D  -> E
```

With Git rebase: 
![Git Rebase](/git-github/images/git-rebase.png)

**Squash commits** with squashing, all the feature branch commits are squeezed into a single commit when merged into main. 
This keeps main history linear like rebasing, while creating a single merge commit. But remember we will lose the fine details of individual feature commits in the main branch's commit history.

With Squash Commit:
![Git Squash Commit](/git-github/images/squash-commit.png)

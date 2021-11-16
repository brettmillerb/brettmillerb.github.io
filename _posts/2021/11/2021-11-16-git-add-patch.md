---
title: git add --patch - Selectively track changes within a file
summary: Hunking your work for fine grained control over changes

excerpt_separator: <!--more-->

tags:
    - git
    - productivity
---
If there is one thing I have learned whilst using git is that you never really stop learning git!

### Scenario
I am working on fixing something in a project and whilst doing so I notice something else which needs to be fixed. Now I could go and raise an issue against the repo to remind myself that it needs to be fixed, alternatively I could add a `TODO` in the code and there are extensions which track that for you.

Or if you're really clever you can fix it straight away and utilise git to selectively track changes within the file.

<!--more-->

### git add
Normally working on a change within a project, you will make and save the changes in your file then using `git add` you will add the changes you just made to your index.

```bash
# Add all local changes to your index
git add .

git commit -m "Made a super awesome change"
```

You can be more selective than this by only commiting certain files so you can chunk changes across multiple files into different commits.

```bash
# Only add changes to a specific file to your index
git add MyProject/subFolder/file.ps1
```

### git add --patch
You can go one step further utilising `git add --patch` which allows you to add specific changes within the same file to different commits.

Using this blog post for example.

```bash
❯❯ git status -u
On branch master
Your branch is up to date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	_posts/2021/11/2021-11-16-git-add-patch.md

nothing added to commit but untracked files present (use "git add" to track)
```

I can choose to add different parts of the file to the index so that I can make my commits smaller and easier to track within the git history.

```bash
❯❯ git add --patch _posts/2021/11/2021-11-16-git-add-patch.md

@@ -17,4 +17,6 @@

 <!--more-->

+### git add
+Normally working on a change within a project....etc

(1/3) Stage this hunk [y,n,q,a,d,j,J,g,/,e,?]?
```

Git will show you a diff information about where the code is in the file, followed by the diff (hunk) of code that it has selectively chosen for you. This is followed by the number of hunks and some interactive options for adding code to the index.

Typing `?` will show help for the interactive options. I've only listed the simple ones here:

```bash
y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this hunk or any of the remaining ones
d - do not stage this hunk or any of the later hunks in the file
s - split the current hunk into smaller hunks
? - print help
```

Once you have selected the hunks you want to add you will commit your changes with a relevant commit message

```bash
❯❯ git commit -m "Added first 2 hunks from patch"
[gitAddPatch 4f998ba] Added first 2 hunks from patch
 1 file changed, 29 insertions(+)
 create mode 100644 _posts/2021/11/2021-11-16-git-add-patch.md
```

You'll notice that running `git status` again will still show the file still has untracked changes which is the remaining `hunk` that we didn't add to the index.

```bash
❯❯ git status
On branch gitAddPatch
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   _posts/2021/11/2021-11-16-git-add-patch.md
```

You can then add the rest of the file to your index with a final `git add .` and a meaningful commit message.

```bash
* b23195b - (HEAD -> gitAddPatch) Added rest of changes to file (24 seconds ago) <Brett Miller>
* 4f998ba - Added first 2 hunks from patch (3 minutes ago) <Brett Miller>
```

### Conclusion
Git as a utility is massive and I've definitely only scratched the surface of what it can do. Learning these little tips and tricks save me significant time and effort whilst working on projects.

This came in handy recently when I had some parameters that I was changing during testing and I wanted to commit changes to the code but not the minor changes to the parameters that I was using for testing. I was able to fix another bug and patch the change to the file as a separate commit so that I could keep my git history clean and have it reviewed as a different pull request.
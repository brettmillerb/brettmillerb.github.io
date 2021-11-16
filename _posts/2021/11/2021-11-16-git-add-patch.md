---
title: Selectively track parts of a file in git
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


### Conclusion


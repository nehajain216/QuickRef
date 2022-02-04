## Basic Git commands
* To check Git Version:
> ```git version```
* Add changes to Git
>```git add <filename>```
* Add all the files to Git in one shot
> ```git add .```
* Commit files
> ```git commit -m "<commit message>"```
* Push changes to remote repository:
> ```git push```
* Pull changes from remote repository: Pull - Fetch + merge
(fetch changes to your local repository and merge changes to working directory)
> ```git pull```
* Fetch changes from remote repository : Fetch - fetch changes and does not merge
(Only fetch changes to your local repository)
> ```git fetch```

* List all the branches both remote and local:
> ```git branch -a```
* List all remote branches :
> ```git branch -r```
* List all local branches:
> ```git branch```
* Create New branch in git:
> ```git branch <branch-name>```
* Switch to branch:
> ```git checkout <branch-name>```

* To remove the origin after cloning:
> ```git remote remove origin```

* To set new project as origin after removing the existing origin:
> ```git add remote origin <git repo url>```

# CHEATSHEET - WORKFLOW GIT/GITHUB

## 🌳 HOW MANY BRANCH ?

* master
* development
* one branch by feature

## 1️⃣  GIT COMMANDS

* `git checkout -b name-of-your-feature`
* `git push --set-upstream origin name-of-your-feature`
* code...
* `git add name_of_your_modified_file` or `git add --patch` and `y` for the files you want to commit

  *__NB 1 :__ never use `git add .`*

  *__NB 2 :__ `git add --patch` doesn't take new and deleted files into account*
* `git commit -m 'title of your commit'`

  *__NB 3 :__ the title of your commit should be concise, in english, and in the present tense*

  *__NB 4 :__ commit your work regularly*
* `git checkout development`
* `git pull --prune`
* `git checkout -`
* `git merge development`
* `git push -u origin name-of-your-feature`

## 2️⃣ PULL REQUEST

* Go to your GitHub repository
* Go to **Pull requests** tab
* Click **New pull request**
* Choose **base: development** and **compare: name-of-your-feature**
* Type a title and description for your pull request
* Click **Create pull request**

  *You can request a pull request review in __Reviewers__ by clicking __Request__ next to the username of your teammate*
* Click **Merge pull request**
* Type a commit message
* Click **Confirm merge**

If your feature is finished, you can delete your branch :
* Click **Closed** to see the list of closed pull requests
* Click the pull request that's associated with the branch that you want to delete
* Click **Delete branch**

  *You can restore a deleted branch by clicking __Restore branch__*


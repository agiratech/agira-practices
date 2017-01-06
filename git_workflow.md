# Agile project workflow involving github

A simple git workflow for agile team to plan task/bugs efficiently. Its based on this [documentation](http://reinh.com/blog/2009/03/02/a-git-workflow-for-agile-teams.html)

# Rules Summary
1. [Sprint Planning - Prioritize tasks](https://github.com/agiratech/agile-git-workflow/blob/master/README.md#sprint-planning---prioritize-tasks)
2. [Pull to update your local master](https://github.com/agiratech/agile-git-workflow#pull-to-update-your-local-master)
3. [Create a feature branch](https://github.com/agiratech/agile-git-workflow#create-a-feature-branch)
4. [Do work in your feature branch](https://github.com/agiratech/agile-git-workflow#do-work-in-your-feature-branch)
5. [Keep Feature Branch up-to-date with master](https://github.com/agiratech/agile-git-workflow#keep-feature-branch-up-to-date-with-master)
6. [Complete your Feature](https://github.com/agiratech/agile-git-workflow#complete-your-feature)
7. [Create a Pull Request & Review Pull request](https://github.com/agiratech/agile-git-workflow#create-a-pull-request--review-pull-request)
8. [Delete Feature Branch](https://github.com/agiratech/agile-git-workflow#delete-feature-branch)
9. [Verify and close the feature](https://github.com/agiratech/agile-git-workflow#verify-and-close-the-feature)

The above rules makes an efficient workflow for developers in Agile teams to handles features / bugs to keep a clean and sane history.

# Sprint Planning - Prioritize Tasks
The team or Product Owner will need to decide what it is, what we are going to do and what are the priorities.

For this workflow we recommend that all work be iteratively broken down into small units which can be accomplished relatively quickly by a single team member or pair, and for this document we shall use the term Feature

The Features should be expressed in some ordered form in some system. In the simplest case this can be done directly in the GitHub Issues for this repo, although in larger projects a more flexible system should be used like Jira, redmine, etc…

When implementing a workflow for the team, always start simple. Overly complex workflows are hard to understand and very difficult to adapt. For software teams, we recommend these basic workflow states:
* **To do** - work that has not been started
* **In progress** - work that is actively being looked at by the team
* **Review** - work that is completed, but awaiting code review
* **Done** - work that is completely finished and meets the team's definition of done

In an issue tracker, these statuses flow from one to the next using transitions which structure the workflow.

However it is achieved, each Feature should have a short unique identifier (typically an integer or other code). This Feature Id should be referenced in the Commit Messages and Feature Branches

A number of Features may be grouped together into a Sprint.

# Pull to update your local master
Never work on master branch. Pull the latest updates on your local repo
```
git checkout master
git pull origin master
```
This should never create a merge commit because we are never working directly in master.Whenever you perform a pull, merge or rebase, make sure that you run tests directly afterwards. Git may not show any conflicts but that doesn’t mean that two changes are compatible. Also run tests before you commit (of course).

# Create a feature branch
Make sure you are on master. Create a Feature Branch with a well formated branch name:
* The Feature Id (or GitHub Issue Number)
* A hyphenated lowercase human readable short feature name
```
git checkout -b Dev-1-short-feature-name
```
The id (Dev-1) allows us to easily track this branch back to the feature that spawned it. The title is there to give us humans a little hint as to what it is.

#Do work in your feature branch
Do the coding and write test cases in this feature branch, committing early and often (for instance, whenever your tests pass)
```
git add .
git commit -m 'Useful commit message'
```
This Feature Id (Dev-1) should be referenced in the Commit Messages

# Keep Feature Branch up-to-date with master
As you work periodically, after you know of changes to master or when your feature is complete - Fetch the remote master and rebase your Feature Branch to those changes
```
git fetch origin master
git rebase origin/master
```
This is often done by checking master out and pulling, but this method requires extra steps:
```
git checkout master
git pull
git checkout 3275-add-commenting
git rebase master
```

# Complete your Feature
Once work on the feature(Dev-1) is complete, you will have a branch with a lot of small commits like “adding a model and a migration”, “adding a controller and some views”, “adding tests” and so on. This is useful while developing but larger, incremental commits are more easier to maintain. So we will use an interactive rebase to squash some of your commits together so that your feature can be seen as single commit in the remote repo.

* Begin the squash
```
git rebase -i origin/master
```

* Git will display an editor window with a list of the commits to be modified, something like
```
pick 3dcd585 Adding Comment model, migrations, spec
pick 9f5c362 Adding Comment controller, helper, spec
pick dcd4813 Adding Comment relationship with Post
pick 9ea48e3 Add form on Post show page
```

* Now we tell git what we to do. Change these lines to
```
pick 3dcd585 Adding Comment model, migrations, spec
squash 9f5c362 Adding Comment controller, helper, spec
squash dcd4813 Adding Comment relationship with Post
squash 9ea48e3 Add form on Post show page
```
save and quit.

* Above changes will squash these commits together into one commit and present us with a new editor window where we can give the new commit a message. We’ll use the feature id and title for the subject and list the original commit messages in the body
```
[#Dev-1] User can add a comment to a post

* Adding Comment model, migrations, spec
* Adding Comment controller, helper, spec
* Adding Comment relationship with Post
* Add form on Post show page
```

* And Push your changes feature branch
```
git push origin Dev-1-short-feature-name
```
# Create a Pull Request & Review Pull Request
On GitHub, create a pull Request to the master branch

Some one(Team Lead) need to do code review.Based on code review, we need to do the following changes

* Fixes would be made locally on the feature branch and then the updates pushed with git push origin Dev-1-short-feature-name
* This will automatically update the pull request

Again do the code review. If everything is fine, approve the pull request.

# Delete Feature Branch
Delete the feature branch(Dev-1) after pull request approval to keep the repo tidy.
* Locally using command git branch -d Dev-1-short-feature-name
* On GitHub - Using the option on the Pull Request

# Verify and close the feature
Pull the master and verify the feature
```
git checkout master
git pull origin master
```
Run the test suite on the master branch.
If problems are found then fix on a new bug Branch like Bug-1.
If everything is fine, close the issue

# Conclusion
Do the same for all features and their sprints.We follow above workflow management because it helps us meet our goals faster and exemplifies our team culture. We are experts in agile workflow management (if we do say so ourselves), and we want to help you become experts too! Enjoy Agile Workflow!!
# Git workflow
## What: Description of Git workflow
A Git Workflow is a recipe or recommendation for how to use Git to accomplish work in a consistent and productive mnner. Git workflows encourage users to leverage Git effectively and consistently. Git offers a lot of flexibility in how users manage changes. Given Git's focus on flexibility, there is no standardized process on how to interact with Git. When working with a team on a Git managed project, it's important to make sure the team is all in agreement on how the flow of changes will be applied. To ensure the team is on the same page, an agreed upon Git workflow should be developed or selected. There are several publicized Git workflows that may be a good fit for your team.
The array of possible workflows can make it hard to know where to begin when implementing Git in the workplace. Here we discuss one of the most common Git workflows for software teams to explain why we need Git workflow and how it works.
### A successful Git workflow
When evaluating a workflow for your team, it's most important that you consider your team's culture. You want the workflow to enhance the effectiveness of your team and not be a burden that limits produ
ctivity. Some things to consider when evaluating a Git workflow are:

* Does this workflow scale with team size?
* Is it easy to undo mistakes and errors with this workflow?
* Does this workflow impose any new unnecessary cognitive overhead to the team?

## Example: Centralized Workflow
![Centralized Workflow .png](https://i.loli.net/2017/10/29/59f5f1521372d.png)

The Centralized Workflow is a great Git workflow for teams transitioning from SVN. Like Subversion, the Centralized Workflow uses a central repository to serve as the single point-of-entry for all changes to the project. Instead of **trunk**,the default development branch is called **master** and all changes are committed into this branch. This workflow doesn't require any other branches besides **master**.

Transitioning to a distributed version control system may seem like a daunting task, but you don't have to change your existing workflow to take advantage of Git. Your team can develop projects in the exact same way as they do with Subversion.

### Why: Advantages of Centralized Workflow
Using Git to power your development workflow presents a few advantages over SVN. 

1.  It gives every developer their own local copy of the entire project. This isolated environment lets each developer work independently of all other changes to a project - they can add commits to their local repository and completely forget about upstream developments until it's convenient for them.

2.  It gives you access to Git's robust branching and merging model. Unlike SVN, Git branches are designed to be a fail-safe mechanism for integrating code and sharing changes between repositories. The Centralized Workflow has no defined pull request or forking patterns. A Centralized Workflow is generally better suited for teams migrating from SVN to Git and smaller size teams.

### How it works
Developers start by cloning the central repository. In their own local copies of the project, they edit files and commit changes as they would with SVN; however, these new commits are stored locally - they're completely isolated from the central repository. This lets developers defer synchronizing upstream until they're at a convenient break point.

To publish changes to the official project, developers "push" their local **master** branch to the central repository. This is the equivalent of **svn commit**, except that it adds all of the local commits that aren't already in the central **master** branch.
#### Initialize the central repository
![Initialize the central repository.png](https://i.loli.net/2017/10/29/59f5f1b3b973d.png)

First, someone needs to create the central repository on a server. If it's a new project, you can initialize an empty repository.

Otherwise, you'll need to import an existing Git or SVN repository.

Central repositories should always be bare repositories (they shouldn't have a working directory), which can be created as follows:

        ssh user@host git init --bare /path/to/repo.git

Be sure to use a valid SSH username for **user**, the domain or IP address of your server for host, and the location where you'd like to store your repo for **/path/to/repo.git**. Note that the **.git** extension is conventionally appended to the repository name to indicate that it's a bare repository.

#### Hosted central repositories
Central repositories are often created through 3rd party Git hosting services like [Bitbucket Cloud] [bc] or [Bitbucker Server] [bs] . The process of initializing a bare repository discussed above is handled for you by the hosting service. The hosting service will then provide an address for the central repository to access from your local repository.

[bs]: https://bitbucket.org/product/enterprise?_ga=2.263785996.1825450665.1509262834-2066908965.1509262834
[bc]: https://bitbucket.org/product?_ga=2.31534910.1825450665.1509262834-2066908965.1509262834  "Optional Title Here"

#### Clone the central repository
Next, each developer creates a local copy of the entire project. This is accomplished via the [git clone] [gc] command:

        git clone ssh://user@host/path/to/repo.git

When you clone a repository, Git automatically adds a shortcut called **origin** that points back to the "parent" repository, under the assumption that you'll want to interact with it further on down the road.

[gc]: https://www.atlassian.com/git/tutorials/setting-up-a-repository/git-clone

#### Make changes and commit
Once the repository is cloned locally, a developer can make changes using the standard Git commit process: edit, stage, and commit. If you're not familiar with the staging are, it's a way to prepare a commit without habing to include every change in the working directory. This lets you create highly focused commits, even if you've made a lot of local changes.

        git status # View the state of the repo
        git add <some-file> # Stage a file
        git commit # Commit a file</some-file>

Remember that since these commands create local commits, John can repeat this process as many times as he wants without worrying about what's going on in the central repository. This can be very useful for large features that need to be broken down into simpler, more atomic chunks.

#### Push new commits to central repository
Once the local repository has new changes committed. These change will need to be pushed to share with other developers on the project.

        git push origin master

This command will push the new committed changes to the central repository. When pushing changes to the central repository, it is possible that updates from another developer have been previously pushed that contain code which conflict with the intend push updates. Git will output a message indicating this conflict. In this situation, **git pull** will first need to be executed. This conflict scenario will be expanded on in the following section.

#### Managing conflicts
The central repository represents the official project, so its commit history should be treated as sacred and immutable. If a developer's local commits diverge from the central repository, Git will refuse to push their changes because this would overwrite official commits.

![Managing conflicts.png](https://i.loli.net/2017/10/29/59f5eafb6d9ba.png)

Before the developer can publish their feature, they need to fetch the updated central commits and rebase their changes on top of them. This is like saying, "I want to add my changes to what everyone else has already done." The result is a perfectly linear history, just like in traditional SVN workflows.

If local changes directly conflict with upstream commits, Git will pause the rebasing process and give you a chance to manually resolve the conflicts. The nice thing about Git is that it uses the same **git status** and **git add** commands for both generating commits and resolving merge conflicts. This makes it easy for new developers to manage their own merges. Plus, if they get themselves into trouble, Git makes it very easy to abort the entire rebase and try agian (or go find help).

## Guidelines
There is no one size fits all Git workflow. As previously stated, it's important to develop a Git workflow that is a productivity enhancement for your team. In addition to team culture, a workflow should also complement business culture. Git features like branches and tags should complement your business's release schedule. If your team is using [task tracking project management software] [ttpms] you may want to use branches that correspond with tasks in progress. In addition, some guidelines to consider when deciding on a workflow are:

### Short-lived branches
The longer a branch lives separate from the production branch, the higher the risk for merge conflicts and deployment challenges. Short-lived branches promote cleaner merges and deploys.

### Minimize and simplify reverts
It's important to have a wortflow that helps proactively prevent merges that will have to be reverted. A workflow that tests a branch before allowing it to be merged into the **master** branch is am example. However, accidents do happen. That being said, it's beneficial to have a workflow that allows for easy reverts that will not disrupt the flow for other team members.

### Match a release schedule
A workflow should complement your business's software development release cycle. If you plan to release multiple times a day, you will want to keep your **master** branch stable. If your release schedule is less frequent, you may want to consider using Git tags to tag a branch to a version.

## Summary
In this document we discussed Git workflows. We took an in-depth look at a Centralized Workflow. Some key takeaways from this document are:

1.  There is no one-size-fits-all Git workflow
2.  A workflow should be simple and enhance the productivity of your team
3.  Your business requirements should help shape your Git workflow

To read about the next Git workflow check out our comprehensive breakdown of the [Feature Branch Workflow] [fbw].

[fbw]: https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow

## Reference
[Comparing Workflows] [cw]

[cw]: https://www.atlassian.com/git/tutorials/comparing-workflows
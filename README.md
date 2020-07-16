# 13PlumbingRysGitTutorial

13 Plumbing Rys Git Tutorial

# 13. [Plumbing](https://github.com/c4arl0s/RysGitTutorial#13-plumbing-1)
 * [Examine Commit Details](https://github.com/c4arl0s/RysGitTutorial#-examine-commit-details)	
 * [Examine a tree](https://github.com/c4arl0s/RysGitTutorial#-examine-a-tree)
 * [Examine a Blob](https://github.com/c4arl0s/RysGitTutorial#-examine-a-blob)
 * [Examine a Tag](https://github.com/c4arl0s/RysGitTutorial#-examine-a-tag)
 * [Inspect Git's Branch Representation](https://github.com/c4arl0s/RysGitTutorial#-inspect-gits-branch-representation)
 * [Explore the Object Database](https://github.com/c4arl0s/RysGitTutorial#-explore-the-object-database)
 * [Collect the Garbage](https://github.com/c4arl0s/RysGitTutorial#-collect-the-garbage)
 * [Add Files to the index](https://github.com/c4arl0s/RysGitTutorial#-add-files-to-the-index)
 * [Store the Index in the Database](https://github.com/c4arl0s/RysGitTutorial#-store-the-index-in-the-database)
 * [Create a Commit Object](https://github.com/c4arl0s/RysGitTutorial#-create-a-commit-object)
 * [Update HEAD](https://github.com/c4arl0s/RysGitTutorial#-update-head)
 * [Conclusion](https://github.com/c4arl0s/RysGitTutorial#-conclusion-11)
 * [Quick Reference](https://github.com/c4arl0s/RysGitTutorial#-quick-reference-7)

# 13. [Plumbing](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)

In Rewriting History, I talked about the internal representation of a Git Repository. I may have mislead you a bit. While the reflog, interactive rebasing, end resetting may be more complex feature of Git, they are still considered part of the porcelain, as is every other command we have covered. In this module, we will take a look at Git's **plumbing** - The low level commands that give us access to Git's **true** internal representation of a project.

Unless you start hacking on Git's source code, you will probably never need to use the plumbing commands represented below. But, manually manipulating a repository will fill in the conceptual details of how Git actually stores your data, and you should walk away with a much better understanding of the techniques that we have been using throughout this tutorial. In turn, this knowledge will make the familiar porcelain commands even more powerful.

We will start by inspecting Git's object database, then we will manually create and commit a snapshot using only Git's low-level interface.

# 	* [Examine Commit Details](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)	
# 	* [Examine a tree](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Examine a Blob](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Examine a Tag](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Inspect Git's Branch Representation](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Explore the Object Database](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Collect the Garbage](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Add Files to the index](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Store the Index in the Database](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Create a Commit Object](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Update HEAD](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Conclusion](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
# 	* [Quick Reference](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)
 


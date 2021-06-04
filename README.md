# [Go back to content](https://github.com/c4arl0s/RysGitTutorial#rys-git-tutorial)

# [13 Plumbing - Content](https://github.com/c4arl0s/13PlumbingRysGitTutorial#go-back-to-content)

1. [x] [1. Examine Commit Details](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-examine-commit-details)	
2. [x] [2. Examine a tree](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-examine-a-tree)
3. [x] [3. Examine a Blob](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-examine-a-blob)
4. [x] [4. Examine a Tag](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-examine-a-tag)
5. [x] [5. Inspect Git's Branch Representation](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-inspect-gits-branch-representation)
6. [x] [6. Explore the Object Database](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-explore-the-object-database)
7. [x] [7. Collect the Garbage](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-collect-the-garbage)
8. [x] [8. Add Files to the index](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-add-files-to-the-index)
9. [x] [9. Store the Index in the Database](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-store-the-index-in-the-database)
10. [x] [10. Create a Commit Object](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-create-a-commit-object)
11. [x] [11. Update HEAD](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-update-head)
12. [x] [12. Conclusion](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-conclusion)
13. [x] [13. Quick Reference](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-quick-reference)
 
# [13 Plumbing](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

In Rewriting History, I talked about the internal representation of a Git Repository. I may have mislead you a bit. While the reflog, interactive rebasing, end resetting may be more complex feature of Git, they are still considered part of the porcelain, as is every other command we have covered. **In this module, we will take a look at Git's plumbing - The low level commands that give us access to Git's true internal representation of a project**.

Unless you start hacking on Git's source code, you will probably never need to use the plumbing commands represented below. **But, manually manipulating a repository will fill in the conceptual details of how Git actually stores your data, and you should walk away with a much better understanding of the techniques that we have been using throughout this tutorial**. In turn, this knowledge will make the familiar porcelain commands even more powerful.

We will start by inspecting Git's object database, then we will manually create and commit a snapshot using only Git's low-level interface.

# 	* [Examine Commit Details](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)	

First, lets take a closer look at our latest commit with the git cat-file plumbing command

```console
Fri Jul 17 ~/iOS/RysGitTutorialRepository 
$ git cat-file commit HEAD
tree c5ba33c4fdb6d0585fcb6f778b876daa30253067
parent 56599780e162975562da46742664c1132a24b78e
author c4arl0s <c.santiago.cruz@icloud.com> 1594256023 -0500
committer c4arl0s <c.santiago.cruz@icloud.com> 1594256023 -0500

Add .gitignore file
```

**The commit parameter tells Git that we want to see a commit object, and as we already know, HEAD refers to the most recent commit**. This will output the following, although your IDs and user information will be different.

**This is the complete representation of a commit: a tree, a parent, user data, and a commit message**. The user information and commit message are relatively straightforward, but we have never seen the tree or parent values before.

**A tree object is Git's representation of the snapshot we have been talking about since the beginning of this tutorial**. They record the state o a directory at a given point, without any notion of time or author. To tie trees together into a coherent project history, Git wraps each one in a commit object and specifies a parent, which is just another commit. **By following the parent of each commit, you can walk through the entire history of a project**.

![Screen Shot 2020-07-17 at 8 05 48](https://user-images.githubusercontent.com/24994818/87789244-5897f700-c804-11ea-86f6-a23fe65b4101.png)

Notice that each commit refers to one and only one tree objects. **Form the git cat-file output, we can also infer that trees use SHA-1 checksums for their ID's**. This will be the case for all of Git's internal objects.

# 	* [Examine a tree](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

Next, let`s try to inspect a tree using the same git cat-file command. Make sure to change. 

```console
Fri Jul 17 ~/iOS/RysGitTutorialRepository 
$ git cat-file tree c5ba33c4fdb6d0585fcb6f778b876daa30253067
CZ?G?<L?Bf`I40000 about??\??n"???*c??S?100644 blue.html7c?#>]Z!?K?.ij??O100644 green.html?.,?
                                                                                            ?i??r??9A6Ud?100644 index.html??i$9??nA?D?c??100644 news-1.htmlv?*??+qeH?W???6_ܭ100644 news-2.html???
       G?~??&H??$ؤ?100644 news-3.htmlE
                                      33P??BL?-i?Utz?ϭ100644 orange.html??1?z?>? ?alX???100644 pink.htmlC????:?\?.]??PP?+
                            ?100644 rainbow.html?S;1M5?8?A:|?߱?4?100644 red.htmlp???e?D?(y????100644 yellow.html??x?I?A?C??J)51?8?
```

**Unfortunately, treess contain binary data, which is quite ugly when displayed in its raw form**. So, Git offers another useful plumbing command:

```console
Fri Jul 17 ~/iOS/RysGitTutorialRepository 
$ git ls-tree c5ba33c4fdb6d0585fcb6f778b876daa30253067
100644 blob 99ed0d431c5a19f147da3c4cb8421b5566600449	.gitignore
040000 tree e0b8e25c9fb46e22f593df2a63a9e0a60053c07f	about
100644 blob 370563f1b719233e5d5a21914bb82e696af7cb4f	blue.html
100644 blob 822e2cb2140bba69f4bd72fac43941360f5564f6	green.html
100644 blob 0193c1691e2439829e6e41b944e41763def10010	index.html
100644 blob 76db2ad3de2b0071654800cc57d5f3ee365fdcad	news-1.html
100644 blob 1ddbdae80b47e69d7ee5fd2648d110cd24d8a4be	news-2.html
100644 blob 450c33335098bd424c842d69fe55747a8211cfad	news-3.html
100644 blob ebe83105d77abe3ed420eea5616cc50858b38bbe	orange.html
100644 blob 431492b9a3943abe5cd02e5db1bf5050ee2b0bfd	pink.html
100644 blob a8533b314d359238f099413a7caddfb1cf1834ee	rainbow.html
100644 blob 70ce141cd1e36585441e1f8e2879f7ade6fa87fa	red.html
100644 blob 18bfc77ac5d8cd0da8e478cb3e6fdfb1e76a9c0d	style.css
100644 blob e9d1781fd949fd41d2439ae3824a293531bc38a5	yellow.html
```

This will output the contents of the tree, which looks an awful lot like a directory listing.

**By examining the above output, we can presume that "blobs" represent files in our repository, whereas trees represent folders**. Go ahead and examine the about tree with another git ls-tree to see if this really is the case. You should see the contents of our about folder.

So, blob objects are how Git stores our file data, tree objects combine blobs and other trees into a directory listing, then commit objects tie trees into a project history. These are only types of objects that git needs to implement nearly all the porcelain commands we have been using, and their relationship is summed up as follows:

![Screen Shot 2020-07-17 at 8 17 10](https://user-images.githubusercontent.com/24994818/87790269-ecb68e00-c805-11ea-92a9-50568665b06a.png)

# 	* [Examine a Blob](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

Let's take a look at the blob associated with blue.html (be sure to change the following to the ID next to blue.html in your tree output.

```console
Sat Jul 18 ~/iOS/RysGitTutorialRepository 
$ git cat-file blob 370563f1b719233e5d5a21914bb82e696af7cb4f
<!DOCTYPE html>
<html lang="en">
<head>
  <title>The Blue Page</title>
  <link rel="stylesheet" href="style.css" />
  <meta charset="utf-8" />
</head>
<body>
  <h1 style="color: #00F">The Blue Page</h1>
  <p>Blue is the color of the sky.</p>
</body>
</html>
```

**This should display the entire contents of blue.html, confirming that blobs really are plain data files**. Note that blobs are pure content: there is no mention of a file in the above output. That is to say, the name blue.html is stored in the tree that contains the blob, not the blob itself.

You may recall from [The Basics](https://github.com/c4arl0s/2TheBasicsRysGitTutorial#2-the-basics---content) that an SHA-1 checksum ensures an object's contents is never corrupted without Git knowing about it. **Checksums work by using the object's contents to generate a unique character sequence**. This not only functions as an identifier, it also guarantees that an object will not be silently corrupted (the altered content would generate different ID).

When it comes to blob objects, this has an additional benefit. **Since two blobs with the same data will have the same ID, Git must share blobs across multiple trees**. 

> For example, our blue.html file has not been changed since it was created, so our repository will only have a single associated blob, and all subsequent trees will refer to it. By not creating duplicate blobs for each tree object, Git vastly reduces the size of a repository. With this in mind, we can revise our Git object diagram to the following.

![Screen Shot 2020-07-18 at 16 49 14](https://user-images.githubusercontent.com/24994818/87862517-a43ac580-c916-11ea-81b8-12965cd7b874.png)

However, as soon as you change a single line in a file, Git must create a new blob object because its contents will have changed, resulting in a new SHA-1 checksum.

# 	* [Examine a Tag](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

The fourth and final type of Git object is the tag object. We can use the same git cat-file command to show the details of a tag.

```console
Sun Jul 19 ~/iOS/RysGitTutorialRepository 
$ git cat-file tag v2.0
object 49baa6e81a7ad87714540ec9ce9fceaaa12409c1
type commit
tag v2.0
tagger c4arl0s <c.santiago.cruz@gmail.com> 1591810941 -0500

An even stabler version of the website
```

This will output the commit ID associated with v2.0 along with the tag's name, author, creation date, and message. **The straightforward relationship between tags and commits gives us our finalized Git object diagram**.

![Screen Shot 2020-07-19 at 12 53 00](https://user-images.githubusercontent.com/24994818/87881429-d43da380-c9be-11ea-81c1-7c840b66b37f.png)


# 	* [Inspect Git's Branch Representation](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

We now have the tools to fully explore Git's branch representation. Using the -t flag, we can determine what kind of object Git uses for branches.

```console
Mon Jul 20 ~/iOS/RysGitTutorialRepository 
$ git cat-file -t master
commit
```

That's right, a branch is just a reference to a commit object, which means we can view it with a normal git cat-file.

```console
Mon Jul 20 ~/iOS/RysGitTutorialRepository 
$ git cat-file commit master
tree c5ba33c4fdb6d0585fcb6f778b876daa30253067
parent 56599780e162975562da46742664c1132a24b78e
author c4arl0s <c.santiago.cruz@icloud.com> 1594256023 -0500
committer c4arl0s <c.santiago.cruz@icloud.com> 1594256023 -0500

Add .gitignore file
```

This will output the exact same information as our original git cat-file commit HEAD. **It seems that both the master branch and HEAD are simply references to a commit object**.

Using a text editor, open up the .git/refs/heads/master file. You should find the commit checksum of the most recent commit, which you can view with git log -n 1.

```console
Mon Jul 20 ~/iOS/RysGitTutorialRepository 
$ cat .git/refs/heads/master 
da3867ee04234207430527875d5180cab83eb144
```

```console
Mon Jul 20 ~/iOS/RysGitTutorialRepository 
$ git log -n 1
commit da3867ee04234207430527875d5180cab83eb144 (HEAD -> test, master)
Author: c4arl0s <c.santiago.cruz@icloud.com>
Date:   Wed Jul 8 19:53:43 2020 -0500

    Add .gitignore file
```

**This single file is all Git needs to maintain the master branch - all other information is extrapolated through the commit object relationships discussed above**.

The HEAD reference, on the other hand, is recorded in .git/HEAD. Unlike the branch tips, HEAD is not a direct link to a commit. Instead, it refers to a branch, which Git uses to figure out which commit is currently checked out. Remember that detached HEAD state ocurred when HEAD did not coincide with the tip of any branch. **Internally, all this means to Git is that .git/HEAD does not contain a local branch**. Try checking out an old commit:

```console
on Jul 20 ~/iOS/RysGitTutorialRepository 
$ git checkout HEAD~1
Note: switching to 'HEAD~1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 5659978 Add a pink block of color
```

Now, .git/HEAD should contain a commit ID instead of a branch. This tells Git that we are in a detached HEAD state. Regardless of what state you are in, the git checkout command will always record the checked-out reference in .git/HEAD. 

Let's get back to our master branch before moving on:

```console
Mon Jul 20 ~/iOS/RysGitTutorialRepository 
$ git checkout master
Previous HEAD position was 5659978 Add a pink block of color
Switched to branch 'master'
```

# 	* [Explore the Object Database](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

While we have a basic understanding of Git's object interaction, we have yet to explore were Git keeps all of these objects. In your RysGitTutorialRepository, open the directory .git/objects. This is Git's object database.

**Each object, regardless of type, is stored as a file, using its SHA-1 checksum as the filename (sort of**). But, instead of sorting all objects in a single directory, they are split up using the first two characters of their ID as a directory name, resulting in an object database that looks something like the following.

```console
Tue Jul 21 ~/iOS/RysGitTutorialRepository 
$ ls .git/objects/
pack 3f   d5   e1   17   a7   a6   b9   e9   16   5d   32   37   18   95   3c   99   01
info 0a   1b   27   4e   7d   3d   1e   70   f9   47   fb   d4   d6   c3   74   29   14
26   06   c9   1a   2d   f4   f7   81   04   b6   3b   78   d9   40   43   72   a8
c1   a2   d2   ce   20   de   35   71   eb   af   2f   1d   45   ca   69   50   3e
bc   08   4a   e0   8d   6a   5a   d8   2b   91   a0   cc   36   c0   61   c5   bf
e6   cb   10   49   5f   97   12   82   85   76   51   9b   6f   56   57   da   ed
```

For example, an object with the following ID:

```console
7a52bb857229f89bffa74134ee3de48e5e146105
```
is stored in a folder called 7a, using the remaining characters (52bb8...) as a filename. This gives us an object ID, but before we can inspect items in the object database, we need to know what type of object it is. Again, we can use the -t flag 

```console
git cat-file -t 7a52bb8
```

My object was a blob, but yours may be different. If it is a tree, remember to use git ls-tree to turn that ugly binary data into a pretty directory listing.


# 	* [Collect the Garbage](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

**As your repository grows, Git may automatically transfer your object files into a more compact form know as a "pack" file**. You can force this compression with the garbage collection command, but beware: this command is undo-able. If you want to continue exploring the contents of the .git/objects folder, you should do so before running the following command. Normal Git functionality will not be affected. 

```console
Wed Jul 22 ~/iOS/RysGitTutorialRepository 
$ git gc
Enumerating objects: 106, done.
Counting objects: 100% (106/106), done.
Delta compression using up to 4 threads
Compressing objects: 100% (101/101), done.
Writing objects: 100% (106/106), done.
Total 106 (delta 47), reused 0 (delta 0)
```

This compress individual object files into a faster, smaller pack file and removes dangling commits (e.g. from a deleted, unmerge branch).

Of course, all of the same object ID's will still work with git cat-file, and all of the porcelain commands will remain unaffected. The git gc command only changes Git's storage mechanism- not the contents of a repository. Running git gc every now and then is usually a good idea, as it keeps your repository optimized.

# 	* [Add Files to the index](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

Thus far, we have been discussing Git's low-level representation of committed snapshots. **The rest of this module will shift gears and use more "plumbim" commands to manually prepare and commit a new snapshot**. This will give us an idea of how Git manages the working directory and the staging area.

Create a new file called news-4.html in RysGitTutorialRepositor and add the following HTML.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Indigo Invasion</title>
  <link rel="stylesheet" href="style.css" />
  <meta charset="utf-8" />
</head>
<body>
  <h1 style="color: #A0C">Indigo Invasion</h1>
  <p>Last week, a coalition of Asian designers, artists,
  and advertisers announced the official color of Asia:
  <span style="color: #A0C">Indigo</span>.</p>
    
  <p><a href="index.html">Return to home page</a></p>
</body>
</html>
```

Then, update the index.html "News" section o match the following.

```html
<h2 style="color: #C00">News</h2>
<ul>
  <li><a href="news-1.html">Blue Is The New Hue</a></li>
  <li><a href="rainbow.html">Our New Rainbow</a></li>
  <li><a href="news-2.html">A Red Rebellion</a></li>
  <li><a href="news-3.html">Middle East's Silent Beast</a></li>
  <li><a href="news-4.html">Indigo Invasion</a></li>
</ul>”
```

Instead of git add, we will use the low-level git update-index command to add files to the staging area. The index is Git's term for the staged snapshot.

```console
Thu Jul 23 ~/iOS/RysGitTutorialRepository 
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   index.html

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	news-4.html

no changes added to commit (use "git add" and/or "git commit -a")
```

```console
Thu Jul 23 ~/iOS/RysGitTutorialRepository 
$ git update-index index.html
```

```console
Thu Jul 23 ~/iOS/RysGitTutorialRepository 
$ git update-index news-4.html 
error: news-4.html: cannot add to the index - missing --add option?
fatal: Unable to process path news-4.html
```

The last command will throw an error - Git won't let you add a new file to the index without explicitly stating that it is a new file:

```console
Thu Jul 23 ~/iOS/RysGitTutorialRepository 
$ git update-index --add news-4.html 
```

We have just moved the working directory into the index, which means we have a snapshot prepared for committal. However, the process will not be quite as simple as a mere git commit. 

# 	* [Store the Index in the Database](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

Remember that all Commits refer to a tree object, which represents the snapshot for that commit. **So, before creating a commit object, we need to add our index (the staged tree) to Git's object database**. We can do this with the following command.

```console
Fri Jul 24 ~/iOS/RysGitTutorialRepository 
$ git write-tree
```

This command creates a tree object from the index and stores it in .git/objects. It will output the ID of the resulting tree (yours may be different):

```console
Fri Jul 24 ~/iOS/RysGitTutorialRepository 
$ git write-tree
0caa26d27ad464ac2dc6e90741cc5d06020def9e
```

You can examine your new snapshot with git ls-tree. Keep in mind that the only new blobs created for this commit were index.html and news-4.html. The rest of the three contains references to existing blobs.

```console
Fri Jul 24 ~/iOS/RysGitTutorialRepository 
$ git ls-tree 0caa26d
100644 blob 99ed0d431c5a19f147da3c4cb8421b5566600449	.gitignore
040000 tree e0b8e25c9fb46e22f593df2a63a9e0a60053c07f	about
100644 blob 370563f1b719233e5d5a21914bb82e696af7cb4f	blue.html
100644 blob 822e2cb2140bba69f4bd72fac43941360f5564f6	green.html
100644 blob d5411e19a037ba8e4bda22fc2eca0989a5df7899	index.html
100644 blob 76db2ad3de2b0071654800cc57d5f3ee365fdcad	news-1.html
100644 blob 1ddbdae80b47e69d7ee5fd2648d110cd24d8a4be	news-2.html
100644 blob 450c33335098bd424c842d69fe55747a8211cfad	news-3.html
100644 blob 64887ccefbf12890e4a1fa91808108e1fff6237b	news-4.html
100644 blob ebe83105d77abe3ed420eea5616cc50858b38bbe	orange.html
100644 blob 431492b9a3943abe5cd02e5db1bf5050ee2b0bfd	pink.html
100644 blob a8533b314d359238f099413a7caddfb1cf1834ee	rainbow.html
100644 blob 70ce141cd1e36585441e1f8e2879f7ade6fa87fa	red.html
100644 blob 18bfc77ac5d8cd0da8e478cb3e6fdfb1e76a9c0d	style.css
100644 blob e9d1781fd949fd41d2439ae3824a293531bc38a5	yellow.html
```

**So, we have our tree object, but we have yet to add it to the project history**. -->

# 	* [Create a Commit Object](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

To commit the new tree object, we need to manually figure out the ID of the parent commit.

```console
Fri Jul 24 ~/iOS/RysGitTutorialRepository 
$ git log --oneline -n 1
```

This will output the following line, though your commit ID will be different. We will use this ID to specify the parent of our new commit object.

```console
da3867e (HEAD -> master, test) Add .gitignore file
```

**The git commit-tree command creates a commit object from a tree and a parent ID, while the author information is taken from an environment variable set by Git**. Make sure to change 0caa26d to your tree ID, and da3867e to your most recent commit ID.

```console
Fri Jul 24 ~/iOS/RysGitTutorialRepository 
$ git commit-tree 0caa26d -p da3867e
Add 4tth news item
99a52ee48ef0f382e1af77fa58a12d6cdcc1b093
```

**This command will wait for more input: the commit message**. Type Add 4th news item and press Enter to create the commit message, then Ctrl-Z and Enter for Windows or Ctrl-D for Unix to specify and "End-of-file" character to end the input. Like the git write-tree command, this will output the ID of the resulting commit object.

```console
99a52ee48ef0f382e1af77fa58a12d6cdcc1b093
```

**You will now be able to find this commit in .git/objects, but neither HEAD nor the branches have been updated to include this commit, It is a dangling commit at this point**. Fortunately for us, we know where Git stores its branch information.

![Screen Shot 2020-07-24 at 16 06 40](https://user-images.githubusercontent.com/24994818/88435380-b1006300-cdc7-11ea-83b7-0d14275d8862.png)

# 	* [Update HEAD](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

Since we are not in a detached HEAD state, HEAD is a reference to a branch. So, all we need to do to update HEAD is move the master branch forward to our new commit object. **Using a txt editor, replace the contents of git/refers/heads/master with the output from git commit-tree in the previous step**.

In this example there is no master file.

```console
Fri Jul 24 ~/iOS/RysGitTutorialRepository 
$ cat .git/refers/head/master
cat: .git/refers/head/master: No such file or directory
```

If this file seems to have disappeared, don't fret! This just means that the git gc command packed up all of our branch references into single file. 

Instead of .git/refers/heads/master, open up .git/packed-refs, find the line with refs/heads/master, and change the ID to the left of it.

```console
Fri Jul 24 ~/iOS/RysGitTutorialRepository 
$ cat .git/packed-refs 
# pack-refs with: peeled fully-peeled sorted 
da3867ee04234207430527875d5180cab83eb144 refs/heads/master
da3867ee04234207430527875d5180cab83eb144 refs/heads/test
a002c653723d7683a8a039b660e1818336a2df59 refs/remotes/john/master
51f9d9e84c2e10e5ac859f2c8a6c4ac66ed39b17 refs/remotes/john/pink-page
56599780e162975562da46742664c1132a24b78e refs/remotes/origin/master
bfa55aa42e51e450336c12f91897d6c12a88f5b5 refs/stash
61cb509a5d2d9a771034370cace5c807327a84c6 refs/tags/v1.0
^453c8a4db079c3d235e3470754a79a22ea0f0afd
3da40702e43f277667e5816f14aebfb8bc4ffd1e refs/tags/v2.0
^49baa6e81a7ad87714540ec9ce9fceaaa12409c1
```

```console
Fri Jul 24 ~/iOS/RysGitTutorialRepository 
$ vim .git/packed-refs 
```

```console
Fri Jul 24 ~/iOS/RysGitTutorialRepository 
$ cat .git/packed-refs 
# pack-refs with: peeled fully-peeled sorted 
99a52ee48ef0f382e1af77fa58a12d6cdcc1b093 refs/heads/master
da3867ee04234207430527875d5180cab83eb144 refs/heads/test
a002c653723d7683a8a039b660e1818336a2df59 refs/remotes/john/master
51f9d9e84c2e10e5ac859f2c8a6c4ac66ed39b17 refs/remotes/john/pink-page
56599780e162975562da46742664c1132a24b78e refs/remotes/origin/master
bfa55aa42e51e450336c12f91897d6c12a88f5b5 refs/stash
61cb509a5d2d9a771034370cace5c807327a84c6 refs/tags/v1.0
^453c8a4db079c3d235e3470754a79a22ea0f0afd
3da40702e43f277667e5816f14aebfb8bc4ffd1e refs/tags/v2.0
^49baa6e81a7ad87714540ec9ce9fceaaa12409c1
```

**Now that our master branch points to the new commit, we should b able to see the news-4.html file in the project history**.

```console
Fri Jul 24 ~/iOS/RysGitTutorialRepository 
$ git log -n 2
commit 99a52ee48ef0f382e1af77fa58a12d6cdcc1b093 (HEAD -> master)
Author: c4arl0s <c.santiago.cruz@icloud.com>
Date:   Fri Jul 24 16:04:26 2020 -0500

    Add 4tth news item

commit da3867ee04234207430527875d5180cab83eb144 (test)
Author: c4arl0s <c.santiago.cruz@icloud.com>
Date:   Wed Jul 8 19:53:43 2020 -0500

    Add .gitignore file
```

![Screen Shot 2020-07-24 at 16 27 24](https://user-images.githubusercontent.com/24994818/88436799-b1e6c400-cdca-11ea-88bf-c50a8b2d3836.png)

**The last four sections explain everything that happens behind the scenes when we execute git commit -a -m "Some Message**". Aren't you glad you won't have to use Git's Plumbing ever again ?

![Screen Shot 2020-07-24 at 16 31 36](https://user-images.githubusercontent.com/24994818/88437010-2883c180-cdcb-11ea-9781-bdbf25756b2f.png)

# 	* [Conclusion](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)

After this module, you hopefully have a solid grasp of the object database that underlies almost every Git command. We examined commits, trees, blobs, tags, and branches, and we even created a commit object from scratch. All of this was meant to give you a deeper understanding of Git's porcelain commands, and you should now feel ready to adapt Git to virtually any task you could possibly demand from a version control system.

As you migrate these skills to real-world projects, remember that git is merely a tool for tracking your files, not a cure-all for managing software projects. No amount of intimate Git knowledge can make up a haphazard set of conventions within a development team.

Thus includes our journey through Git-based revision control. This tutorial was meant to prepare you for the realities of distributed software development - not to transform you into a Git expert overnight. You should be able to manage your own projects, collaborate with other Git users, and, perhaps most importantly, understand exactly what any other piece of Git documentation is trying to convey.

Your job now is to take these skills and apply them to new projects, sift through complex histories that you have never seen before, talk to other developers about their Git workflows, and take the time to actually try all of those "I wonder what would have happen if..." scenarios. Good Luck!!

For questions, comments, or suggestions, please contact us.

# 	* [Quick Reference](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing---content)
 
```console
$ git cat-file type objectID
```
Display the specified object, where type is one of commit, tree, blob, or tag.

```console
$ git cat-file -t objectID
```
Output the type of the specified object.

```console
$ git ls-tree treeID
```
Display a pretty version of the specified tree objects.


```console
git gc
```
Perform a garbage collection on the object database.

```console
git update-index --add file
```
Stage the specified file, using the optional --add flag to denote a new untracked file.

```console
git write-tree
```
Generate a tree from the index and store it in the object data-base. Returns the ID of the new tree object.

```console
git commit-tree treeID -p parentID
```
Create a New commit object from the given tree object and parent commit. Returns the ID of the new commit object.



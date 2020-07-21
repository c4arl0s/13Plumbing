# [Go back to content](https://github.com/c4arl0s/RysGitTutorial#rysgittutorial)

13 Plumbing Rys Git Tutorial

# 13. [Plumbing - Content]()
 * [13. Plumbing - Intro]()
 * [Examine Commit Details](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-examine-commit-details)	
 * [Examine a tree](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-examine-a-tree)
 * [Examine a Blob](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-examine-a-blob)
 * [Examine a Tag](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-examine-a-tag)
 * [Inspect Git's Branch Representation](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-inspect-gits-branch-representation)
 * [Explore the Object Database](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-explore-the-object-database)
 * [Collect the Garbage](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-collect-the-garbage)
 * [Add Files to the index](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-add-files-to-the-index)
 * [Store the Index in the Database](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-store-the-index-in-the-database)
 * [Create a Commit Object](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-create-a-commit-object)
 * [Update HEAD](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-update-head)
 * [Conclusion](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-conclusion)
 * [Quick Reference](https://github.com/c4arl0s/13PlumbingRysGitTutorial#-quick-reference)

# 13. [Plumbing](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)

In Rewriting History, I talked about the internal representation of a Git Repository. I may have mislead you a bit. While the **reflog**, interactive rebasing, end resetting may be more complex feature of Git, they are still considered part of the porcelain, as is every other command we have covered. In this module, we will take a look at Git's **plumbing** - The low level commands that give us access to Git's **true** internal representation of a project.

Unless you start hacking on Git's source code, you will probably never need to use the plumbing commands represented below. But, manually manipulating a repository will fill in the conceptual details of how Git actually stores your data, and you should walk away with a much better understanding of the techniques that we have been using throughout this tutorial. In turn, this knowledge will make the familiar porcelain commands even more powerful.

We will start by inspecting Git's object database, then we will manually create and commit a snapshot using only Git's low-level interface.

# 	* [Examine Commit Details](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)	

First, lets take a closer look at our latest commit with the **git cat-file** plumbing command

```console
Fri Jul 17 ~/iOS/RysGitTutorialRepository 
$ git cat-file commit HEAD
tree c5ba33c4fdb6d0585fcb6f778b876daa30253067
parent 56599780e162975562da46742664c1132a24b78e
author c4arl0s <c.santiago.cruz@icloud.com> 1594256023 -0500
committer c4arl0s <c.santiago.cruz@icloud.com> 1594256023 -0500

Add .gitignore file
```

The **commit** parameter tells Git that we want to see a commit object, and as we already know, HEAD refers to the most recent commit. This will output the following, although your IDs and user information will be different.

This is the complete representation of a commit: **a tree, a parent, user data, and a commit message. The user information and commit message are relatively straightforward, but we have never seen the **tree** or **parent** values before.

A **tree object** is Git's representation of the **snapshot** we have been talking about since the beginning of this tutorial. They record the state o a directory at a given point, without any notion of time or author. To tie trees together into a coherent project history, Git wraps each one in a commit object and specifies a **parent**, which is just another commit. By following the parent of each commit, you can walk through the entire histor of a project.

![Screen Shot 2020-07-17 at 8 05 48](https://user-images.githubusercontent.com/24994818/87789244-5897f700-c804-11ea-86f6-a23fe65b4101.png)

Notice that each commit refers to one and only one tree objects. Form the **git cat-file** output, we can also infer that trees use SHA-1 chechsums for their ID's. This will be the case for all of Git's internal objects.

# 	* [Examine a tree](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)

Next, let`s try to inspect a tree using the same **git cat-file** command. Make sure to change 

```console
Fri Jul 17 ~/iOS/RysGitTutorialRepository 
$ git cat-file tree c5ba33c4fdb6d0585fcb6f778b876daa30253067
CZ?G?<L?Bf`I40000 about??\??n"???*c??S?100644 blue.html7c?#>]Z!?K?.ij??O100644 green.html?.,?
                                                                                            ?i??r??9A6Ud?100644 index.html??i$9??nA?D?c??100644 news-1.htmlv?*??+qeH?W???6_ܭ100644 news-2.html???
       G?~??&H??$ؤ?100644 news-3.htmlE
                                      33P??BL?-i?Utz?ϭ100644 orange.html??1?z?>? ?alX???100644 pink.htmlC????:?\?.]??PP?+
                            ?100644 rainbow.html?S;1M5?8?A:|?߱?4?100644 red.htmlp???e?D?(y????100644 yellow.html??x?I?A?C??J)51?8?
```

Unfortunately, treess contain binary data, which is quite ugly when displayed in its raw form. So, Git offers another useful plumbing command:

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

By examining the above output, we can presume that **"blobs" represent files in our repository, whereas trees represent folders. Go ahead and examine the **about** tree with another **git ls-tree** to see if this really is the case. You should see the contents of our **about** folder.

So, **blob objects** are how Git stores our file data, tree objects combine blobs and other trees into a directory listing, then commit objects tie trees into a project history. These are only types of objects that git needs to implement nearly all the porcelain commands we have been using, and their relationship is summed up as follows:

![Screen Shot 2020-07-17 at 8 17 10](https://user-images.githubusercontent.com/24994818/87790269-ecb68e00-c805-11ea-92a9-50568665b06a.png)

# 	* [Examine a Blob](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)

Let's take a look at the blob associated with **blue.html** (be sure to change the following to the ID next to **blue.html** in **your** tree output.

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

This should display the entire contents of **blue.html**, confirming that blobs really are plain data files. Note that blobs are pure content: **there is no mention of a file in the above output.** That is to say, the name **blue.html** is stored in the **tree that contains the blob, not the blob itself.

You may recall from [The Basics]() that an SHA-1 checksum ensures an object's contents is never corrupted without Git knowing about it. Checksums work by using the object's contents to generate a unique character sequence. This not only functions as an identifier, it also guarantees that an object will not be silently corrupted (the altered content would generate different ID)

When it comes to blob objects, this has an additional benefit. Since two blobs with the same data will have the same ID, Git **must** share blobs across multiple trees. For example, our **blue.html** file has not been changed since it was created, so our repository will only have a single associated blob, and all subsequent trees will refer to it. By not creating duplicate blobs for each tree object, Git vastly reduces the size of a repository. With this in mind, we can revise our Git object diagram to the following.

![Screen Shot 2020-07-18 at 16 49 14](https://user-images.githubusercontent.com/24994818/87862517-a43ac580-c916-11ea-81b8-12965cd7b874.png)

However, as soon as you change a single line in a file, Git must create a new blob object because its contents will have changed, resulting in a new SHA-1 checksum.

# 	* [Examine a Tag](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)

The fourth and final type of Git object is the **tag object**. We can use the same **git cat-file** command to show the details of a tag.

```console
Sun Jul 19 ~/iOS/RysGitTutorialRepository 
$ git cat-file tag v2.0
object 49baa6e81a7ad87714540ec9ce9fceaaa12409c1
type commit
tag v2.0
tagger c4arl0s <c.santiago.cruz@gmail.com> 1591810941 -0500

An even stabler version of the website
```

This will output the commit ID associated with v2.0 along with the tag's name, author, creation date, and message. The straightforward relationship between tags and commits gives us our finalized Git object diagram.

![Screen Shot 2020-07-19 at 12 53 00](https://user-images.githubusercontent.com/24994818/87881429-d43da380-c9be-11ea-81c1-7c840b66b37f.png)


# 	* [Inspect Git's Branch Representation](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)

We now have the tools to fully explore Git's branch representation. Using the **-t flag**, we can determine what kind of object Git uses for branches.

```console
Mon Jul 20 ~/iOS/RysGitTutorialRepository 
$ git cat-file -t master
commit
```

That's right, a branch is just a reference to a commit object, which means we can view it with a normal **git cat-file**

```console
Mon Jul 20 ~/iOS/RysGitTutorialRepository 
$ git cat-file commit master
tree c5ba33c4fdb6d0585fcb6f778b876daa30253067
parent 56599780e162975562da46742664c1132a24b78e
author c4arl0s <c.santiago.cruz@icloud.com> 1594256023 -0500
committer c4arl0s <c.santiago.cruz@icloud.com> 1594256023 -0500

Add .gitignore file
```

This will output the exact same information as our original **git cat-file commit HEAD**. It seems that both the **master branch** and **HEAD** are simply references to a commit object.

Using a text editor, open up the **.git/refs/heads/master** file. You should find the commit checksum of the most recent commit, which you can view with **git log -n 1**.

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

This single file is all Git needs to maintain the **master** branch - all other information is extrapolated through the commit object relationships discussed above.

The **HEAD** reference, on the other hand, is recorded in **.git/HEAD**. Unlike the branch tips, **HEAD** is not a direct link to a commit. Instead, it refers to a branch, which Git uses to figure out which commit is currently checked out. Remember that **detached HEAD** state ocurred when **HEAD** did not coincide with the tip of any branch. Internally, all this means to Git is that **.git/HEAD** does not contain a local branch. Try checking out an old commit:

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

Now, **.git/HEAD** should contain a commit ID instead of a branch. This tells Git that we are in a detached HEAD state. Regardless of what state you are in, the **git checkout** comman will always record the checked-out reference in **.git/HEAD**. 

Let's get back to our **master** branch before moving on:

```console
Mon Jul 20 ~/iOS/RysGitTutorialRepository 
$ git checkout master
Previous HEAD position was 5659978 Add a pink block of color
Switched to branch 'master'
```

# 	* [Explore the Object Database](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)

While we have a basic understanding of Git's object interaction, we have yet to explore were Git keeps all of these objects. In your **RysGitTutorialRepository**, open the directory **.git/objects**. This is Git's object database.

Each object, regardless of type, is stored as a file, using its **SHA-1** **checksum** as the filename (sort of). But, instead of sorting all objects in a single directory, they are split up using the first two characters of their ID as a directory name, resulting in an object database that looks something like the following.

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
is stored in a folder called **7a**, using the remaining characters (52bb8...) as a filename. This gives us an object ID, but before we can inspect items in the object database, we need to know what type of object it is. Again, we can use the **-t flag** 

```console
git cat-file -t 7a52bb8
```

My object was a blob, but yours may be different. If it is a tree, remember to use **git ls-tree** to turn that ugly binary data into a pretty directory listing.


# 	* [Collect the Garbage](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)
# 	* [Add Files to the index](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)
# 	* [Store the Index in the Database](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)
# 	* [Create a Commit Object](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)
# 	* [Update HEAD](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)
# 	* [Conclusion](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)
# 	* [Quick Reference](https://github.com/c4arl0s/13PlumbingRysGitTutorial#13-plumbing)
 


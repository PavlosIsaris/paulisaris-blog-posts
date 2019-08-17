---
title: "Useful commands to check directories and files size"
date: "2018-06-26"
tags: [linux,productivity]
image: img/posts/directories_size.jpg
---

Often times, there is a need to check the size of directories or files in a project, or to evaluate the space a directory occupies in a live/development server, or just at our local machine.

Here is a list of useful commands that you can leverage to make sure the available disk space of your machine is normal and that the huge `node_modules` directory size of your project hasn't gone crazy :D 

### We will use the [du command](https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/), which is explained below:

```
du (disc usage) command estimates file_path space usage

The options -sh are (from man du):

  -s, --summarize
         display only a total for each argument

  -h, --human-readable
         print sizes in human readable format (e.g., 1K 234M 2G)
```

To check more than one directory and see the total, use du -sch:
```
  -c, --total
         produce a grand total
```

### So, let's see the basic commands:


Shows you the size of the directory in readable format:
```
$ du -sh directory/
```

Shows you a list with all the directories along with their sizes, ordered by size:

```
$ du -sh * | sort -h
```

Example in my example flutter project:
```
paul@paul-Inspiron-N5110:~/projects/flutter_test$ du -sh * | sort -h
4,0K	android.iml
4,0K	flutter_test_android.iml
4,0K	flutter_test.iml
4,0K	pubspec.yaml
4,0K	README.md
8,0K	test
12K	lib
200K	android
212K	ios

```
Also, if you want to include hidden files and directories, you can run:

```
$ du -sch .[^.]* * |sort -h
```

### Bonus:

Outputs the total and available size of your system's paritions
```
$ df -h
```

Outputs the [inodes](https://en.wikipedia.org/wiki/Inode) usage in your system:
```
$ df -i
```

If you have more useful commands to share, leave them in the comments below!
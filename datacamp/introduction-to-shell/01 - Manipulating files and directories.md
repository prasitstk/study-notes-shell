# 01 - Manipulating files and directories

## Where am I?

To find out where you are in the filesystem, run the command `pwd` (short for "print working directory").

This prints the absolute path of your current working directory, which is where the shell runs commands and looks for files by default.

---

## How can I identify files and directories?

To find out what's there in a directory, type `ls /path/to/dir` (which is short for "listing") and press the enter key.

To list what is in the current directory, just `ls`.

For example, `ls /home/repl` shows you what's in your `/home/repl` directory.

---

## How else can I identify files and directories?

An **absolute path** is like a latitude and longitude: it has the same value no matter where you are.

A **relative path**, on the other hand, specifies a location starting from where you are: it's like saying "20 kilometers north".

The shell decides if a path is absolute or relative by looking at **its first character**: If it begins with `/`, it is **absolute**. If it does not begin with `/`, it is **relative**.

---

## How can I move to another directory?

You can move around in the filesystem using the command `cd /path/to/dir` (which stands for "change directory").

---

## How can I move up a directory?

More often, you will take advantage of the fact that the special path `..` (two dots with no spaces) means "the directory above the one I'm currently in". So, you can use `cd ..` to move up to **the parent directory**.

The root directory `/` is **the very top of the filesystem**.

A single dot on its own, `.`, always means **"the current directory"**, so `ls` on its own and `ls .` do the same thing, while `cd .` has no effect.

One final special path is `~` (the tilde character), which means **"your home directory"**, such as /home/repl. No matter where you are, `ls ~` will always list the contents of your home directory, and `cd ~` will always take you home.

---

## How can I copy files?

```shell
cp original.txt duplicate.txt
```

> NOTE: If there already was a file called `duplicate.txt`, it is overwritten.

```shell
cp seasonal/autumn.csv seasonal/winter.csv backup
```

> NOTE: If the last parameter to `cp` is an existing directory, then the command above will copy all of the files (`seasonal/autumn.csv` and `seasonal/winter.csv`) into that directory.

---

## How can I move a file?

`mv` moves it from one directory to another.

```shell
mv autumn.csv winter.csv ..
```

> NOTE: Moves the files `autumn.csv` and `winter.csv` from the current working directory up one level to its parent directory (because `..` always refers to the directory above your current location).

---

## How can I rename files?

`mv` can also be used to rename files. If you run:

```shell
mv course.txt old-course.txt
```

then the `file course.txt` in the current working directory is **"moved"** to the file `old-course.txt`.

> IMPORTANT: Just like `cp`, `mv` will overwrite existing files. If, for example, you already have a file called `old-course.txt`, then the command shown above will replace it with whatever is in `course.txt`.

---

## How can I delete files?

We use `rm`, which stands for **"remove"**. As with `cp` and `mv`, you can give `rm` the names of as many files as you'd like, so:

```shell
rm thesis.txt backup/thesis-2017-08.txt
```

> NOTE: This will remove both `thesis.txt` and `backup/thesis-2017-08.txt`.
>
> IMPORTANT: `rm` does exactly what its name says, and it does it **right away**: unlike graphical file browsers, **the shell doesn't have a trash can**, so when you type the command above, your thesis is gone for good.

---

## How can I create and delete directories?

Create a new directory by `mkdir /path/to/new/dir`.

Remove an empty directory by `rmdir /path/to/empty/dir`. Note that `rmdir` will only work when the directory is empty.

---

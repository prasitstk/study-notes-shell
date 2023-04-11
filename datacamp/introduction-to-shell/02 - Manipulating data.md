# 02 - Manipulating data

## How can I view a file's contents?

The simplest way to do this is with `cat`, which just prints the contents of files onto the screen.

Its name is short for **"concatenate"**, meaning **"to link things together"**, since it will print all the files whose names you give it, one after the other.

---

## How can I view a file's contents piece by piece?

We can use the command called `less`. When you less a file, one page is displayed at a time:

- `spacebar` to page down
- `q` to quit

If you give `less` **the names of several files**, you can type:

- `:n` (colon and a lower-case 'n') to move to the next file
- `:p` to go back to the previous one
- `:q` to quit.

---

## How can I look at the start of a file?

Run `head /path/to/file`

```shell
head seasonal/summer.csv
```

Display:

```text
Date,Tooth
2017-01-11,canine
2017-01-18,wisdom
2017-01-21,bicuspid
2017-02-02,molar
2017-02-27,wisdom
2017-02-27,wisdom
2017-03-07,bicuspid
2017-03-15,wisdom
2017-03-20,canine
```

---

## How can I type less?

One of the shell's power tools is **tab completion**. If you start typing the name of a file and then press the tab key, the shell will do its best to auto-complete the path. For example, if you type `sea` and press **tab**, it will fill in the directory name `seasonal/` (with a trailing slash). If you then type `a` and **tab**, it will complete the path as `seasonal/autumn.csv`.

If the path is ambiguous, such as `seasonal/s`, pressing **tab a second time** will display a list of possibilities. Typing another character or two to make your path more specific and then pressing **tab** will fill in the rest of the name.

---

## How can I control what commands do?

The shell lets you change `head`'s behavior by giving it **a command-line flag** (or just "flag" for short). If you run the command:

```shell
head -n 3 seasonal/summer.csv
```

> NOTE: This will only display the first three lines of the file.
>
> NOTE: A flag's name usually indicates its purpose (for example, `-n` is meant to signal **"number of lines"**).

---

## How can I list everything below a directory?

In order to see everything underneath a directory, no matter how deeply nested it is, you can give `ls -R` (The flag `-R` means **"recursive"**).

Note that `ls -F` the flag `-F` that prints a `/` after the name of every directory in the list and a `*` after the name of every runnable program.

So, you can also run `ls -R -F /path/to/dir` to see everything it contains with `/` and `*` indicators. (The order of the flags doesn't matter, but the directory name must come last.)

---

## How can I get help for a command?

To find out what commands do, people used to use the `ma`n command (short for **"manual"**). For example, the command `man head` brings up this information of the `head` command.

`man` automatically invokes `less`, so you may need to press **spacebar** to page through the information and `:q` to quit.

---

## How can I select columns from a file?

`head` and `tail` let you select **rows** from a text file. If you want to select **columns**, you can use the command `cut`.

```shell
cut -f 2-5,8 -d , values.csv
```

> NOTE: This means "select columns 2 through 5 and columns 8, using comma as the separator".

`cut` uses `-f` (meaning **"fields"**) to specify columns and `-d` (meaning **"delimiter"**) to specify the separator. You need to specify the latter because some files may use spaces, tabs, or colons to separate columns.

> NOTE: `cut -f 1` or `cut -f1` means the same.

### What can't cut do?

`cut` is a simple-minded command. In particular, **it doesn't understand quoted strings**. If, for example, your file is:

```text
Name,Age
"Johel,Ranjit",28
"Sharma,Rupinder",26
```

then:

```shell
cut -f 2 -d , everyone.csv
```

will produce:

```text
Age
Ranjit"
Rupinder"
```

### Example

```shell
echo "first:second:third:" | cut -d : -f 2
# Result:
# second

echo "first:second:third:" | cut -d : -f 2-3
# Result:
# second:third

echo "first:second:third:" | cut -d : -f 2-4
# Result:
# second:third:
```

---

## How can I repeat commands?

One of the biggest advantages of using the shell is that it makes it easy for you to do things over again. If you run some commands, you can then press the **up-arrow** key to cycle back through them. You can also use the **left and right arrow keys** and the **delete** key to edit them. Pressing **return** will then run the modified command.

Even better, `history` will print a list of commands you have run recently. Each one is preceded by **a serial number** to make it easy to re-run particular commands: just type `!55` to **re-run the 55th command** in your `history` (if you have that many). You can also re-run a command by typing **an exclamation mark followed by the command's name**, such as `!head` or `!cut`, which will **re-run the most recent use of that command**.

---

## How can I select lines containing specific values?

`grep` selects lines according to what they contain. In its simplest form, `grep` takes a piece of text followed by one or more filenames and prints all of the lines in those files that contain that text.

For example, `grep bicuspid seasonal/winter.csv` prints lines from `winter.csv` that contain **"bicuspid"**.

`grep` can search for **patterns** as well; we will explore those in the next course. What's more important right now is some of `grep`'s more common flags:

- `-c`: print a count of matching lines rather than the lines themselves
- `-h`: do not print the names of files when searching multiple files
- `-i`: ignore case (e.g., treat "Regression" and "regression" as matches)
- `-l`: print the names of files that contain matches, not the matches
- `-n`: print line numbers for matching lines
- `-v`: invert the match, i.e., only show lines that don't match

### Example

```shell
grep molar seasonal/autumn.csv

# Result:
# 2017-02-01,molar
# 2017-05-25,molar
```

```shell
grep -n -v molar seasonal/spring.csv

# Results
# 1:Date,Tooth
# 2:2017-01-25,wisdom
# 3:2017-02-19,canine
# 4:2017-02-24,canine
# 5:2017-02-28,wisdom
# 6:2017-03-04,incisor
# 7:2017-03-12,wisdom
# 8:2017-03-14,incisor
# 10:2017-04-29,wisdom
# 11:2017-05-08,canine
# 12:2017-05-20,canine
# 13:2017-05-21,canine
# 14:2017-05-25,canine
# 16:2017-06-13,bicuspid
# 17:2017-06-14,canine
# 18:2017-07-10,incisor
# 19:2017-07-16,bicuspid
# 20:2017-07-23,bicuspid
# 21:2017-08-13,bicuspid
# 22:2017-08-13,incisor
# 23:2017-08-13,wisdom
```

```shell
grep -c incisor seasonal/autumn.csv seasonal/winter.csv

# Results:
# seasonal/autumn.csv:3
# seasonal/winter.csv:6
```

```shell
grep -v Tooth spring.csv summer.csv

# Results:
# spring.csv:2017-01-25,wisdom
# spring.csv:2017-02-19,canine
# spring.csv:2017-02-24,canine
# spring.csv:2017-02-28,wisdom
# spring.csv:2017-03-04,incisor
# ...
# summer.csv:2017-01-11,canine
# summer.csv:2017-01-18,wisdom
# summer.csv:2017-01-21,bicuspid
# summer.csv:2017-02-02,molar
# summer.csv:2017-02-27,wisdom
# ...

grep -h -v Tooth spring.csv summer.csv

# Results:
# 2017-01-25,wisdom
# 2017-02-19,canine
# 2017-02-24,canine
# 2017-02-28,wisdom
# 2017-03-04,incisor
# ...
# 2017-01-11,canine
# 2017-01-18,wisdom
# 2017-01-21,bicuspid
# 2017-02-02,molar
# 2017-02-27,wisdom
# ...
```

---

## Merge text files by column

Instead of `cut`, `paste` command will combine the data files by column in a single table using a specified separator.

### Example

```shell
paste -d ,  seasonal/autumn.csv seasonal/winter.csv

# Result:
# Date,Tooth,Date,Tooth
# 2017-01-05,canine,2017-01-03,bicuspid
# 2017-01-17,wisdom,2017-01-05,incisor
# 2017-01-18,canine,2017-01-21,wisdom
# 2017-02-01,molar,2017-02-05,molar
# 2017-02-22,bicuspid,2017-02-17,incisor
# 2017-03-10,canine,2017-02-25,bicuspid
# 2017-03-13,canine,2017-03-12,incisor
# 2017-04-30,incisor,2017-03-25,molar
# ...
```

> NOTE: The caveat is that the last few rows have the wrong number of columns.

---

## More example about `tail`

```shell
tail -n +2 seasonal/*.csv

# Results:
# ==> seasonal/autumn.csv <==
# 2017-01-05,canine
# 2017-01-17,wisdom
# 2017-01-18,canine
# 2017-02-01,molar
# ...
# ==> seasonal/spring.csv <==
# 2017-01-25,wisdom
# 2017-02-19,canine
# 2017-02-24,canine
# 2017-02-28,wisdom
# ...
```

```shell
tail -q -n +2 seasonal/*.csv

# Results:
# 2017-01-05,canine
# 2017-01-17,wisdom
# 2017-01-18,canine
# 2017-02-01,molar
# ...
# 2017-01-25,wisdom
# 2017-02-19,canine
# 2017-02-24,canine
# 2017-02-28,wisdom
# ...
```

---

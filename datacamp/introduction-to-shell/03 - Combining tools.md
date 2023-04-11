# 03 - Combining tools

## How can I store a command's output in a file?

You can use **redirection** to save any command's output anywhere you want. If you run this command:

```shell
head -n 5 seasonal/summer.csv > top.csv

# Check by
cat top.csv
```

---

## How can I use a command's output as an input?

```shell
head -n 5 seasonal/winter.csv > top.csv
tail -n 3 top.csv
```

---

## What's a better way to combine commands?

Using **redirection** to combine commands has two drawbacks:

1. It leaves a lot of intermediate files lying around (like top.csv).
2. The commands to produce your final result are scattered across several lines of history.

The shell provides another tool that solves both of these problems at once called a **pipe**. Once again, start by running head:

```shell
head -n 5 seasonal/summer.csv | tail -n 3
```

The **pipe** symbol tells the shell to use **the output of the command on the left as the input to the command on the right**.

## Example

```shell
cut -d , -f2 seasonal/summer.csv | grep -v Tooth
```

---

## How can I combine many commands?

You can chain any number of commands together. For example, this command:

```shell
cut -d , -f 1 seasonal/spring.csv | grep -v Date | head -n 10
```

will:

1. select the first column from the spring data;
2. remove the header line containing the word "Date"; and
3. select the first 10 lines of actual data.

---

## How can I count the records in a file?

The command `wc` (short for **"word count"**) prints the number of **c**haracters, **w**ords, and **l**ines in a file. You can make it print only one of these using `-c`, `-w`, or `-l` respectively.

```shell
grep 2017-07 seasonal/spring.csv | wc -l

# Results
# 3
```

---

## How can I specify many files at once?

Instead of:

```shell
cut -d , -f 1 seasonal/winter.csv seasonal/spring.csv seasonal/summer.csv seasonal/autumn.csv
```

Use **wildcards** to specify a list of files with a single expression. The most common wildcard is `*`, which means **"match zero or more characters"*.

```shell
cut -d , -f 1 seasonal/*

# or

cut -d , -f 1 seaso
### Example

```shell
head -n 3 seasonal/s*.csv

# Result:
# ==> seasonal/spring.csv <==
# Date,Tooth
# 2017-01-25,wisdom
# 2017-02-19,canine

# ==> seasonal/summer.csv <==
# Date,Tooth
# 2017-01-11,canine
# 2017-01-18,wisdom
```

### What other wildcards can I use?

The shell has other wildcards as well, though they are less commonly used:

- `?` matches a single character, so `201?.txt` will match `2017.txt` or `2018.txt`, but not `2017-01.txt`.
- `[...]` matches **any one of the characters inside the square brackets**, so `201[78].txt` matches `2017.txt` or `2018.txt`, but not `2016.txt`.
- `{...}` matches **any of the comma-separated patterns inside the curly brackets**, so `{*.txt, *.csv}` matches any file whose name ends with .txt or .csv, but not files whose names end with .pdf.

---

## How can I sort lines of text?

As its name suggests, `sort` puts data in order. By default it does this in **ascending alphabetical order**, but the flags:

- `-n` can be used to sort numerically
- `-r` can be used to reverse the order of its output
- `-b` tells it to ignore leading blanks
- `-f` tells it to fold case (i.e., be case-insensitive)

### Example

```shell
cut -d , -f 2 seasonal/winter.csv | grep -v Tooth | sort -r

# Result:
# wisdom
# wisdom
# wisdom
# wisdom
# molar
# molar
# molar
# molar
# incisor
# incisor
# incisor
# incisor
# incisor
# incisor
# canine
# canine
# canine
# canine
# canine
# canine
# canine
# bicuspid
# bicuspid
# bicuspid
# bicuspid
```

```shell
wc -l seasonal/*.csv | grep -v total

# Result:
# 21 seasonal/autumn.csv
# 24 seasonal/spring.csv
# 25 seasonal/summer.csv
# 26 seasonal/winter.csv
  
wc -l seasonal/*.csv | grep -v total | sort -n

# Result:
# 21 seasonal/autumn.csv
# 24 seasonal/spring.csv
# 25 seasonal/summer.csv
# 26 seasonal/winter.csv

wc -l seasonal/*.csv | grep -v total | sort -n | head -n 1

# Result: The file containing the fewest lines.
# 21 seasonal/autumn.csv
```

---

## How can I remove duplicate lines?

Another command that is often used with `sort` is `uniq`, whose job is to remove duplicated lines. **More specifically**, it removes **adjacent duplicated lines**. If a file contains:

```text
2017-07-03
2017-07-03
2017-08-03
2017-08-03
```

then `uniq` will produce:

```text
2017-07-03
2017-08-03
```

but if it contains:

```text
2017-07-03
2017-08-03
2017-07-03
2017-08-03
```

then `uniq` will print **all four lines**.

The reason is that `uniq` is built to work with very large files. In order to remove non-adjacent lines from a file, it would have to keep the whole file in memory (or at least, all the unique lines seen so far). By only removing adjacent duplicates, it only has to keep the most recent unique line in memory.

`uniq -c` will display unique lines with a count of how often each occurs.

### Example

```shell
cut -d , -f 2 seasonal/winter.csv | grep -v Tooth | sort | uniq -c

# Result:
#       4 bicuspid
#       7 canine
#       6 incisor
#       4 molar
#       4 wisdom
```

---

## How can I save the output of a pipe?

The shell lets us redirect the output of a sequence of piped commands:

```shell
cut -d , -f 2 seasonal/*.csv | grep -v Tooth > teeth-only.txt
```

However, `>` must appear at the end of the pipeline: if we try to use it in the middle, like this:

```shell
cut -d , -f 2 seasonal/*.csv > teeth-only.txt | grep -v Tooth
```

then all of the output from cut is written to teeth-only.txt, so there is nothing left for grep and **it waits forever for some input**.

However, `>` can also apeear at the first of command as follows:

```shell
head -n 3 seasonal/winter.csv > result.txt

# is the same as

> result.txt head -n 3 seasonal/winter.csv
```

### How can I stop a running program?

The commands and scripts that you have run so far have all executed quickly, but some tasks will take minutes, hours, or even days to complete. You may also mistakenly put redirection in the middle of a pipeline, causing it to hang up.

If you decide that you don't want a program to keep running, you can type `Ctrl + C` to end it. This is often written `^C` in Unix documentation; note that the **'c'** can be lower-case.

---

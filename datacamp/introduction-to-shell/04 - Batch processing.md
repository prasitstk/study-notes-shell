# 04 - Batch processing

## How does the shell store information: Environment variables?

Like other programs, the shell stores information in variables. Some of these, called **environment variables**, are available all the time.

**Environment variables**' names are conventionally written in **upper case**, and a few of the more commonly-used ones are shown below:

- `HOME` = User's home directory: `/home/repl`
- `PWD` = Present working directory (same as `pwd` command)
- `SHELL` = Which shell program is being used: `/bin/bash`
- `USER` = User's ID: `repl`
- `HISTFILESIZE` = determines how many old commands are stored in your command history.
- `OSTYPE` = holds the name of the kind of operating system you are using: `linux-gnu`

---

## How can I print a variable's value?

```shell
echo $USER

# Result:
# repl
```

---

## How else does the shell store information: Shell variables?

The other kind of variable is called **a shell variable**, which is like a local variable in a programming language.

To create **a shell variable**, you simply assign a value to a name:

```shell
training=seasonal/summer.csv
```

> IMPORTANT: Must not have any spaces before or after the `=` sign.

Once you have done this, you can check the variable's value with:

```shell
echo $training
```

---

## How can I repeat a command many times?

```shell
for filetype in gif jpg png; do echo $filetype; done

# Result
# gif
# jpg
# png
```

---

## How can I repeat a command once for each file?

```shell
for filename in seasonal/*.csv; do echo $filename; done

# Result:
# seasonal/autumn.csv
# seasonal/spring.csv
# seasonal/summer.csv
# seasonal/winter.csv
```

> NOTE: because the shell expands seasonal/*.csv to be a list of four filenames before it runs the loop.

### Example

```shell
files=seasonal/*.csv
for f in $files; do echo $f; done

# Result:
# seasonal/autumn.csv
# seasonal/spring.csv
# seasonal/summer.csv
# seasonal/winter.csv
```

---

## How can I run many commands in a single loop?

```shell
for file in seasonal/*.csv; do head -n 2 $file | tail -n 1; done
```

```shell
for file in seasonal/*.csv; do grep 2017-07 $file | tail -n 1; done
```

---

## Why shouldn't I use spaces in filenames?

It's easy and sensible to give files multi-word names like `July 2017.csv` when you are using a graphical file explorer. However, this causes problems when you are working in the shell. For example, suppose you wanted to rename `July 2017.csv` to be `2017 July data.csv`. You cannot type:

```shell
mv July 2017.csv 2017 July data.csv
```

because it looks to the shell as though you are trying to move four files called `July`, `2017.csv`, `2017`, and `July` (again) into a directory called `data.csv`.

Instead, you have to **quote** the files' names so that the shell treats each one as a single parameter:

```shell
mv 'July 2017.csv' '2017 July data.csv'
```

---

## How can I do many things in a single loop?

To tell the shell where one ends and the next begins, you must separate them with **semi-colons**:

```shell
for f in seasonal/*.csv; do echo $f; head -n 2 $f | tail -n 1; done
```

---

# 01 - From Command-Line to Bash Script

## Shell commands refresher

- `(e)grep` filters input based on regex pattern matching
- `cat` concatenates file contents line-by-line
- `tail` / `head` give only the last/first -n (a flag) lines
- `wc` does a word or line count (with flags `-w` (word counts) `-l` (line counts))
- `sed` does pattern-matched string replacement

---

## Some shell practice

- `grep a fruits.txt` or `grep 'a' fruits.txt` will filter and print only the lines in `fruits.txt` file that contain "a" character.
- `grep [pc] fruits.txt` or `grep '[pc]' fruits.txt` will filter and print only the lines in `fruits.txt` file that contain "p" or "c" or both characters.
- `cat new_fruits.txt | sort | uniq -c | head -n 3` will do the following:
  - `cat new_fruits.txt` will print all lines but they are piped as input to the next command.
  - `sort` will sort all lines from input and output to the next command.
  - `uniq -c` will make the lines of input unique. Note that it is required input to be sorted first because it will only try to make the adjacent line unique only. The `-c` option will count the number of each unique line that appear in the original one before they are unique.
  - `head -n 3` will print output only the first 3 lines.
- `cat animals.txt | cut -d " " -f 2 | sort | uniq -c` will do the following:
  - `cat animals.txt` will print all lines but they are piped as input to the next command.
  - `cut -d " " -f 2` will split on spaces and take the second field.
  - `sort` will sort all lines from input and output to the next command.
  - `uniq -c` will do the unique count for each unique group.

### Input files

#### fruits.txt

```text
banana
apple
carrot
```

#### new_fruits.txt

```text
pineapple
banana
carrot
apple
carrot
apple
grape
carrot
apple
pineapple
grape
apple
```

#### animals.txt

```text
magpie, bird
emu, bird
kangaroo, marsupial
wallaby, marsupial
shark, fish
```

---

## Other examples

- `cat soccer_scores.csv | grep 1959` will return `1959,Dunav,2` which is a line in the `soccer_scores.csv` file.
- `cat two_cities.txt | grep -e 'Sydney Carton' -e 'Charles Darnay' | wc -l` or `cat two_cities.txt | egrep 'Sydney Carton|Charles Darnay' | wc -l` will count lines that contain either the character 'Sydney Carton' or 'Charles Darnay'.

---

## Bash script anatomy

- Begin with `#!/usr/bash` (she-bang)
  - So your interpreter knows it is a Bash script and to use Bash located in `/usr/bash`
  - This could be a different path if you installed Bash somewhere else such as `/bin/bash` (type `which bash` to check)

- Middle lines contain code (line-by-line commands)

- File extension = `.sh`. This is not needed if the first line has the she-bang and path to Bash (``#!/usr/bash`), but a convention.

- Can be run in the terminal using `bash script_name.sh`. This way, `script_name.sh` do not need to be the executable file and she-bang is not needed.
  - Or if you have mentioned first line (`#!/usr/bash`) you can simply run using `./script_name.sh`. However, this way, `script_name.sh` needs to be the executable file by running `chmod +x script_name.sh` first, for example.

---

## References

### REGEX (Regular Expressions)

- [regex101.com](https://regex101.com/)

### SED command

- [Sed Command in Linux/Unix With Examples](https://www.interviewkickstart.com/learn/sed-command-in-linux-unix-with-examples)

---

## A simple Bash script

### script.sh

```shell
#!/bin/bash

cat server_log_with_todays_date.txt
```

### server_log_with_todays_date.txt

```text
2019-01-01 | server request | windows | ping-2.000
2019-02-01 | server request | mac | ping-2.000
2019-02-01 | server request | windows | ping-12.000
2019-02-01 | server request | linux | ping-6.000
```

---

## Shell pipelines to Bash scripts

### script.sh

```shell
#!/bin/bash

cat soccer_scores.csv | cut -d "," -f 2 | tail -n +2 | sort | uniq -c
```

> NOTE: `tail -n +2` this just ensures we don't aggregate the CSV headers. Instead of the last NUM lines, use `-n +NUM` to output starting with line NUM (the first line is `1`).

### soccer_scores.csv

```text
﻿Year,Winner,Winner Goals
1932,Arda,4
1933,Botev,1
1934,Cherno,5
1935,Dunav,2
1936,Cherno,4
1937,Dunav,4
1938,Beroe,5
1939,Botev,2
1940,Beroe,3
1941,Botev,1
1942,Botev,1
1943,Arda,1
1944,Botev,4
1945,Botev,1
1946,Botev,4
1947,Etar,1
1948,Dunav,2
1949,Etar,3
1950,Dunav,2
1951,Etar,4
1952,Dunav,3
1953,Beroe,5
1954,Botev,2
1955,Beroe,2
1956,Lokomotiv,4
1957,Levski,5
1958,Dunav,2
1959,Dunav,2
1960,Cherno,3
1961,Etar,3
1962,Dunav,2
1963,Cherno,5
1964,Arda,3
1965,Etar,3
1966,Arda,5
1967,Etar,3
1968,Arda,5
1969,Levski,3
1970,Dunav,3
1971,Etar,2
1972,Dunav,3
1973,Etar,1
1974,Dunav,1
1975,Etar,1
1976,Arda,2
1977,Cherno,2
1978,Dunav,5
1979,Dunav,5
1980,Arda,3
1981,Cherno,5
1982,Dunav,5
1983,Beroe,3
1984,Arda,2
1985,Beroe,1
1986,Etar,3
1987,Arda,1
1988,Etar,1
1989,Botev,3
1990,Beroe,3
1991,Cherno,5
1992,Dunav,4
1993,Etar,4
1994,Arda,3
1995,Beroe,5
1996,Arda,1
1997,Levski,1
1998,Dunav,5
1999,Levski,1
2000,Dunav,2
2001,Etar,5
2002,Arda,4
2003,Etar,2
2004,Arda,4
2005,Etar,4
2007,Cherno,4
```

---

## Extract and edit using Bash scripts

### script.sh

```shell
#!/bin/bash

cat soccer_scores.csv | sed 's/Cherno/Cherno City/g' | sed 's/Arda/Arda United/g' > soccer_scores_edited.csv
```

### soccer_scores.csv

```text
Year,Winner,Winner Goals
1932,Arda,4
1933,Botev,1
1934,Cherno,5
1935,Dunav,2
1936,Cherno,4
1937,Dunav,4
1938,Beroe,5
1939,Botev,2
1940,Beroe,3
1941,Botev,1
1942,Botev,1
1943,Arda,1
1944,Botev,4
1945,Botev,1
1946,Botev,4
1947,Etar,1
1948,Dunav,2
1949,Etar,3
1950,Dunav,2
1951,Etar,4
1952,Dunav,3
1953,Beroe,5
1954,Botev,2
1955,Beroe,2
1956,Lokomotiv,4
1957,Levski,5
1958,Dunav,2
1959,Dunav,2
1960,Cherno,3
1961,Etar,3
1962,Dunav,2
1963,Cherno,5
1964,Arda,3
1965,Etar,3
1966,Arda,5
1967,Etar,3
1968,Arda,5
1969,Levski,3
1970,Dunav,3
1971,Etar,2
1972,Dunav,3
1973,Etar,1
1974,Dunav,1
1975,Etar,1
1976,Arda,2
1977,Cherno,2
1978,Dunav,5
1979,Dunav,5
1980,Arda,3
1981,Cherno,5
1982,Dunav,5
1983,Beroe,3
1984,Arda,2
1985,Beroe,1
1986,Etar,3
1987,Arda,1
1988,Etar,1
1989,Botev,3
1990,Beroe,3
1991,Cherno,5
1992,Dunav,4
1993,Etar,4
1994,Arda,3
1995,Beroe,5
1996,Arda,1
1997,Levski,1
1998,Dunav,5
1999,Levski,1
2000,Dunav,2
2001,Etar,5
2002,Arda,4
2003,Etar,2
2004,Arda,4
2005,Etar,4
2007,Cherno,4
```

---

## Standard streams & arguments

### STDIN-STDOUT-STDERR

In Bash scripting, there are three **streams** for your program:

- STDIN (standard input): A stream of data into the program
- STDOUT (standard output): A stream of data out of the program
- STDERR (standard error): Errors in your program

By default, these streams will come from and write out to the terminal.

- `2> /dev/null` means redirecting STDERR to be deleted.
- `1> /dev/null` means redirecting STDOUT to be deleted.

---

## STDIN vs ARGV

Bash scripts can take **arguments** to be used inside by adding a **space** after the script execution call.

- **ARGV** is the array (vector) of all the arguments given to the program.
- Each argument can be accessed via the `$` notation. The first as `$1`, the second as `$2` etc.
- `$0` stores the name of the script.
- `$@` and `$*` give all the arguments in **ARGV**
- `$#` gives the length (number) of arguments, excluding `$0`.

### Example

#### args.sh

```shell
#!/usr/bash
echo $1
echo $2
echo $@
echo "There are" $# "arguments"
```

#### Running

```shell
bash ./args.sh one two three four five
```

#### Output

```shell
./args.sh
one
two
one two three four five
There are 5 arguments
```

---

## Using arguments with HR data

### script.sh

```shell
# Echo the first ARGV argument
echo $1

# Cat all the files
# Then pipe to grep using the first ARGV argument
# Then write out to a named csv using the first ARGV argument
cat hire_data/*.csv | grep "$1" > "$1".csv
```

---

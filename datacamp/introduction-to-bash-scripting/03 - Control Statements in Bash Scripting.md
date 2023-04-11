# 03 - Control Statements in Bash Scripting

## IF statement

```shell
if [ CONDITION ]; then
    # SOME CODE
else
    # SOME OTHER CODE
fi
```

- Spaces between square brackets and conditional elements inside (first line)
- Semi-colon after close-bracket `];`

---

## IF statement and strings

```shell
x="Queen"
if [ $x == "King" ]; then
    echo "$x is a King!"
else
    echo "$x is not a King!"
fi
```

You could also use `!=` for **not equal to**)

---

## Arithmetic IF statement

### Option 1

Using the double-parentheses:

```shell
x=10
if (($x > 5)); then
    echo "$x is more than 5!"
fi
```

### Option 2

Using square brackets and an arithmetic flag rather than `>`, `<`, `==`. `!=`:

- `-eq` for **equal to**
- `-ne` for **not equal to**
- `-lt` for **less than**
- `-le` for **less than or equal to**
- `-gt` for **greater than**
- `-ge` for **greater than or equal to**

```shell
x=10
if [ $x -gt 5 ]; then
    echo "$x is more than 5!"
fi
```

---

## Other Bash conditional flags

- `-e` if the file exists
- `-s` if the file exists and has size greater than zero
- `-r` if the file exists and is readable
- `-w` if the file exists and is writable

## References

- [Bash Conditional Expressions](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)

---

## Using AND (`&&`) and OR (`||`) in Bash in multiple conditions

### Option 1

```shell
x=10
if [ $x -gt 5 ] && [ $x -lt 11 ]; then
    echo "$x is more than 5 and less than 11!"
fi
```

### Option 2

```shell
x=10
if [[ $x -gt 5 && $x -lt 11 ]]; then
    echo "$x is more than 5 and less than 11!"
fi
```

---

## IF and command-line programs

**IMPORTANT**: Please remove the square brackets in this case:

For example, to check if the file `words.txt` has **Hello** inside:

```shell
if grep -q Hello words.txt; then
    echo "Hello is inside!"
fi
```

> NOTE: The `-q` flag (quiet flag) is required so it does not return the matched lines like grep normally does. It just returns true if any lines match.

Using shell-within-a-shell:

```shell
if $(grep -q Hello words.txt); then
    echo "Hello is inside!"
fi
```

---

## Example :: Sorting model results

```shell
# Extract Accuracy from first ARGV element
accuracy=$(grep Accuracy $1 | sed 's/.* //')

# Conditionally move into good_models folder
if [ $accuracy -ge 90 ]; then
    mv $1 good_models/
fi

# Conditionally move into bad_models folder
if [ $accuracy -lt 90 ]; then
    mv $1 bad_models/
fi
```

---

## Example :: Moving relevant files

```shell
# Create variable from first ARGV element
sfile=$1

# Create an IF statement on sfile's contents
if grep -q 'SRVM_' $sfile && grep -q 'vpt' $sfile ; ____
	# Move file if matched
	mv $sfile good_logs/
fi
```

---

## FOR loop & WHILE statements

### Basic structure

```shell
for x in 1 2 3
do
    echo $x
done
```

### FOR loop number ranges

Bash has a neat way to create a numeric rnage called **brace expansion**:

- `{START..STOP..INCREMENT}`

> NOTE: INCREMENT defaults to one if not specified.

```shell
for x in {1..5..2}
do
    echo $x
done

# Result
# 1
# 3
# 5
```

### FOR loop three expression syntax

Another common way to write FOR loops is the **three expression** syntax.

```shell
for ((x=2;x<=4;x+=2))
do
    echo $x
done

# Result
# 2
# 4
```

### Glob expansions

```shell
for book in books/*
do
    echo $book
done

# Result:
# books/book1.txt
# books/book2.txt
```

```shell
# Use a FOR loop on files in directory
for file in inherited_folder/*.R
do  
    # Echo out each file
    echo $file
done

```

### Shell-within-a-shell to FOR loop

List all the files and pipe to a grep pattern-match to only keep those with **air** in their name (case insensitive).

```shell
for book in $(ls books/ | grep -i 'air')
do
    echo $book
done

# Result:
# AirportBook.txt
# FairMarketBook.txt
```

---

## WHILE statement syntax

```shell
x=1
while [ $x -le 3 ];
do
    echo $x
    ((x+=1))
done
```

---

## Example :: Cleaning up a directory

```shell
# Create a FOR statement on files in directory
for file in robs_files/*.py
do
    # Create IF statement using grep
    if grep -q 'RandomForestClassifier' $file ; then
        # Move wanted files to to_keep/ folder
        mv $file to_keep/
    fi
done
```

---

## CASE statements

### Basic CASE statement format

```shell
case 'STRINGVAR' in
  PATTERN1)
  COMMAND1;;
  PATTERN2)
  COMMAND2;;
  *)
  DEFAULT COMMAND;;
esac
```

- Begin by selecting which variable or string (`'STRINGVAR'`) to match against
  - You could call shell-within-a-shell here! 
- Add as many possible matches & actions as you like
  - You can use regex for the `PATTERN`. Such as `Air*` for `'starts with Air'` or `*hat*` for `'contains hat'`
- Ensure to separate the pattern and code to run by a close-parenthesis and finish commands with double semi-colon
- `*) DEFAULT COMMAND;;` It is common (but not required) to finish with a default command that runs if none of the other patterns match.
- `esac` Finally, the finishing word is `esac`, which is `case` backwards.

### IF version

```shell
if grep -q 'sydney' $1; then
    mv $1 sydney/
fi
if grep -q 'melbourne|brisbane' $1; then
    rm $1
fi
if grep -q 'canberra' $1; then
    mv $1 "IMPORTANT_$1"
fi
```

### CASE version

```shell
case $(cat $1) in
  *sydney*)
  mv $1 sydney/ ;;
  *melbourne*|*brisbane*)
  rm $1 ;;
  *canberra*)
  mv $1 "IMPORTANT_$1" ;;
  *)
  echo "No cities found" ;;
esac
```

---

## Example :: Days of the week with CASE

```shell
# Create a CASE statement matching the first ARGV element
case $1 in
  # Match on all weekdays
  Monday|Tuesday|Wednesday|Thursday|Friday)
  echo "It is a Weekday!";;
  # Match on all weekend days
  Saturday|Sunday)
  echo "It is a Weekend!";;
  # Create a default
  *) 
  echo "Not a day!";;
esac
```

---

## Example :: Moving model results with CASE

```shell
# Use a FOR loop for each file in 'model_out/'
for file in model_out/*
do
    # Create a CASE statement for each file's contents
    case $(cat $file) in
      # Match on tree and non-tree models
      *"Random Forest"*|*GBM*|*XGBoost*)
      mv $file tree_models/ ;;
      *KNN*|*Logistic*)
      rm $file ;;
      # Create a default
      *) 
      echo "Unknown model in $file" ;;
    esac
done

```

---

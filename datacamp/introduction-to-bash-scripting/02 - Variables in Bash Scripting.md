# 02 - Variables in Bash Scripting

## Assigning variables

Similar to other languages, you can assign variables with the equals notation.

```shell
var1="Moon"
var2='Liu'
```

Then reference with `$` notation.

```shell
echo $var1
echo "Hi there" $var1 $var2
```

**IMPORTANT**: Bash is NOT forgiving about spaces in variable creation.

```shell
var1 = "Moon"
echo $var1

# Result: script.sh: line 3: var1 command not found
```

---

## Single, double, backticks

In Bash, using different quotation marks can mean different things. Both when creating variables and printing.

- Single quotes (`'sometext'`) = Shell interprets what is between literally
- Double quotes (`"sometext"`) = Shell interprets literally **except** using `$` and backticks (`` ` ``)

The last way creates a **shell-within-a-shell**, outlined below. Useful for calling command-line programs. This is done with backticks.

- Backticks (`` `sometext` ``) = Shell runs the command and captures STDOUT back into a variable

Example

```shell
var1="The date is `date`."
echo $var1

# Result
# The date is Mon 2 Dec 2019 14:13:35 AEDT.
```

The `date` program was called, output captured and combined in-line with the `echo` call.

## Parentheses vs backticks

There is an equivalent to backtick notation:

```shell
var1="The date is `date`."
var2="The date is $(date)."
echo $var1
echo $var2

# Result
# The date is Mon 2 Dec 2019 14:13:35 AEDT.
# The date is Mon 2 Dec 2019 14:13:35 AEDT.
```

Both work the same though using backticks is older. Parentheses is used more in modern applications.

---

## Numeric variables in Bash

### `expr` command-line program

Numbers are not natively supported like Python or R: `1 + 4` gives you an error.

`expr` is a useful utility program that helps you to use basic arithmetic.

```shell
expr 1 + 4

# Result
# 5
```

> NOTE: `expr` cannot natively handle decimal places: `expr 1 + 2.5` gives you an error.

### `bc` command-line program

`bc` (basic calculator) is a useful program to perform calculations:

```shell
bc
# bc 1.07.1
# Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
# This is free software with ABSOLUTELY NO WARRANTY.
# For details type `warranty'.
> 5 + 7
> 12
> quit
# (back to shell)
```

Using `bc` without opening the calculator is possible by piping:

```shell
echo "5 + 7.5" | bc

# Result
# 12.5
```

`bc` also has a `scale` argument for how many decimal places.

```shell
echo "10 / 3" | bc

# Result
# 3

echo "scale=3; 10 / 3" | bc
# Result
# 3.333
```

---

## Number in Bash scripts

We can assign numeric variables just like string variables:

```shell
dog_name='Roger'
dog_age=6
echo "My dog's name is $dog_name and he is $dog_age years old"
```

Beware that `dog_age="6"` will work, but makes it a string!

---

## Double bracket notation

```shell
expr 5 + 7
echo $((5 + 7))

# Result
12
12
```

Beware this method uses `expr`, not `bc` (no decimals!)

---

## Shell within a shell revisited

```shell
model1=87.65
model2=89.20
echo "The total score is $(echo "$model1 + $model2" | bc)"
echo "The average score is $(echo "($model1 + $model2) / 2" | bc)"

# Result
# The total score is 176.85
# The average score is 88
```

---

## Example :: Converting Fahrenheit to Celsius

### script.sh

```shell
# Get first ARGV into variable
temp_f=$1

# Subtract 32
temp_f2=$(echo "scale=2; $temp_f - 32" | bc)

# Multiply by 5/9
temp_c=$(echo "scale=2; $temp_f2 * 5 / 9" | bc)

# Print the celsius temp
echo $temp_c
```

---

## Example :: Extracting data from files

### script.sh

```shell
# Create three variables from the temp data files' contents
temp_a=$(cat temps/region_A)
temp_b=$(cat temps/region_B)
temp_c=$(cat temps/region_C)

# Print out the three variables
echo "The three temperatures were $temp_a, $temp_b, and $temp_c"

```

---

## Arrays in Bash

Two types of arrays in Bash

- **Normal numerical-indexed array** ~ list in Python, vector in R = numerical indexes
- **Associative array** ~ dict in Python, list in R = key-value pairs
  - Note that associative array is only available in Base 4 onwards. Please check with `bash --version` in your terminal.

### Numerical-indexed array

#### Creating an array in Bash

1. Declare without adding elements

    ```shell
    declare -a my_first_array
    ```

2. Create and add elements at the same time

    ```shell
    my_first_array=(1 2 3)
    # Remember: No spaces around equal sign!
    # Remember: Bash uses spaces between array elements, not commas.
    ```

#### Array properties

- All array elements can be returned using `array[@]`. Bash requires curly brackets `{}` around the array name when you want to access these properties.

```shell
my_array=(1 3 5 2)
echo ${my_array[@]}
```

- The length of an array is accessed using `#array[@]`

```shell
echo ${#my_array[@]}
```

- Accessing array elements using square brackets.

```shell
my_first_array=(15 20 300 42)
echo ${my_first_array[2]}
# Return the third element
```

> NOTE: Bash uses zero-indexing for arrays.

- Set array elements using the index notation.

```shell
my_first_array=(15 20 300 42 23 2 4 33 54 67 66)
my_first_array[0]=999
echo ${my_first_array[0]}

# Result
# 999
```

> NOTE: Do not use the `$` when overwriting an index such as `${my_first_array[0]}=999` as this will not work.

- Use the notation `array[@]:N:M` to **slice** out a subset of the array.
  - Here `N` is the starting index and `M` is how many elements to return.

```shell
my_first_array=(15 20 300 42 23 2 4 33 54 67 66)
echo ${my_first_array[@]:3:2}

# Result
# 42 23
```

- Append to an array using `array+=(elements)`

```shell
my_array=(300 42 23 2 4 33 54 67 66)
my_array+=(10)
echo ${my_array[@]}

# Result
# 300 42 23 2 4 33 54 67 66 10

# If you do not add parentheses around what you want to append?
my_array=(300 42 23 2 4 33 54 67 66)
my_array+=10
echo ${my_array[@]}

# Result: The string 10 is just added to our first element.
# 30010 42 23 2 4 33 54 67 66

```

### Associative arrays

#### Creating an associative array

1. Declare without adding elements

    ```shell
    declare -A city_details
    city_details=([city_name]="New York" [population]=14000000)
    echo ${city_details[city_name]}
    ```

    ```shell
    # Create empty associative array
    declare -A model_metrics

    # Add the key-value pairs
    model_metrics[model_accuracy]=98
    model_metrics[model_name]="knn"
    model_metrics[model_f1]=0.82
    ```

2. Create and add elements at the same time

    ```shell
    declare -A city_details=([city_name]="New York" [population]=14000000)
    ```

```shell
declare -A model_metrics=([model_accuracy]=98 [model_name]="knn" [model_f1]=0.82)
```

You can access the keys of an associative array with an `!`:

```shell
echo ${!city_details[@]}  # Return all the keys
echo ${!model_metrics[@]}
```

---

## Example :: Climate calculations in Bash

```shell
# Create variables from the temperature data files
temp_b="$(cat temps/region_B)"
temp_c="$(cat temps/region_C)"

# Create an array with these variables as elements
region_temps=($temp_b $temp_c)

# Call an external program to get average temperature
average_temp=$(echo "scale=2; (${region_temps[0]} + ${region_temps[1]}) / 2" | bc)

# Append average temp to the array
region_temps+=($average_temp)

# Print out the whole array
echo ${region_temps[@]}

```

---

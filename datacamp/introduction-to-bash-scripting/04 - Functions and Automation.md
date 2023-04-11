# 04 - Functions and Automation

## Bash function anatomy

A Bash function has the following syntax:

```shell
function_name () {
    #function_code
    return #something
}
```

Alternate structure is:

```shell
function function_name {
    #function_code
    return #something
}
```

The main differences:

- Use the word `function` to denote starting a function build.
- You can drop the parenthesis on the opening line if you like, though many people keep them by convention.

Calling a Bash function by its name:

```shell
function print_hello () {
    echo "Hello world"
}
print_hello  # here we call the function
```

---

## Example :: Fahrenheit to Celsius Bash function

```shell
temp_f=30
function convert_temp () {
    temp_c=$(echo "scale=2; ($temp_f - 32) * 5 / 9" | bc)
    echo $temp_c
}
convert_temp # call the function

# Result
# -1.11
```

---

## Example :: Uploading model results to the cloud

```shell
# Create function
function upload_to_cloud () {
  # Loop through files with glob expansion
  for file in output_dir/*_results.txt
  do
    # Echo that they are being uploaded
    echo "Uploading $file to cloud"
  done
}

# Call the function
upload_to_cloud

```

---

## Example :: Get the current day

```shell
# Create function
what_day_is_it () {

  # Parse the results of date
  current_day=$(date | cut -d " " -f1)

  # Echo the result
  echo $current_day
}

# Call the function
what_day_is_it
```

---

## Passing arguments into Bash functions

Passing arguments into functions is similar to how you pass arguments into a script.

- Each argument can be accessed via the `$1`, `$2` notation.
- `$@` and `$*` give all the arguments in `ARGV`
- `$#` gives the length (number) of arguments

### Example

```shell
function print_filename {
    echo "The first file was $1"
    for file in $@
    do
        echo "This file has name $file"
    done
}

print_filename "LOTR.txt" "mod.txt" "A.py"
```

---

## Scope in Bash functions

All variables in Bash are global by default even initializing inside a function.

### Example :: Global scope

```shell
function print_filename {
    first_filename=$1
}
print_filename "LOTR.txt" "model.txt"
echo $first_filename

# Result
# LOTR.txt
```

You can use the `local` keyword to restrict variable scope.

```shell
function print_filename {
    local first_filename=$1
}
print_filename "LOTR.txt" "model.txt"
echo $first_filename

# Result
# (empty line because $first_filename outside the function has not been initialized yet in outer scope)
```

---

## Return values

The `return` option in Bash is only meant to determine if the function was a success (0) or failure (other values 1-255). It is captured in the global variable `$?`.

So, our options to return a value from the function instead of `return` are:

1. Assign to a global variable like the above example in the global scope.
2. `echo` what we want back (**last line** in function) and capture using shell-within-a-shell

### Example :: A return error

```shell
function function_2 {
    echlo. # An error of 'echo'
}
function_2 # Call the function. This will print the error message
echo $?  # This will print the error code 127

# Result
# script03.sh: line 2: echlo.: command not found
# 127
```

### Example :: Force to return 0

```shell
function function_2 {
    echlo. # An error of 'echo'
    echo Run this line even the above line error!
    return 0
}
function_2 # Call the function
echo $?

# Result
# script03.sh: line 2: echlo.: command not found
# Run this line even the above line error!
# 0
```

### Example :: Return a value from a function (The recommended way)

```shell
function convert_temp {
    echo $(echo "scale=2; ($1 - 32) * 5 / 9" | bc)
}
converted=$(convert_temp 30)
echo "30F in Celsius is $converted C"

# Result
# 30F in Celsius is -1.11 C
```

---

## Example :: A percentage calculator

```shell
# Create a function 
function return_percentage () {

  # Calculate the percentage using bc
  percent=$(echo "scale=2; 100 * $1 / $2" | bc)

  # Return the calculated percentage
  echo $percent
}

# Call the function with 456 and 632 and echo the result
return_test=$(return_percentage 456 632)
echo "456 out of 632 as a percent is $return_test%"

```

---

## Example :: Sports analytics function

```shell
# Create a function
function get_number_wins () {

  # Filter aggregate results by argument
  win_stats=$(cat soccer_scores.csv | cut -d "," -f2 | egrep -v 'Winner'| sort | uniq -c | egrep "$1")

}

# Call the function with specified argument
get_number_wins "Etar"

# Print out the global variable
echo "The aggregated stats are: $win_stats"

```

> NOTE: egrep -v 'Winner' is to filter out the lines that contain the word 'Winner'. `-v, --invert-match` option means selecting non-match lines.

---

## Example :: Summing an array

```shell
# Create a function with a local base variable
function sum_array () {
  local sum=0
  # Loop through, adding to base variable
  for number in "$@"
  do
    sum=$(echo "$sum + $number" | bc)
  done
  # Echo back the result
  echo $sum
  }
# Call function with array
test_array=(14 12 23.5 16 19.34)
total=$(sum_array "${test_array[@]}")
echo "The total sum of the test array is $total"

```

---

## Scheduling your scripts with Cron

`crontab -l` to see what schedules (`cron jobs`) are currently programmed for a particular user.
`crontab -e` to add/edit the list of `cron jobs` inside `crontab`.

### Cron job structure

```text
+---------- minute (0 - 59)
| +-------- hour (0 - 23)
| | +------ day of the month (1 - 31)
| | | +---- month (1 - 12)
| | | | +-- day of the week (0 - 6) (Sunday to Saturday, 7 = Sunday in some system)
| | | | |
* * * * * command to execute
```

### Examples

- `5 1 * * * bash myscript.sh` = Run every day at 1:05am
- `15 14 * * 7 bash myscript.sh` = Run at 2:15pm every Sunday
- `15,30,45 * * * * bash myscript.sh` = Run at the 15, 30, and 45 minutes every hour, every day etc.
- `*/15 * * * * bash myscript.sh` = Run every 15 minutes every hour, every day etc.

### References

- [crontab guru](https://crontab.guru/)

---

# 05 - Creating new tools

## How can I edit a file: nano?

Unix has a bewildering variety of text editors. For this course, we will use a simple one called Nano. If you type `nano filename`, it will open `filename` for editing (or create it if it doesn't already exist). You can move around with the arrow keys, delete characters using backspace, and do other operations with control-key combinations:

- `Ctrl + K`: delete a line.
- `Ctrl + U`: un-delete a line.
- `Ctrl + O`: save the file ('O' stands for 'output'). You will also need to press Enter to confirm the filename!
- `Ctrl + X`: exit the editor.

Note that in Nano, **"copy and paste"** is achieved by navigating to the line you want to copy, pressing `CTRL + K` to cut the line, then `CTRL + U` twice to paste two copies of it.

---

## How can I save commands to re-run later?

Since the commands you type in are just text, you can store them in files for the shell to run over and over again. To start exploring this powerful capability, put the following command in a file called `headers.sh`:

```shell
head -n 1 seasonal/*.csv
```

Once you have created this file, you can run it by typing:

```shell
bash headers.sh
```

This tells the shell (which is just a program called `bash`) to run the commands contained in the file `headers.sh`, which produces the same output as running the commands directly.

You can also save output to a file:

```shell
bash headers.sh > headers.out
```

---

## How can I write loops in a shell script?

Shell scripts can also contain loops. You can write them using semi-colons, or split them across lines without semi-colons to make them more readable:

```shell
# Print the first and last data records of each file.
for filename in $@
do
    head -n 2 $filename | tail -n 1
    tail -n 1 $filename
done
```

> NOTE: You don't have to indent the commands inside the loop, but doing so makes things clearer.
>
> NOTE: The first line of this script is a comment to tell readers what the script does. Comments start with the # character and run to the end of the line. 

---

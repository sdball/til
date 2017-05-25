# til shell things

## `comm`

The `comm` command allows you to quickly compare two sorted text files and see

- the lines only in the first file
- the lines only in the second file
- the lines in both files

By default all three results are displayed separated by tabs. You can suppress any of the three categories with command line arguments.

- `-1` suppress printing lines that are only in the first file
- `-2` suppress printing lines that are only in the second file
- `-3` suppress printing lines that are in both files
- `-i` case-insensitive comparision

`comm` is quite fast at comparing millions of lines of text.

### Examples

Let's say you just generated file of unique lines from a file that was already sorted:

```shell
uniq sorted_lines.txt > unique_sorted_lines.txt
```

When you count the lines you see that there were indeed some duplicates.

```
$ wc -l *
 30000001 sorted_lines.txt
 30000000 unique_sorted_lines.txt
 60000001 total
```

You certainly don't want to wait through all the time it would take to resort and count all the lines with `uniq -c` and then figure out the annoying sort/grep filter to get the results down to just the single line that would have a count of 2. comm to the rescue!

```
# suppress printing the lines only in unique_sorted_lines and (especially) the lines common to both
$ comm -23 sorted_lines.txt unique_sorted_lines.txt
the single line that's only in sorted_lines.txt and not in unique_sorted_lines.txt
```

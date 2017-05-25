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

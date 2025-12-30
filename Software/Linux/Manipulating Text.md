## cat and echo
cat is short for concatenate. We can read and print files but them main purpose of cat is to combine (concatenate) multiple files together. 

There is tac command(cat spelled backwards). Everything remain same but order of line is inverted.

echo just means 'say this out loud'. It is also used to append to files. 

## Working with Large and Compressed Files
Some files may require both viewing and administrative updating. 

Why Large Files Are a Problem
- Editors try to **load the entire file into memory**
- Large files can be **hundreds of megabytes or even gigabytes**
- Opening them can crash your terminal or slow down the system

The Smart Way: Use `less`
Instead of opening large files in an editor, use the **`less`** command. It lets you view the file one screen at a time **without loading the whole thing into memory**.
```bash
	less somefile
```
 and less can handle reading on its own so no need for cat. 

There is more also but less is more powerful than more. 

head command is used to quickly view the first few lines of a file. By default 10 lines. You can pass multiple files also. 

tail command is used to quickly view the last few lines of a file. It is useful since most output is often at the end of the file. 

## Sed and awk
When working with files in Linux, it's very common to:
- **Edit text**
- **Extract specific information**
- **Automate repetitive changes**
For these tasks, two classic and powerful tools are:
- `sed` – the **Stream Editor**
- `awk` – a **pattern scanning and processing** language
But the question may arise, then why not use Python or Perl ? 
While tools like **Python**, **Perl**, and **bash scripts** are widely used for automation, `sed` and `awk` remain essentials because:

|Feature|`sed` / `awk`|
|---|---|
|Lightweight|Yes|
|Fast execution|Yes|
|Works in minimal systems|Yes (even during boot)|
|Great for quick one-liners|Absolutely|

These tools use **minimal system resources** and run **faster** than heavier scripting languages.

What Can `sed` and `awk` Do?

|Tool|Common Use Cases|
|---|---|
|`sed`|- Search and replace text  <br>- Delete or insert lines  <br>- Modify files in-place|
|`awk`|- Process structured text (columns)  <br>- Summarize data  <br>- Extract fields from logs, CSV, or tabular data|
When to Use `sed` and `awk` ?
- Editing config files in automation scripts
- Quickly analyzing log files
- Extracting or formatting output in shell pipelines
- When writing scripts that need to run **everywhere**, including on **barebones or embedded systems**

#### sed
**sed** is a powerful text processing tool and is one of the oldest, earliest and most popular UNIX utilities. It is used to modify the contents of a file or input stream, usually placing the contents into a new file or output stream. Its name is an abbreviation for stream editor.
![[Pasted image 20250622132750.png]]
**sed** can filter text, as well as perform substitutions in data streams.

Data from an input source/file (or stream) is taken and moved to a working space. The entire list of operations/modifications is applied over the data in the working space and the final contents are moved to the standard output space (or stream).

**awk** is used to extract and then print specific contents of a file and is often used to construct reports. It was created at Bell Labs in the 1970s and derived its name from the last names of its authors: Alfred Aho, Peter Weinberger, and Brian Kernighan.

**awk** has the following features:
- It is a powerful utility and interpreted programming language.
- It is used to manipulate data files, and for retrieving and processing text.
- It works well with fields (containing a single piece of data, essentially a column) and records (a collection of fields, essentially a line in a file).
## File Manipulation Utilities
In managing your files, you may need to perform tasks such as sorting data and copying data from one location to another. Linux provides numerous file manipulation utilities that you can use while working with text files. In this section, you will learn about the following file manipulation programs:
- **sort**
- **uniq**
- **paste**
- **join**
- **split**.

Regular Expressions and Search Patterns
**Regular expressions** are text strings used for matching a specific pattern, or to search for a specific location, such as the start or end of a line or a word. Regular expressions can contain both normal characters or so-called meta-characters, such as ***** and **$**.

Many text editors and utilities such as **vi**, **sed**, **awk**, **find** and **grep** work extensively with regular expressions. Some of the popular computer languages that use regular expressions include Perl, Python and Ruby. It can get rather complicated and there are whole books written about regular expressions. Thus, we will do no more than skim the surface here.

These regular expressions are different from the wildcards (or meta-characters) used in filename matching in command shells such as bash (which were covered in the _Command-Line Operations_ chapter). The table lists search patterns and their usage.
![[Pasted image 20250622133845.png]]
For example, consider the following sentence: **the quick brown fox jumped over the lazy dog**.

Some of the patterns that can be applied to this sentence are as follows:
![[Pasted image 20250622133933.png]]
## Greps and Strings
**grep** is extensively used as a primary text searching tool. It scans files for specified patterns and can be used with regular expressions, as well as simple strings, as shown in the table:

**strings** is used to extract all printable character strings found in the file or files given as arguments. It is useful in locating human-readable content embedded in binary files; for text files one can just use **grep**.

For example, to search for the string **my_string** in a spreadsheet:  

`$ strings book1.xls | grep my_string`

## Miscellaneous Utilites
tr
tee, wc, cut

## Summary 
You have completed Chapter 13. Let’s summarize the key concepts covered:
- The command line often allows the users to perform tasks more efficiently than the GUI.
- **cat**, short for concatenate, is used to read, print, and combine files.
- **echo** displays a line of text either on standard output or to place in a file.
- **sed** is a popular stream editor often used to filter and perform substitutions on files and text data streams.
- **awk** is an interpreted programming language, typically used as a data extraction and reporting tool.
- **sort** is used to sort text files and output streams in either ascending or descending order.
- **uniq** eliminates duplicate entries in a text file.
- **paste** combines fields from different files. It can also extract and combine lines from multiple sources.
- **join** combines lines from two files based on a common field. It works only if files share a common field.
- **split** breaks up a large file into equal-sized segments.
- Regular expressions are text strings used for pattern matching. The pattern can be used to search for a specific location, such as the start or end of a line or a word.
- **grep** searches text files and data streams for patterns and can be used with regular expressions.
- **tr** translates characters, copies standard input to standard output, and handles special characters.
- **tee** saves a copy of standard output to a file while still displaying at the terminal.
- **wc** (word count) displays the number of lines, words, and characters in a file or group of files.
- **cut** extracts columns from a file.
- **less** views files a page at a time and allows scrolling in both directions.
- **head** displays the first few lines of a file or data stream on standard output. By default, it displays 10 lines.
- **tail** displays the last few lines of a file or data stream on standard output. By default, it displays 10 lines.
- **strings** extracts printable character strings from binary files.
- The **z** command family is used to read and work with compressed files.
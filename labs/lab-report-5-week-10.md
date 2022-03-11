# Week 10 Lab Report
**Objective**: How does our MarkdownParse compare to a standard Markdown parser, and how can we improve our own parser?  
**Due date**: Friday, March 11, 2022  
**Lab report requirements**: [https://ucsd-cse15l-w22.github.io/week/week10/](https://ucsd-cse15l-w22.github.io/week/week10/)  
  
I found the tests with different results by using `diff` on the results of my MarkdownParse versus Prof. Politz' MarkdownParse from Week 9. I used the following commands to make the results files:
```
# The repos were already cloned into my home directory on ieng6, so I'm skipping that part
# lab-9/ is the implementation provided in lab 9, markdown-parse is my implementation
$ cp -r lab-9/test-files markdown-parse/
$ cp lab-9/script.sh markdown-parse
$ cd markdown-parse/
$ bash script.sh > results.txt
$ cd ..
$ cd lab-9
$ bash script.sh > results.txt
$ cd ..
$ diff markdown-parse/results.txt lab-9/results.txt 
```
Then, I found the contents of the .md files using `cat [file].md`, and checked against the actual Markdown rendering using [Commonmark's renderer](https://spec.commonmark.org/dingus/). 

## 22.md
```
[foo](/bar\* "ti\*tle")
```
My output: `[/bar\* "ti\*tle"]`  
Joe's output: `[]`  
I don't think either of our outputs are correct. This was verified by looking at the Commonmark rendition:  
Expected output: `[/bar*]`  

The problem is that this link is formatted in a way not considered in our parser. It provides the URL within the parantheses as well as a title for the link inside quotation marks. It also has escaped asterisks.  

To fix our code, we would need to
1. Look for escaped characters.
2. Look for quotation marks (single or double) after a space and URL.

## 194.md
```
[Foo*bar\]]:my_(url) 'title (with parens)'

[Foo*bar\]]
```
My output: `[]`  
Joe's output: `[url]`  
I don't think either of our outputs are correct. This was verified by looking at the Commonmark rendition:  
Expected output: `[my_(url)]`  

The problem is that this link is also not formatted in a way not considered in our parser. It uses an escaped backslash, a regular backslash, and a colon to end the target and to signify the url as well as a title.  

To fix our code, we would need to
1. See if the link is in this specific format...? While playing with the renderer, it seems that it needs the specific sequence `\]]:` in order to render a link this way, so check for that.
2. Again, look for quotations marks (single or double) that signify the link titlee.
3. Look for the word after `\]]:` and make that the URL.
4. Ignore that the closing parentheses do not actually encase the link.


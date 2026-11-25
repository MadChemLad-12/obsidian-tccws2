# Source
Please use either of the two sources 

[[https://ia.net/writer/support/basics/markdown-guide#tutorial]]
[[https://www.markdownguide.org/basic-syntax/#headings]]

# Cheat Sheet

Format 	Markdown
Paragraph 	An empty line between paragraphs
Bold 	**bold**
Italic 	*italic*
Heading 	# Heading
Numbered list 	1. Item
Bulleted list 	- Item
Task list 	- [ ] Task
Blockquote 	> Quote
Line break 	Two spaces at the end of a line
Horizontal rule 	---
Image 	![Description](image.jpg)
Link 	[Text](https://example.com)
Table 	Vertical bars between cells, hyphens under the header
Strikethrough 	~~strikethrough~~
Highlight 	==highlight==
Footnote 	Text[^1] and [^1]: Note
Code 	`code`
Code block 	Three backticks above and below
Comment 	// Comment
Math 	$e^{i\pi}+1=0$

# Stuff I have no idea of
## Blockqoutes
Type > plus a space.
This basically allows then to fold under easyily

> A quoted paragraph
>> A quoted paragraph inside a quotation

## Nested lists
You can also nest lists in levels
* First level
    * Second level

1. First level
    1. Second level

* First level unordered list item
    1. Second level ordered list item


## Line breaks
From a mark down perspective you can do it via hitting entre or
<br />

“When you do want to insert a <br /> break tag using Markdown, you end a line with two or more spaces, then type return.”

## Horizontal Rules

You can add a thematic break which will be represented by a dividing line (<hr>) when exported to HTML. To do so, add three or more asterisks (*), hyphens (-), or underscores (_) on a line by themselves, optionally separated with spaces. Leave an empty line above and below. For example:

The first part ends here.

- - - 

The second part starts here.

## Page Breaks
For documents you can add a page break like this 
The last line of the first page.

+++

The first line of the next page.
Doesnt work here as it is currently not a pdf file

#  Images
Both local and web URLs are supported. Markdown uses the following syntax for images:
![](http://example.com/image.jpg)

![](./image.jpg)

the [] here stores the object image.jpg

# Links

Create a link by surrounding the link text in square brackets, followed immediately by the URL in parentheses:
[text to link](http://example.com/)

You can also use reference links. Add the reference in square brackets after the text to link. Then, on a line by itself add the reference with a colon, space, and the URL:

[text to link][ref]

…

[ref]: http://example.com/

# Escaping Characters

To display a literal character that would otherwise be used to format text in a Markdown document, add a backslash (\) in front of the character.

\* Without the backslash, this would be a bullet in an unordered list.

The rendered output looks like this:

* Without the backslash, this would be a bullet in an unordered list.

# Table of context

```
{{TOC}}

# Title
## Chapter
## Another Chapter

```

# Code

Use single backticks \` to indicate code within a line:
Keyword `func` indicates a function in Swift programming language.

Or use 3 \` to make a coding block
``` swift
class Shape {
    var numberOfSides = 0
}
```

# Comments
This line is visible. <!-- This is a comment -->
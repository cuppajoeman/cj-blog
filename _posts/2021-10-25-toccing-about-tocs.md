---
layout: post
title: TOCCing about TOC's
published: true
---

# Topic

Today I'll be showing you how to make your own custom table of contents for pdf files

# Terminology

OCR - Optical Character Recognition

# Motivation

When reading textbooks, one of the most handy things is to be able to move around in the book.

With physical textbooks, using sticky notes and the index is the fastest kind of navigation you can use.

In one sense having a physical textbook makes you learn the definitions faster since it becomes kind of a pain to be flipping through the book all the time.

On the other hand, having a virtual textook is easier for asking questions to larger audiences using screenshots and marking on that screenshot, also the way we can query electronic documents (so long as they are sufficently machine readable) is much better using tools like pdfgrep and such.

Additionally electronic documents can be more easily distributed, and for students who are low on cash (or just don't want to be spending their money on textbooks) this is great.

Though much of the time the quality of the documents we can get our hands on is quite low quality.

At a minimum every book we have should have a table of contents that lets you jump to the relevant section. (I would say this is equally or more important than having the book OCR'ed since even then getting to an exact section via searches can be a pain)

When you come across a book without a table of contents then the best thing to do is to usually try and find a version which has one, and otherwise you can make it yourself.

# A solution

The tool we will use is called jpdfbookmarks, it will allow us to create custom TOC's for a pdf. 

To get started we'll need to find the non-OCR'ed TOC from the book and start OCRing it for this we will follow this guide: 

![](https://ebooks.stackexchange.com/a/7763/18449)

At the end of the day we use ![this](https://ocr.space/tablerecognition) to OCR our pdf and ![pdftoppm](https://askubuntu.com/a/50180/582013) to get the pictures of the TOC.

Once you get the output, you'll realize that it's not perfect, but that the OCR usually makes the same mistakes in multiple places, so that we can usually regex most of the TOC into a good format.

At the end we'll need a format like this:

```
 Chapter 1. The Beginning/23
     Para 1.1 Child of The Beginning/25
         Para 1.1.1 Child of Child of The Beginning/26
 Chapter 2. The Continue/30,TopLeft,120,42
     Para 2.1 Child of The Beginning/32,FitPage
```

Where tab's indicate nesting and slash with a number is the page number that it will link to.

The fact is that we're not done yet, because the page number in the pdf (the one that you would see in the physical copy) is not actually the real page number you see in your electronic version, this is because the first couple pages count toward the latter, but not towards the former, therefore we need to give each of the page numbers we generated a specific offset, this can be done through jpdfbookmarks using the delta symbol after selecting all bookmarks.

But I found it easier to fix them with vim's regex. Here are some helpful regex:

* `%smagic/.*\zs\/\(\d\+\)/\= "\/" . (submatch(1)+1)/` - adds one to all page numbers
* `'<,'>s/.*\zs\/\(\d\+\)/\= "\/" . (submatch(1)+1)/` - adds one to all page numbers in selection

Once you have a text representation of your TOC you can now load that into jpdfbookmarks using the load bookmarks button. After saving the file we have a pdf with the correct TOC.

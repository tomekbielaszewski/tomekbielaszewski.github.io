---
title: Java and string formatting
published: true
comments: true
tags: english, java, everydayjob
---

# One day I found a bug
The other day I was working on some ecommerce system. In this part of the app I had to display a barcode in numeric format.
The requested design contained something like the following:
```
1234 12345 12345 1234 1
```
but I was simply returning a string:
```
1234123451234512341
```
Easy peasy lemon squeezy, it's a 5min job including unit tests. First thought was to use some regexp. You don't love it? Ew, 
are you a newbie?! Ok, ok I admit I have a little Stockholm Syndrome. I think the code would look something like:
```java
String s = "1234123451234512341";
s.replaceAll("(.{4})", "$1 ");
```
Job done! More 9gag time for me! But hey! To easy, something's fishy here. The pattern showed in design has 4 chars, then 
5 chars, 5 chars, 4 chars and a single one. I noticed that when writing unit tests. No way there is some elegant way of 
achieving this with regexp. I have to find something different.

Then I stumbled upon `MaskFormatter`. Very handy in my case, where I have to format string using a... mask, duh. The big downside 
is the fact it is a part of `javax.swing.text` package. Usually it is used together with `JFormattedTextField`. But I used 
it for simple string manipulation.

My final solution was:
```java
MaskFormatter maskFormatter = new MaskFormatter("#### ##### ##### #### #");
maskFormatter.setValueContainsLiteralCharacters(false);
return maskFormatter.valueToString(barcode).trim();
```

Works like a charm.

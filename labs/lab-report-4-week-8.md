# Week 8 Lab Report
**Objective**: What are some edge cases for parsing Markdown links?  
**Due date**: Friday, February 25, 2022  
**Lab report requirements**: [https://ucsd-cse15l-w22.github.io/week/week8/](https://ucsd-cse15l-w22.github.io/week/week8/)  
  
Link to my MarkdownParse repository: [https://github.com/sprestrelski/markdown-parse](https://github.com/sprestrelski/markdown-parse)  
Link to reviewed MarkdownParse repository: [https://github.com/m1ma0314/markdown-parse](https://github.com/m1ma0314/markdown-parse)

## Snippet 1
Expected Output: [`google.com, google.com, ucsd.edu]  

Test in `MarkdownParseTest.java` 
```java
@Test
    public void testCodeQuote() throws IOException{
        List<String> expect = List.of("`google.com", "google.com", "ucsd.edu");
        Path fileName = Path.of("lab8_1.md");
        String contents = Files.readString(fileName);
        assertEquals(expect, MarkdownParse.getLinks(contents));
    }
```

My implementation output, where my test failed:
```java
1) testCodeQuote(MarkdownParseTest)
java.lang.AssertionError: expected:<[`google.com, google.com, ucsd.edu]> but was:<[url.com, `google.com, google.com, google.com]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testCodeQuote(MarkdownParseTest.java:63)
```

Reviewed implementation output, where my test failed:
```java
1) testCodeQuote(MarkdownParseTest)
java.lang.AssertionError: expected:<[`google.com, google.com, ucsd.edu]> but was:<[url.com, `google.com, google.com]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testCodeQuote(MarkdownParseTest.java:87)
```

Code change:  
For snippet 1, you could probably just check if the backtick is closed inside of the brackets. However, I do not think there is a small (<10 line code change) that would make my program work for all cases related to inline code with backticks. You would need to check 
1. if the backtick is closed 
2. if the backtick is closed inside of the brackets
3. if there are more than 2 backticks (ie. code blocks use three backticks)
4. if the backtick is escaped, which would lead to
5. if there are double backticks


## Snippet 2
Expected Output: [a.com, a.com(()), example.com]  

Test in `MarkdownParseTest.java` 
```java
@Test
    public void testNestedEsc() throws IOException{
        List<String> expect = List.of("a.com", "a.com(())", "example.com");
        Path fileName = Path.of("lab8_2.md");
        String contents = Files.readString(fileName);
        assertEquals(expect, MarkdownParse.getLinks(contents));
    }
```

My implementation output, where my test failed: 
```java
2) testNestedEsc(MarkdownParseTest)
java.lang.AssertionError: expected:<[a.com, a.com(()), example.com]> but was:<[a.com, a.com, a.com((]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testNestedEsc(MarkdownParseTest.java:71)
```

Reviewed implementation output, where my test failed:
```java
2) testNestedEsc(MarkdownParseTest)
java.lang.AssertionError: expected:<[a.com, a.com(()), example.com]> but was:<[a.com, a.com((]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testNestedEsc(MarkdownParseTest.java:95)
```

Code change:  
I do not think there is a small (<10 line code change) that would make my program work for all cases related to nest parentheses, brackets, and escaped brackets. However, you could probably make a short, but >10 line code method to check for nested parentheses and/or brackets by keeping track of how many parentheses/brackets you've seen (ie. [this implementation from class](https://github.com/sprestrelski/test-markdownparse/blob/master/MarkdownParse.java)). For escaped brackets, you could add a few lines that check if brackets are proceeded by a backslash. If so, then move to the next bracket.

## Snippet 3
Expected Output: [https://ucsd-cse15l-w22.github.io/]  

Test in `MarkdownParseTest.java` 
```java
@Test
    public void testMultiLineLinks() throws IOException{
        List<String> expect = List.of("https://ucsd-cse15l-w22.github.io/");
        Path fileName = Path.of("lab8_3.md");
        String contents = Files.readString(fileName);
        assertEquals(expect, MarkdownParse.getLinks(contents));
    }
```

My implementation output, where my test failed:
```java
3) testMultiLineLinks(MarkdownParseTest)
java.lang.AssertionError: expected:<[https://ucsd-cse15l-w22.github.io/]> but was:<[
    https://www.twitter.com
,
    https://ucsd-cse15l-w22.github.io/
, github.com

And there's still some more text after that.

[this link doesn't have a closing parenthesis for a while](https://cse.ucsd.edu/



, https://cse.ucsd.edu/



]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testMultiLineLinks(MarkdownParseTest.java:79)
```

Reviewed implementation output, where my test failed:
```java
3) testMultiLineLinks(MarkdownParseTest)
java.lang.AssertionError: expected:<[https://ucsd-cse15l-w22.github.io/]> but was:<[]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testMultiLineLinks(MarkdownParseTest.java:103)
```

Code change:  
I think there is a <10 line code change that could at least account for line breaks. I am not sure if there is a small code change that could make my program work for all cases related to newlines in brackets and parentheses.  

My program does not check for newlines, so the output contains all of the line breaks in the links as long as they're between parentheses pairs. According to the preview from [CommonMark](https://spec.commonmark.org/dingus/) looks like a single newline is okay between the brackets, but not in the link itself. So, we would only need to check if there were stacked newlines ("\n\n").
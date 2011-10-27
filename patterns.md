---
layout: default
title: Patterns
subtitle: A list of regular expressions to extract R code and chunk options from the input document
---

The [object](objects) `kpat` manages patterns in **knitr**. For example, we can use `kpat$get()` to check the current pattern list. A pattern list includes the following components:

- `chunk.begin`: the pattern for the beginning of a code chunk; it must contain a group defined by `()`, which will be used to extract chunk options
- `chunk.end`: the pattern for the end of a chunk
- `chunk.code`: the pattern to be used to extract R code from a chunk by removing characters of this pattern
- `inline.code`: the pattern to be used to extract the pieces of R code mixed inline with other texts (i.e. those which are not in separate code chunks); like `chunk.begin`, it must contain a group 
- `global.options`: the pattern to extract global options for chunks (options are extracted like those in `chunk.begin` but they permanently change global options, whereas chunk options only locally affect a specific chunk)
- `header.begin`: the pattern to find out where the document header begins; this is used to insert some header information into the output document (e.g. commands in the preamble in LaTeX, or CSS styles in HTML)
- `document.begin`: the pattern to find out where the body of the document begins (currently only used to externalize tikz graphics)

Patterns that are `NULL` will not be matched.

Like Sweave, there are two types of R code in **knitr**: code chunks (like paragraphs) and inline R code which will be executed within text lines. For chunks in a document, options are of the form `label, opt1=TRUE, opt2=FALSE, opt3=character.string` (connected by `,` and `=`; only the chunk label does not need to have a `value`, since it is implicitly the `value` of the chunk option `label`).

## Built-in Patterns

There are several built-in pattern lists in **knitr** which are stored in `optk$get('all.patterns')`.

{% highlight r %}
library(knitr)
apats = optk$get("all.patterns")  # a list of all built-in patterns
str(apats)
{% endhighlight %}
{% highlight text %}
## List of 4
##  $ rnw :List of 6
##   ..$ chunk.begin   : chr "^<<(.*)>>="
##   ..$ chunk.end     : chr "^@\\s*$"
##   ..$ inline.code   : chr "\\\\Sexpr\\{([^\\}]*)\\}"
##   ..$ global.options: chr "\\\\SweaveOpts\\{([^\\}]*)\\}"
##   ..$ header.begin  : chr "^\\s*\\\\documentclass[^\\}]+\\}"
##   ..$ document.begin: chr "^\\s*\\\\begin\\{document\\}"
##  $ brew:List of 1
##   ..$ inline.code: chr "<%[=]{0,1}\\s+(.*)[\\s-]+%>"
##  $ tex :List of 7
##   ..$ chunk.begin   : chr "^%+\\s*begin.rcode\\s*(.*)"
##   ..$ chunk.end     : chr "^%+\\s*end.rcode"
##   ..$ chunk.code    : chr "^%+"
##   ..$ global.options: chr "%+\\s*roptions\\s*([^\n]*)"
##   ..$ inline.code   : chr "\\\\rinline\\{([^\\}]*)\\}"
##   ..$ header.begin  : chr "^\\s*\\\\documentclass[^\\}]+\\}"
##   ..$ document.begin: chr "^\\s*\\\\begin\\{document\\}"
##  $ html:List of 5
##   ..$ chunk.begin   : chr "^<!--\\s*begin.rcode\\s*(.*)"
##   ..$ chunk.end     : chr "^\\s*end.rcode\\s*-->"
##   ..$ inline.code   : chr "<!--\\s*rinline\\s*(.*)\\s*-->"
##   ..$ global.options: chr "<!--\\s*roptions\\s*(.*)\\s*-->"
##   ..$ header.begin  : chr "^\\s*<head>"
{% endhighlight %}


Depending on the extension of the input filename, **knitr** will automatically choose a pattern list from the above lists, e.g. `file.Rnw` will use `apats$rnw`, and `file.html` will use `apats$html`, etc.
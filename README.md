# Webmention 0.1

Webmention is a simple way to automatically notify any URL when you link to it on your site. From the receivers perspective, it's a way to request notification when other sites link to it.

It’s a modern alternative to [Pingback](http://www.hixie.ch/specs/pingback/pingback) and other forms of [Linkback](http://en.wikipedia.org/wiki/Linkback).

### Versions

#### Latest Version:
http://webmention.org

#### Draft Versions:
* [version 0.2](0.2.md)

### Editors
* Sandeep Shetty (sandeep.io sandeep.shetty@gmail.com)

### Contributors
* Aaron Parecki (aaronparecki.com aaron@parecki.com)
* Barnaby Walters (waterpigs.co.uk)
* ...

### Copyright

<a rel="license" href="http://creativecommons.org/publicdomain/zero/1.0/">
<img alt="CC0" src="http://i.creativecommons.org/p/zero/1.0/80x15.png">
</a> To the extent possible under law, the editors have waived 
all copyright and related or neighboring rights to this work. 
In addition, as of 2013-09-27, the editors have made 
this specification available under the <a rel="license" href="http://www.openwebfoundation.org/legal/the-owf-1-0-agreements/owfa-1-0">Open Web Foundation Agreement Version 1.0</a>, which is available at <http://www.openwebfoundation.org/legal/the-owf-1-0-agreements/owfa-1-0>.

## Introduction

Here's a typical webmention flow:

1. Alice posts some interesting content on her site (which is setup to receive webmentions).
2. Bob sees this content and comments about it on his site, linking back to Alice's original post.
3. Using webmention, Bob's publishing software automatically notifies Alice's server that her post has been linked to along with the URL to Bob's post.
4. Alice's publishing software verifies that Bob's post actually contains a link to her post and then includes this information on her site.

# Implementations
See [IMPLEMENTATIONS](IMPLEMENTATIONS.md)

## See also

* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [RESTful Pingback](http://www.w3.org/wiki/Pingback)
* [Semantic Pingback](http://aksw.org/projects/semanticpingback)
* [TalkBack](http://elie.im/publication/reclaiming-the-blogosphere-talkBack-a-secure-linkBack-protocol-for-weblogs#.UIWq_k4geoM)


## Let's collaborate
Feel free to [file an issue](issues) if you have feedback/questions/suggestions.


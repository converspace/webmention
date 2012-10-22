# WebMention Specification

A modern alternative to [Pingback](http://www.hixie.ch/specs/pingback/pingback).


## Protocol Flow

### Sender discovers Receiver Endpoint

```
> GET /bob/post/2 HTTP/1.1
> Host: bobs.host


< HTTP/1.1 200 OK
< Link: <http://bobs.host/webmention-endpoint>; rel="http://webmention.org/"
< ...
<
< <html>
< ...
< <link href="http://bobs.host/webmention-endpoint" rel="http://webmention.org/" />
< ...
```




### Sender Notifies Receiver

```
> POST /webmention-endpoint HTTP/1.1
> Host: bobs.host
> Content-Type: application/x-www-url-form-encoded
>
> source=http://alices.host/alice/post/42&\
> target=http://bobs.host/bob/post/2
```

_Note: the "\" character is used here to indicate line wrapping in the request content and is not part of the content itself._

`bobs.host` should check that `target` is a valid resource belonging to it and then perform a `GET` on `source` and confirm that it actually links to `target`.

## Preventing Spam
* WebMention receivers SHOULD moderate webmentions and MUST link to `source` with `rel="nofollow"`.
* Receivers could also periodically reverify that `source` links to `target`.


## See also

* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [RESTful Pingback](http://www.w3.org/wiki/Pingback)
* [Semantic Pingback](http://aksw.org/projects/semanticpingback)

## Let's collaborate
Feel free to [file an issue](https://github.com/converspace/webmention-specification/issues) if you have feedback/questions/suggestions.
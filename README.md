# WebMention 0.1

A modern alternative to [Pingback](http://www.hixie.ch/specs/pingback/pingback).


## Editors
* Sandeep Shetty (sandeep.shetty@gmail.com)

## Contritbutors
* Aaron Parecki (aaron@parecki.com)

__NOTE__: All contributions to this specification are released into the public domain.


## Protocol Flow

### Sender discovers Receiver Endpoint

```http
GET /bob/post/2 HTTP/1.1
Host: bobs.host
```
```http
HTTP/1.1 200 OK
Link: <http://bobs.host/webmention-endpoint>; rel="http://webmention.org/"

<html>
...
<link href="http://bobs.host/webmention-endpoint" rel="http://webmention.org/" />
...
```


### Sender Notifies Receiver

```http
POST /webmention-endpoint HTTP/1.1
Host: bobs.host
Content-Type: application/x-www-url-form-encoded

source=http://alices.host/alice/post/42&
target=http://bobs.host/bob/post/2
```
```http
HTTP/1.1 202 Accepted
```

### Verification
`bobs.host` SHOULD check that `target` is a valid resource belonging to it and then perform a `GET` on `source` and confirm that it actually links to `target`.

**Note**: Might look for [rel="in-reply-to"](http://microformats.org/wiki/comment-brainstorming#hAtom_and_in-reply-to), or just the link itself somewhere on the page.

## Response

### Success

If the WebMention request was successful, the server MUST reply with an `HTTP 202 Accepted` response code. The body of the response is left undefined, but is recommended to return a message indicating the pingback was successful and should respect the content-type of the `Accept` header. If no `Accept` header is present, an HTML success body may be returned. It is also valid to not send any body in the response.

For example, typical responses may look like the following:

#### HTML

```http
POST /webmention-endpoint HTTP/1.1
Host: bobs.host
Content-Type: application/x-www-url-form-encoded

source=http://alices.host/alice/post/42&
target=http://bobs.host/bob/post/2
```
```http
HTTP/1.1 202 Accepted
Content-Type: text/html

<!DOCTYPE html>
<html>
  <head>
    <title>WebMention</title>
  </head>
  <body>
	<p>WebMention was successful</p>
  </body>
</html>
```

#### JSON

```http
POST /webmention-endpoint HTTP/1.1
Host: bobs.host
Content-Type: application/x-www-url-form-encoded
Accept: application/json

source=http://alices.host/alice/post/42&
target=http://bobs.host/bob/post/2
```
```http
HTTP/1.1 202 Accepted
Content-Type: application/json

{
  "result": "WebMention was successful"
}
```


### Errors

In addition to the standard HTTP status codes that may be returned for things like malformed requests, WebMention defines several error cases that must be handled.

All errors below MUST be returned with an `HTTP 400 Bad Request` response code.

* `source_not_found`: The source URI does not exist.
* `target_not_found`: The target URI does not exist. This must only be used when an external GET on the target URI would result in an `HTTP 404` response.
* `target_not_supported`: The specified target URI is not a WebMention-enabled resource. For example, on a blog, individual post pages may be WebMention-enabled but the home page may not.
* `no_link_found`: The source URI does not contain a link to the target URI.
* `already_registered`: The specified WebMention has already been registered.

The format of the error body should respect the content-type in the `Accept` header. If no `Accept` header is present, it is acceptable to return an HTML body with a description of the error.

#### HTML

```http
POST /webmention-endpoint HTTP/1.1
Host: bobs.host
Content-Type: application/x-www-url-form-encoded

source=http://alices.host/alice/post/42&
target=http://bobs.host/bob/post/2
```
```http
HTTP/1.1 400 Bad Request
Content-Type: text/html

<!DOCTYPE html>
<html>
  <head>
    <title>WebMention Error</title>
  </head>
  <body>
    <h2>no_link_found</h2>
    <p>The source URI does not contain a link to the target URI</p>
  </body>
</html>
```

#### JSON

```http
POST /webmention-endpoint HTTP/1.1
Host: bobs.host
Content-Type: application/x-www-url-form-encoded
Accept: application/json

source=http://alices.host/alice/post/42&
target=http://bobs.host/bob/post/2
```
```http
HTTP/1.1 400 Bad Request

{
  "error": "no_link_found",
  "error_description": "The source URI does not contain a link to the target URI"
}
```



## Preventing Spam and Abuse
* The verification process SHOULD be queued to prevent DDOS attacks.
* WebMention receivers SHOULD moderate WebMentions, and if a link is displayed back to the source, SHOULD link to `source` with `rel="nofollow"` to prevent spam.
* Receivers MAY periodically re-verify that `source` links to `target`, and remove visible links if source no longer links to target.



## See also

* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [RESTful Pingback](http://www.w3.org/wiki/Pingback)
* [Semantic Pingback](http://aksw.org/projects/semanticpingback)
* [TalkBack](http://elie.im/publication/reclaiming-the-blogosphere-talkBack-a-secure-linkBack-protocol-for-weblogs#.UIWq_k4geoM)

## Let's collaborate
Feel free to [file an issue](https://github.com/converspace/webmention/issues) if you have feedback/questions/suggestions.

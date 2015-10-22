# Webmention 0.2 (RC1)

Webmention is a simple way to automatically notify any URL when you link to it on your site. From the receivers perpective, it's a way to request notification when other sites link to it.

It’s a modern alternative to [Pingback](http://www.hixie.ch/specs/pingback/pingback) and other forms of [Linkback](http://en.wikipedia.org/wiki/Linkback).

Note that the latest, most up-to-date specification is located [on the IndieWebCamp wiki](http://indiewebcamp.com/webmention).

### Versions

#### Latest Version:
http://indiewebcamp.com/webmention

#### Previous Versions:
* [version 0.1](https://github.com/converspace/webmention/blob/c9ab07947d00656237d9a5e626c78148da7166eb/README.md)


### Editors
* [Sandeep Shetty](http://sandeep.io/) (sandeep.shetty@gmail.com)

### Contributors
* [Aaron Parecki](http://aaronparecki.com/) (aaron@parecki.com)
* [Barnaby Walters](http://waterpigs.co.uk/)


### License

[CC0](http://creativecommons.org/choose/zero/)+[OWFa](http://www.openwebfoundation.org/legal/the-owf-1-0-agreements/owfa-1-0)

<a rel="license" href="http://creativecommons.org/publicdomain/zero/1.0/"><img alt="CC0" src="http://i.creativecommons.org/p/zero/1.0/80x15.png"></a> To the extent possible under law, the editors and contributors have waived all copyright and related or neighboring rights to this work. In addition, as of 27 September 2013, the editors and contributors have made this specification available under the <a rel="license" href="http://www.openwebfoundation.org/legal/the-owf-1-0-agreements/owfa-1-0">Open Web Foundation Agreement Version 1.0</a>.

## Introduction

Here's a typical webmention flow:

1. Alice posts some interesting content on her site (which is setup to receive webmentions).
2. Bob sees this content and comments about it on his site, linking back to Alice's original post.
3. Using webmention, Bob's publishing software automatically notifies Alice's server that her post has been linked to along with the URL to Bob's post.
4. Alice's publishing software verifies that Bob's post actually contains a link to her post and then includes this information on her site.


## Protocol Flow

### Sender discovers Receiver Endpoint

```http
GET /post-by-alice HTTP/1.1
Host: alice.host
```
```http
HTTP/1.1 200 OK
Link: <http://alice.host/webmention-endpoint>; rel="webmention"

<html>
<head>
...
<link href="http://alice.host/webmention-endpoint" rel="webmention" />
...
</head>
<body>
....
<a href="http://alice.host/webmention-endpoint" rel="webmention" />
...
</body>
</html>
```

The webmention endpoint is advertised in the HTTP Link header or a `<link>` or `<a>` element with `rel="webmention"`. If more than one of these is present, the HTTP Link header takes precedence, followed by the `<link>` element, and finally the `<a>` element. Clients MUST support all three options and fall back in this order.

### Sender Notifies Receiver

```http
POST /webmention-endpoint HTTP/1.1
Host: alice.host
Content-Type: application/x-www-url-form-encoded

source=http://bob.host/post-by-bob&
target=http://alice.host/post-by-alice
```
```http
HTTP/1.1 202 Accepted

http://alice.host/webmentions/222
```

`202 Accepted` is the recommended status code to return indicating that the request SHOULD be queued and processed asynchronously to prevent __DoS attacks__. The response body SHOULD include a URL that can be used to monitor the status of the request.

If you choose to process the request and perform the [verification](#verification) step synchronously, you can respond with a `200 OK` status on success.

See [Error Responses](#error-responses) for what to do when the webmention is not successful.



### Verification
1. The receiver SHOULD check that `target` is a valid resource belonging to it and that it accepts webmentions.
2. The receiver SHOULD perform a HTTP `GET` request on `source` to confirm that it actually links to `target` (note that the receiver will need to check the `Content-Type` of the entity returned by `source` to make sure it is a textual response).

At this point the receiver can choose to publish information about this webmention along with any other data it picks up from `source`.


#### Error Responses

##### Sender Error

If the webmention was not successful because of something the sender did, you SHOULD return a `400 Bad Request` status code and MAY include a description of the error in the response body.

Possible sender related errors (from the [Pingback](http://www.hixie.ch/specs/pingback/pingback) specification):
* Source URL not found.
* Specified target URL not found.
* Source URL does not contain a link to the target URL.
* Specified target URL does not accept webmentions.

##### Receiver Error

If the webmention was not successful because of an error on the receivers server, it SHOULD return a `500 Internal Server Error` status code and MAY include a description of the error in the response body.


#### Updating existing webmentions
If receiver had received a webmention in the past with the same `source` and `target` then,
* If both the [verification](#verification) steps are successful, it SHOULD update any existing data it picked from `source` for the existing webmention.
* If it received a 410 on step 2 (performing a `GET` request on `source`) or does not find a link to `target` on `source`, it SHOULD delete the existing webmention.


## Preventing Abuse
* The verification process SHOULD be queued and processed asynchronously to prevent DDoS attacks.
* Receivers SHOULD moderate Webmentions, and if a link is displayed back to the source, SHOULD link to `source` with `rel="nofollow"` to prevent spam.
* Receivers MAY periodically re-verify webmentions and [update them](#updating-existing-webmentions).
* If a receiver chooses to publish data it picks up from `source`, it should ensure that the data is encoded and/or filtered to prevent XSS and CSRF attacks.


# Implementations
See [IMPLEMENTATIONS](IMPLEMENTATIONS.md)

# TODO
* Prevention of DDoS 
  * Malicious attacker could send webmentions to a lot of sites with Alice's site as `source` which will result in a DDoS on Alice's site.
    * See [this discussion about Refback](http://krijnhoetmer.nl/irc-logs/whatwg/20111122#l-387). tl;dr: Hixie says "it's already pretty trivial to cause a server to get a lot of GETs, that's not a particularly interesting security issue imho".
* backcompat with v0.1 by also supporting rel="http://webmention.org"?
 
  
## See also

* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [RESTful Pingback](http://www.w3.org/wiki/Pingback)
* [Semantic Pingback](http://aksw.org/projects/semanticpingback)
* [TalkBack](http://elie.im/publication/reclaiming-the-blogosphere-talkBack-a-secure-linkBack-protocol-for-weblogs#.UIWq_k4geoM)


## Let's collaborate
Feel free to [file an issue](https://github.com/converspace/webmention/issues) if you have feedback/questions/suggestions.


# Webmention

A modern alternative to [Pingback](http://www.hixie.ch/specs/pingback/pingback).

Webmention is a simple way to automatically notify any URL when you link to it on your site. From the receivers perpective, it's a way to request notification when other sites link to it.


## Editors
Sandeep Shetty (sandeep.shetty@gmail.com)

__NOTE__: All contributions to this specification are released into the public domain.


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
Link: <http://alice.host/webmention-endpoint>; rel="http://webmention.org/"

<html>
...
<link href="http://alice.host/webmention-endpoint" rel="http://webmention.org/" />
...
```


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

`202 Accepted` is the recommended status code to return indicating that the request SHOULD be queued and processed asynchronously to prevent __DDOS attacks__. The response body SHOULD include a URL that can be used to monitor the status of the request.

If you choose to process the request and perform the [verification](#verification) step synchronously, you can respond with a `200 OK` status on success.

See [Error Responses](#error-responses) for what to do when the webmention is not successful.



### Verification
1. The receiver SHOULD check that `target` is a valid resource belonging to it and that it accepts webmentions.
2. The receiver SHOULD perform a HTTP `GET` request on `source` to confirm that it actually links to `target` (note that the receiver will need to check the `Content-Type` of the entity returned by `source` to make sure it is a textual response).

At this point the receiver can choose to publish information about this webmention along with any other data it picks up from `source`.


#### Error Responses

##### Sender Error

If the webmention was not successful because of something the `sender` did, you SHOULD return a `400 Bad Request` status code and MAY include a simple plain text description of the error in the response body.

Possible `sender` related errors (from the [Pingback](http://www.hixie.ch/specs/pingback/pingback) specification):
* Source URL not found.
* Specified target URL not found.
* Source URL does not contain a link to the target URL.
* Specified target URL does not accept webmentions.

```http
POST /webmention-endpoint HTTP/1.1
Host: alice.host
Content-Type: application/x-www-url-form-encoded

source=http://bob.host/post-by-bob&
target=http://alice.host/post-by-alice
```
```http
HTTP/1.1 400 Bad Request

Source URL does not contain a link to the target URL.
```

##### Receiver Error

If the webmention was not successful because of an error on the receivers server, it SHOULD return a `500 Internal Server Error` status code and MAY include a simple plain text description of the error in the response body.

```http
POST /webmention-endpoint HTTP/1.1
Host: alice.host
Content-Type: application/x-www-url-form-encoded

source=http://bob.host/post-by-bob&
target=http://alice.host/post-by-alice
```
```http
HTTP/1.1 500 Internal Server Error

Cannot processes webmentions at this time. Please try again later.
```

#### Updating existing webmentions
If receiver had received a webmention in the past with the same `source` and `target` then,
* If both the [verification](#verification) steps are successful, it SHOULD update any existing data it picked from `source` for the existing webmention.
* If it failed on step 2, that is, it got a 404 while performing a `GET` request on `source` or does not find a link to `target`, it SHOULD delete the existing webmention.



## Preventing Abuse
* The verification process SHOULD be queued and processed asynchronously to prevent DDOS attacks.
* Receivers SHOULD moderate Webmentions, and if a link is displayed back to the source, SHOULD link to `source` with `rel="nofollow"` to prevent spam.
* Receivers MAY periodically re-verify webmentions and [update them](#updating-existing-webmentions).
* If a `receiver` chooses to publish data it picks up from `source`, it should ensure that the data is encoded and/or filtered to prevent XSS and CSRF attacks.


# Implementations
...

## See also

* [Pingback](http://www.hixie.ch/specs/pingback/pingback)
* [Trackback](http://archive.cweiske.de/trackback/trackback-1.2.html)
* [RESTful Pingback](http://www.w3.org/wiki/Pingback)
* [Semantic Pingback](http://aksw.org/projects/semanticpingback)
* [TalkBack](http://elie.im/publication/reclaiming-the-blogosphere-talkBack-a-secure-linkBack-protocol-for-weblogs#.UIWq_k4geoM)


## Let's collaborate
Feel free to [file an issue](https://github.com/converspace/webmention/issues) if you have feedback/questions/suggestions.


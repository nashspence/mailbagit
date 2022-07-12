---
layout: page
title: WARC Derivatives
permalink: /warcs/
parent: Using mailbagit
nav_order: 9
---

# WARC Derivatives

`mailbagit` can create WARC derivatives for email messages. This is a bit of an experimental feature in that using web archives for email isn't common and there is no concensus yet on how these WARC should be strutured. Still, since WARCs do a good job of both preserving how messages are displayed as well as maintaining email messages as data, they can be really useful for email preservation. Thus, we're including this feature as an example for users to try out and experiment with. If you have thoughts or feedback on this, please [submit an issue](https://github.com/UAlbanyArchives/mailbagit/issues/new/choose)!

WARCs can be more challenging to use that typical files, as you need software to "replay" them. [ReplayWeb.Page](https://replayweb.page/) is an easy method, and you can checkout the [Web Archiving Awesome List](https://github.com/iipc/awesome-web-archiving#replay) for more. The WARC files created by `mailbagit` can also be small enough to view in a text editor. The files are compressed with gzip, so you just have to extract them to plain text.


## What's included in WARC derivatives

`mailbagit` creates a compressed WARC file for each email message. Each WARC includes:

* an HTML message body as `body.html` (or plain text if HTML is not present)
* a `headers.json` file with a complete set of email headers as a response record
* a `headers.json` file with a complete set of email headers as a metadata record
* All message attachments
* All externally-hosted resources such as images, audio, video, or CSS in these tags:
	* `<img>`
	* `<link>`
	* `<object>`
	* `<source>`
* Optionally, if the `-l`/`--external-links` argument is provided, `mailbagit` will also crawl and include `<a>` links contained in the message body as well as the externally-hosted resources for those pages with these tags:
	* `<img>`
	* `<link>`
	* `<object>`
	* `<source>`
	* `<script>`
	* `<iframe>`
	* `<embed>`
	* `<input>`
	* `<track>`

## WARC-Target-URI

Since email messages are not publically available on the web, they don't really have URLs like are typically used for [WARC-Target-URI](https://iipc.github.io/warc-specifications/specifications/warc-format/warc-1.1/#warc-target-uri). While a messages's [Message-ID](https://en.wikipedia.org/wiki/Message-ID) could potentially be useful, we have found cases where his headers was stripped or otherwise missing in email exports.

This field is important, as WARCs and replay software use WARC-Target-URI to arrange and provide access to web archives. For example, the Wayback Machine's calendar interface relies on WARC-Target-URIs, as "https://library.albany.edu" is the WARC-Target-URI in [https://web.archive.org/web/20220417153716/https://library.albany.edu/](https://web.archive.org/web/20220417153716/https://library.albany.edu/).

Since WARC-Target-URI is fundamentally necessary to use WARC files, this makes `mailbagit`'s WARC implementation a bit awkward. We decided to use a standard root URI for each WARC record that is derived from an email message: `http://mailbag/[Mailbag-Message-ID]/`. This is problematic, as the URIs are not unique outside of a mailbag, but we didn't really have a better idea. If you have feedback or suggestions, [let us know](https://github.com/UAlbanyArchives/mailbagit/issues/new/choose)!

Thus, these are could be common WARC-Target-URIs generated by `mailbagit`:

```
http://mailbag/39/body.html
http://mailbag/39/headers.json
http://mailbag/39/attachmentFilename.pdf
```

## 400s and other crawler blocks

`mailbagit` uses Python's `requests` package to crawl externally-hosted resources and include them in WARC derivatives. This is the same method commonly used by web scrapers, which means that many websites try to block crawlers like this. So you may experience HTTP 400 or other blocks, particulatly when using the `-l`/`--external-links` option. These will be documented in the [warnings report]({{ site.baseurl }}/errors/). sWe're hoping to be able to improve web crawling in the future.
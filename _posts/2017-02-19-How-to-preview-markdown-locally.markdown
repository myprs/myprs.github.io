---
layout: post
title:  "How to preview markdown locally?"
date:   2017-02-19 13:17:0 +0100
categories: TipsAndTricks
excerpt_separator: <!--more-->
---

One problem I did experience when working with markdown and github was to preview the markdown, I just produced. I must admit that I'm not too comfortable with markdown syntax. So I fail often at first (and even second and later) edit. Without being able to preview the markdown loacally, this quickly leads to lots of commits, just dealing with minor (and even still faulty) updates e.g. on the README.md file. But there are some different approaches to deal with this. A good list of alternatives is on the [ homepage of the Livedown editor plugin][Livedown]. Go for the "Alternatives" chapter. 



I did decide to use [Grip] for the following reasons:
<!--more-->

 * editor independent, because being a standalone application, in contrast editor plugins,
 * supporting relative URLs (this means that following links in the hirarchy should work)

So install by 

```
$ pip install grip
```
Unfortunately there is no Ubuntu/Debian package [afaik], so staying up to date is a pain (see [here][pippain01] and [here][pippain02] ).

Run by 
```
$ cd myrepo
$ grip myfile.md
 * Running on http://localhost:6419/
```
There are lots of other options, so visit [Grip Homepage][Grip] for mor information.

[afaik]: https://en.wiktionary.org/wiki/AFAIK
[Livedown]: https://github.com/shime/livedown
[Grip]: https://github.com/joeyespo/grip
[pippain01]: https://github.com/pypa/pip/issues/59
[pippain02]: https://github.com/pypa/pip/issues/5://github.com/pypa/pip/issues/3819

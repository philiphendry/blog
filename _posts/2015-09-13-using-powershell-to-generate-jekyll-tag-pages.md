---
author: Philip Hendry
title: Using PowerShell to generate Jekyll tag pages
date: 2015-09-13 17:06
comments: true
layout: post
tags:
- powershell
---
I'm hosting this blog on [GitHub](https://github.com/) using [Jekyll](https://jekyllrb.com/) which is a fantastic free combination. But with anything that's free there are some drawbacks. Primarily it's the inability to use custom Jekyll plugins in GitHub.

I want to keep this combination so I've been following an article that describes [how to generate tag pages](http://www.minddust.com/post/tags-and-categories-on-github-pages/) without having a custom plug-in.

All is well-and-good, but for every tag that's been made, a static page must be created which lists all posts with that tag. This seemed like a pain to keep up-to-date and a perfect opportunity to test my PowerShell skills!

The first step is to iterate over the lines in the `.\_data\tags.yml` file which is in this format:

	net:
	  slug: dot-net
	  name: .Net
	android:
	  slug: android
	  name: Android
	angularjs:
	  slug: angularjs
	  name: AngularJS

The `Get-Content` PowerShell command will fetch the contents then I can filter only the lines I want with `Select-String` and a regular expression:

	get-content .\data\tags.yml | select-string "^[^ \t]"

That regular expression is simply ignoring any line that begins with either a space or a tab.

So now we have a list of lines like this:

	net:
	android:
	angularjs:

I'm going to need to get rid of the trailing colon and then I can use each keyword to create a string:

	... %{ $a = $_.Line.TrimEnd(':'); "--- `nlayout: blog_by_tag`ntag: $a`npermalink: /blog/tag/$a/`n---`n`n"

Here I'm:

* Using `%` which is an alias for `For-Each`
* Within the `For_each` body `$_` represents the current item
* The previous `Select-String` return a `MatchInfo` and a member on that object that returns the line containing the match is `Line`
* I'm using the .Net `TrimEnd()` to remove the trailing colon
* Assigning the result of the `TrimEnd()` to a variable `$a`
* I can then create a brand to string using double-quotes - and this is important since using double-quotes will use string interpolation and therefore any contained variables will be expanded. In other words `"$a"` becomes `the string data` if `$a = "the string data"` whilst `'$a'` will remain `$a`.
* In the string I'm outputting newlines with ``n` 

The final step is to write each of these strings to a new file basing the filename on the keyword stored in the variable `$a`:

    ... | Out-File -FilePath ".\blog\tag\$a.md" -Encoding ascii }

The annoying thing here is Jekyll wants UTF-8 encoded ]files but without a [BOM](https://en.wikipedia.org/wiki/Byte_order_mark) but this isn't easy with `Out-File` and so I've just used ascii instead which is fine for the moment.

So put all together this command is:

    get-content .\_data\tags.yml | 
    select-string "^[^ \t]" | 
    %{ 
	    $a = $_.Line.TrimEnd(':'); 
	    "---`nlayout: blog_by_tag`ntag: $a`npermalink: /blog/tag/$a/`n---`n`n" | 
	    Out-File -FilePath ".\blog\tag\$a.md" -Encoding ascii 
	}

The next improvement would be to iterate over the posts themselves, find all the unique tags and write the tags.yml file... but that's for another day.
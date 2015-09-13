---
author: philiphendry
comments: true
date: 2011-12-23 09:25:35+00:00
layout: post
slug: create-a-contents-page-with-page-numbers-from-html-input-using-websupergoo-abcpdf
title: Create a contents page with page numbers from html input using Websupergoo
  Abcpdf
wordpress_id: 370
tags:
- asp-net
---

I’ve created a report pdf from an html page output from our ASP.NET based product but I needed to change a contents list that was rendered as a hyperlink list on the page into a list of section headings and page number since the pdf was primarily for printing. The solution wasn’t immediately obvious but I’ve come up with the following which I was running in a unit test for simple quick prototyping. The key is using the HtmlOptions.AddTags property in ABCpdf which allows areas of the HTML to be retrieve during pdf rendering and modified.

```
using System.Diagnostics;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using WebSupergoo.ABCpdf8;

namespace TestPdfFormFields
{
	[TestClass]
	public class PdfPrototyping
	{
		struct Tag
		{
			public string name;
			public string rectString;
			public int pagenumber;
		}

		[TestMethod]
		public void testCreatingAPdfWithContentsPageFromHtml()
		{
			// Create Websupergoo ABCpdf document and set up the page size
			var theDoc = new Doc();
			theDoc.Rect.Inset(100, 100);
			theDoc.Rect.Top = 700;

			// The following HtmlOption instructs ABCpdf to interpret the style tags 'abcpdf-tag-visible' and
			// create a array of the id's take from the html and the rectangles representing the size of the
			// html element.
			theDoc.HtmlOptions.AddTags = true;

			// The basis of this solution therefore is to create placeholders where page numbers will be rendered
			// in the contents page and back-fill them once we know where the sections/chapters have been rendered
			// since it won't be known until ABCpdf has chained pages together.
			var theID = theDoc.AddImageHtml(
				@"
				<h1>Contents</h1>
				<ul>
					<li><span id='contents1' style='abcpdf-tag-visible: true; width: 20px;'></span>.......First paragraph</li>
					<li><span id='contents2' style='abcpdf-tag-visible: true; width: 20px;'></span>.......Second paragraph</li>
					<li><span id='contents3' style='abcpdf-tag-visible: true; width: 20px;'></span>.......Third paragraph</li>
				</ul>
				<h1 id='heading1' style='abcpdf-tag-visible: true; page-break-before:always;'>Section One</h1>
				<h1 id='heading2' style='abcpdf-tag-visible: true; page-break-before:always;'>Section Two</h1>
				<h1 id='heading3' style='abcpdf-tag-visible: true; page-break-before:always;'>Section Three</h1>
				");

			var tagCache = new List<Tag>();
			var pagenumber = 1;
			while (true)
			{
				// Fetch all the tags and rectangles and add them to a tagCache for the current theID. Chaining
				// creates a new theID which will contain more tags to add
				var tags = theDoc.HtmlOptions.GetTagIDs(theID);
				var tagRects = theDoc.HtmlOptions.GetTagRects(theID);
				tagCache.AddRange(tags.Select((t, i) => new Tag {name = t, pagenumber = pagenumber, rectString = tagRects[i].String}));

				if (!theDoc.Chainable(theID))
					break;

				theDoc.Page = theDoc.AddPage();
				theID = theDoc.AddImageToChain(theID);
				pagenumber++;
			}

			// Now we have a cache of all contents and heading tags we can iterate through the
			// contents tags, find the smallest page number of the corresponding section then
			// render the page number into the contents.
			theDoc.HPos = 1.0; // Right justify
			theDoc.VPos = 0.5; // Centre vertically
			theDoc.FontSize = 8;
			foreach (var tag in tagCache.Where(t => t.name.StartsWith("contents")))
			{
				var paragraphName = "heading" + tag.name.Substring("contents".Length);
				var paragraphPageNumber = tagCache.Where(t => t.name == paragraphName).Select(t => t.pagenumber).Min();

				theDoc.PageNumber = tag.pagenumber;
				theDoc.Rect.String = tag.rectString;
				theDoc.AddText(paragraphPageNumber.ToString());
			}

			// Now iterate through all the pages, add page numbers and flatten the layers.
			theDoc.Rect.String = "100 70 500 150";
			var pageCount = theDoc.PageCount;
			for (var pageNumber = 1; pageNumber <= pageCount; pageNumber++)
			{
				theDoc.PageNumber = pageNumber;
				theDoc.AddText("Page " + pageNumber + " of " + pageCount);
				theDoc.Flatten();
			}

			const string testFilename = @"c:\temp\HtmlOptionsGetTagRects.pdf";
			if (File.Exists(testFilename))
				File.Delete(testFilename);
			
			theDoc.Save(testFilename);
			Process.Start(testFilename);
		}
	}
}
```
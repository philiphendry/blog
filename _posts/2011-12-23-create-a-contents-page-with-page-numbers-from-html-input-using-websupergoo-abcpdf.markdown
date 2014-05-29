---
author: philiphendry
comments: true
date: 2011-12-23 09:25:35+00:00
layout: post
slug: create-a-contents-page-with-page-numbers-from-html-input-using-websupergoo-abcpdf
title: Create a contents page with page numbers from html input using Websupergoo
  Abcpdf
wordpress_id: 370
categories:
- ASP.NET
---

I’ve created a report pdf from an html page output from our ASP.NET based product but I needed to change a contents list that was rendered as a hyperlink list on the page into a list of section headings and page number since the pdf was primarily for printing. The solution wasn’t immediately obvious but I’ve come up with the following which I was running in a unit test for simple quick prototyping. The key is using the HtmlOptions.AddTags property in ABCpdf which allows areas of the HTML to be retrieve during pdf rendering and modified.

 

 

  

    
    
    <span style="color:#606060;" id="lnum1">   1:</span> <span style="color:#0000ff;">using</span> System.Diagnostics;




    
    
    <span style="color:#606060;" id="lnum2">   2:</span> <span style="color:#0000ff;">using</span> System.IO;




    
    
    <span style="color:#606060;" id="lnum3">   3:</span> <span style="color:#0000ff;">using</span> System.Collections.Generic;




    
    
    <span style="color:#606060;" id="lnum4">   4:</span> <span style="color:#0000ff;">using</span> System.Linq;




    
    
    <span style="color:#606060;" id="lnum5">   5:</span> <span style="color:#0000ff;">using</span> Microsoft.VisualStudio.TestTools.UnitTesting;




    
    
    <span style="color:#606060;" id="lnum6">   6:</span> <span style="color:#0000ff;">using</span> WebSupergoo.ABCpdf8;




    
    
    <span style="color:#606060;" id="lnum7">   7:</span>  




    
    
    <span style="color:#606060;" id="lnum8">   8:</span> <span style="color:#0000ff;">namespace</span> TestPdfFormFields




    
    
    <span style="color:#606060;" id="lnum9">   9:</span> {




    
    
    <span style="color:#606060;" id="lnum10">  10:</span>    [TestClass]




    
    
    <span style="color:#606060;" id="lnum11">  11:</span>    <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span> PdfPrototyping




    
    
    <span style="color:#606060;" id="lnum12">  12:</span>    {




    
    
    <span style="color:#606060;" id="lnum13">  13:</span>       <span style="color:#0000ff;">struct</span> Tag




    
    
    <span style="color:#606060;" id="lnum14">  14:</span>       {




    
    
    <span style="color:#606060;" id="lnum15">  15:</span>          <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">string</span> name;




    
    
    <span style="color:#606060;" id="lnum16">  16:</span>          <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">string</span> rectString;




    
    
    <span style="color:#606060;" id="lnum17">  17:</span>          <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">int</span> pagenumber;




    
    
    <span style="color:#606060;" id="lnum18">  18:</span>       }




    
    
    <span style="color:#606060;" id="lnum19">  19:</span>  




    
    
    <span style="color:#606060;" id="lnum20">  20:</span>          




    
    
    <span style="color:#606060;" id="lnum21">  21:</span>       [TestMethod]




    
    
    <span style="color:#606060;" id="lnum22">  22:</span>       <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span> testCreatingAPdfWithContentsPageFromHtml()




    
    
    <span style="color:#606060;" id="lnum23">  23:</span>       {




    
    
    <span style="color:#606060;" id="lnum24">  24:</span>          <span style="color:#008000;">// Create Websupergoo ABCpdf document and set up the page size</span>




    
    
    <span style="color:#606060;" id="lnum25">  25:</span>          var theDoc = <span style="color:#0000ff;">new</span> Doc();




    
    
    <span style="color:#606060;" id="lnum26">  26:</span>          theDoc.Rect.Inset(100, 100);




    
    
    <span style="color:#606060;" id="lnum27">  27:</span>          theDoc.Rect.Top = 700;




    
    
    <span style="color:#606060;" id="lnum28">  28:</span>  




    
    
    <span style="color:#606060;" id="lnum29">  29:</span>          <span style="color:#008000;">// The following HtmlOption instructs ABCpdf to interpret the style tags 'abcpdf-tag-visible' and</span>




    
    
    <span style="color:#606060;" id="lnum30">  30:</span>          <span style="color:#008000;">// create a array of the id's take from the html and the rectangles representing the size of the </span>




    
    
    <span style="color:#606060;" id="lnum31">  31:</span>          <span style="color:#008000;">// html element. </span>




    
    
    <span style="color:#606060;" id="lnum32">  32:</span>          theDoc.HtmlOptions.AddTags = <span style="color:#0000ff;">true</span>;




    
    
    <span style="color:#606060;" id="lnum33">  33:</span>  




    
    
    <span style="color:#606060;" id="lnum34">  34:</span>          <span style="color:#008000;">// The basis of this solution therefore is to create placeholders where page numbers will be rendered</span>




    
    
    <span style="color:#606060;" id="lnum35">  35:</span>          <span style="color:#008000;">// in the contents page and back-fill them once we know where the sections/chapters have been rendered</span>




    
    
    <span style="color:#606060;" id="lnum36">  36:</span>          <span style="color:#008000;">// since it won't be known until ABCpdf has chained pages together.</span>




    
    
    <span style="color:#606060;" id="lnum37">  37:</span>          var theID = theDoc.AddImageHtml(




    
    
    <span style="color:#606060;" id="lnum38">  38:</span>                   <span style="color:#006080;">@"




    
    
    <span style="color:#606060;" id="lnum39">  39:</span>                      <h1>Contents</h1>




    
    
    <span style="color:#606060;" id="lnum40">  40:</span>                      <ul>




    
    
    <span style="color:#606060;" id="lnum41">  41:</span>                         <li><span id='contents1' style='abcpdf-tag-visible: true; width: 20px;'></span>.......First paragraph</li>




    
    
    <span style="color:#606060;" id="lnum42">  42:</span>                         <li><span id='contents2' style='abcpdf-tag-visible: true; width: 20px;'></span>.......Second paragraph</li>




    
    
    <span style="color:#606060;" id="lnum43">  43:</span>                         <li><span id='contents3' style='abcpdf-tag-visible: true; width: 20px;'></span>.......Third paragraph</li>




    
    
    <span style="color:#606060;" id="lnum44">  44:</span>                      </ul>




    
    
    <span style="color:#606060;" id="lnum45">  45:</span>                      <h1 id='heading1' style='abcpdf-tag-visible: true; page-break-before:always;'>Section One</h1>




    
    
    <span style="color:#606060;" id="lnum46">  46:</span>                      <h1 id='heading2' style='abcpdf-tag-visible: true; page-break-before:always;'>Section Two</h1>




    
    
    <span style="color:#606060;" id="lnum47">  47:</span>                      <h1 id='heading3' style='abcpdf-tag-visible: true; page-break-before:always;'>Section Three</h1>




    
    
    <span style="color:#606060;" id="lnum48">  48:</span>                   "</span>);




    
    
    <span style="color:#606060;" id="lnum49">  49:</span>  




    
    
    <span style="color:#606060;" id="lnum50">  50:</span>          var tagCache = <span style="color:#0000ff;">new</span> List<Tag>();




    
    
    <span style="color:#606060;" id="lnum51">  51:</span>          var pagenumber = 1;




    
    
    <span style="color:#606060;" id="lnum52">  52:</span>          <span style="color:#0000ff;">while</span> (<span style="color:#0000ff;">true</span>)




    
    
    <span style="color:#606060;" id="lnum53">  53:</span>          {




    
    
    <span style="color:#606060;" id="lnum54">  54:</span>             <span style="color:#008000;">// Fetch all the tags and rectangles and add them to a tagCache for the current theID. Chaining</span>




    
    
    <span style="color:#606060;" id="lnum55">  55:</span>             <span style="color:#008000;">// creates a new theID which will contain more tags to add</span>




    
    
    <span style="color:#606060;" id="lnum56">  56:</span>             var tags = theDoc.HtmlOptions.GetTagIDs(theID);




    
    
    <span style="color:#606060;" id="lnum57">  57:</span>             var tagRects = theDoc.HtmlOptions.GetTagRects(theID);




    
    
    <span style="color:#606060;" id="lnum58">  58:</span>             tagCache.AddRange(tags.Select((t, i) => <span style="color:#0000ff;">new</span> Tag {name = t, pagenumber = pagenumber, rectString = tagRects[i].String}));




    
    
    <span style="color:#606060;" id="lnum59">  59:</span>  




    
    
    <span style="color:#606060;" id="lnum60">  60:</span>             <span style="color:#0000ff;">if</span> (!theDoc.Chainable(theID))




    
    
    <span style="color:#606060;" id="lnum61">  61:</span>                <span style="color:#0000ff;">break</span>;




    
    
    <span style="color:#606060;" id="lnum62">  62:</span>  




    
    
    <span style="color:#606060;" id="lnum63">  63:</span>             theDoc.Page = theDoc.AddPage();




    
    
    <span style="color:#606060;" id="lnum64">  64:</span>             theID = theDoc.AddImageToChain(theID);




    
    
    <span style="color:#606060;" id="lnum65">  65:</span>             pagenumber++;




    
    
    <span style="color:#606060;" id="lnum66">  66:</span>          }




    
    
    <span style="color:#606060;" id="lnum67">  67:</span>  




    
    
    <span style="color:#606060;" id="lnum68">  68:</span>          <span style="color:#008000;">// Now we have a cache of all contents and heading tags we can iterate through the</span>




    
    
    <span style="color:#606060;" id="lnum69">  69:</span>          <span style="color:#008000;">// contents tags, find the smallest page number of the corresponding section then</span>




    
    
    <span style="color:#606060;" id="lnum70">  70:</span>          <span style="color:#008000;">// render the page number into the contents.</span>




    
    
    <span style="color:#606060;" id="lnum71">  71:</span>          theDoc.HPos = 1.0;   <span style="color:#008000;">// Right justify</span>




    
    
    <span style="color:#606060;" id="lnum72">  72:</span>          theDoc.VPos = 0.5;   <span style="color:#008000;">// Centre vertically</span>




    
    
    <span style="color:#606060;" id="lnum73">  73:</span>          theDoc.FontSize = 8;




    
    
    <span style="color:#606060;" id="lnum74">  74:</span>          <span style="color:#0000ff;">foreach</span> (var tag <span style="color:#0000ff;">in</span> tagCache.Where(t => t.name.StartsWith(<span style="color:#006080;">"contents"</span>)))




    
    
    <span style="color:#606060;" id="lnum75">  75:</span>          {




    
    
    <span style="color:#606060;" id="lnum76">  76:</span>             var paragraphName = <span style="color:#006080;">"heading"</span> + tag.name.Substring(<span style="color:#006080;">"contents"</span>.Length);




    
    
    <span style="color:#606060;" id="lnum77">  77:</span>             var paragraphPageNumber = tagCache.Where(t => t.name == paragraphName).Select(t => t.pagenumber).Min();




    
    
    <span style="color:#606060;" id="lnum78">  78:</span>  




    
    
    <span style="color:#606060;" id="lnum79">  79:</span>             theDoc.PageNumber = tag.pagenumber;




    
    
    <span style="color:#606060;" id="lnum80">  80:</span>             theDoc.Rect.String = tag.rectString;




    
    
    <span style="color:#606060;" id="lnum81">  81:</span>             theDoc.AddText(paragraphPageNumber.ToString());




    
    
    <span style="color:#606060;" id="lnum82">  82:</span>          }




    
    
    <span style="color:#606060;" id="lnum83">  83:</span>  




    
    
    <span style="color:#606060;" id="lnum84">  84:</span>          <span style="color:#008000;">// Now iterate through all the pages, add page numbers and flatten the layers.</span>




    
    
    <span style="color:#606060;" id="lnum85">  85:</span>          theDoc.Rect.String = <span style="color:#006080;">"100 70 500 150"</span>;




    
    
    <span style="color:#606060;" id="lnum86">  86:</span>          var pageCount = theDoc.PageCount;




    
    
    <span style="color:#606060;" id="lnum87">  87:</span>          <span style="color:#0000ff;">for</span> (var pageNumber = 1; pageNumber <= pageCount; pageNumber++)




    
    
    <span style="color:#606060;" id="lnum88">  88:</span>          {




    
    
    <span style="color:#606060;" id="lnum89">  89:</span>             theDoc.PageNumber = pageNumber;




    
    
    <span style="color:#606060;" id="lnum90">  90:</span>             theDoc.AddText(<span style="color:#006080;">"Page "</span> + pageNumber + <span style="color:#006080;">" of "</span> + pageCount);




    
    
    <span style="color:#606060;" id="lnum91">  91:</span>             theDoc.Flatten();




    
    
    <span style="color:#606060;" id="lnum92">  92:</span>          }




    
    
    <span style="color:#606060;" id="lnum93">  93:</span>  




    
    
    <span style="color:#606060;" id="lnum94">  94:</span>          <span style="color:#0000ff;">const</span> <span style="color:#0000ff;">string</span> testFilename = <span style="color:#006080;">@"c:\temp\HtmlOptionsGetTagRects.pdf"</span>;




    
    
    <span style="color:#606060;" id="lnum95">  95:</span>          <span style="color:#0000ff;">if</span> (File.Exists(testFilename))




    
    
    <span style="color:#606060;" id="lnum96">  96:</span>             File.Delete(testFilename);




    
    
    <span style="color:#606060;" id="lnum97">  97:</span>          theDoc.Save(testFilename);




    
    
    <span style="color:#606060;" id="lnum98">  98:</span>          Process.Start(testFilename);




    
    
    <span style="color:#606060;" id="lnum99">  99:</span>       }




    
    
    <span style="color:#606060;" id="lnum100"> 100:</span>    }




    
    
    <span style="color:#606060;" id="lnum101"> 101:</span> }







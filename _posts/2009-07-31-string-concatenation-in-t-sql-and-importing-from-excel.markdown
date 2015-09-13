---
author: philiphendry
comments: true
date: 2009-07-31 10:34:58+00:00
layout: post
slug: string-concatenation-in-t-sql-and-importing-from-excel
title: String Concatenation in T-SQL and Importing from Excel
wordpress_id: 241
tags:
- sql-server
- t-sql
---

I keep forgetting how simple this little trick is so it’s time to blog it.

I wanted to write some T-SQL that would confirm that the columns just created by an import from an Excel spreadsheet into a temporary table contained the expected columns irrespective of order or other columns that exist in the file. I also wanted to be able to inform the user of the problem instead of just fail with a horrible error. I chose a fairly simple method, I would concatenate the columns from the temporary table (#tempImport in this case) and compare that with an expected string. If they don’t match I can report back to the user that the expected columns do not exist (in this implementation I’m not interested reporting which columns are missing.) The implementation I chose was this :




    
    select * into #tempImport
    from openrowset('Microsoft.Jet.OLEDB.4.0',
       'Excel 8.0;Database=e:\temp\aSpreadsheet.xls', Sheet1$)
    
    declare @columns varchar(512)
    set @columns = ''
    
    select @columns = @columns + ',' + [name]
    from tempdb.sys.columns
    where [object_id] = object_id('tempdb..#tempImport')
        and [name] in ('Store','Store Name','PSA1','PSA2','Mtrg')
    order by [name]
    
    if (@columns <> ',Mtrg,PSA1,PSA2,Store,Store Name')
        print 'The expected columns in the spreadsheet do not exist'





This is a brute-force type of implementation but I like it because it’s very simple and keeps everything in one place. There will be better ways to implement this but for the moment this fits the bill.

Update:

[http://support.microsoft.com/kb/326548](http://support.microsoft.com/kb/326548) is a useful guide to using the ISAM provider.

Update (20/08/2010)

[http://www.projectdmx.com/tsql/rowconcatenate.aspx](http://www.projectdmx.com/tsql/rowconcatenate.aspx) discusses concatenating rows in quite some detail.

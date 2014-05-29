---
author: philiphendry
comments: true
date: 2009-09-15 10:54:41+00:00
layout: post
slug: importing-from-excel-into-sql-server
title: Importing from Excel into SQL Server
wordpress_id: 287
categories:
- SQL Server
- T-SQL
---

I was going through a mountain of pain trying to get an import working from an Excel spreadsheet working into SQL Server 2005 but I finally managed it and here’s how…

 

Here’s the SQL I’m using (slightly abridged!) :

 

  
    
    <span style="color:#0000FF;">set</span><span style="color:#000000;"> </span><span style="color:#0000FF;">transaction</span><span style="color:#000000;"> </span><span style="color:#0000FF;">isolation</span><span style="color:#000000;"> </span><span style="color:#0000FF;">level</span><span style="color:#000000;"> </span><span style="color:#0000FF;">read</span><span style="color:#000000;"> </span><span style="color:#0000FF;">committed</span><span style="color:#000000;">
    
    </span><span style="color:#0000FF;">declare</span><span style="color:#000000;"> </span><span style="color:#008000;">@sql</span><span style="color:#000000;"> </span><span style="color:#0000FF;">nvarchar</span><span style="color:#000000;">(</span><span style="color:#800000;font-weight:bold;">1024</span><span style="color:#000000;">)
    </span><span style="color:#0000FF;">set</span><span style="color:#000000;"> </span><span style="color:#008000;">@sql</span><span style="color:#000000;"> </span><span style="color:#808080;">=</span><span style="color:#000000;"> </span><span style="color:#FF0000;">'</span><span style="color:#FF0000;">select * into ##Temp from opendatasource(</span><span style="color:#FF0000;">''</span><span style="color:#FF0000;">Microsoft.Jet.OLEDB.4.0</span><span style="color:#FF0000;">''</span><span style="color:#FF0000;">, </span><span style="color:#FF0000;">''</span><span style="color:#FF0000;">Extended Properties=Excel 8.0;Data Source=</span><span style="color:#FF0000;">'</span><span style="color:#000000;"> </span><span style="color:#808080;">+</span><span style="color:#000000;"> </span><span style="color:#008000;">@ExcelFileToImport</span><span style="color:#000000;"> </span><span style="color:#808080;">+</span><span style="color:#000000;"> </span><span style="color:#FF0000;">'''</span><span style="color:#FF0000;">)...</span><span style="color:#FF0000;">'</span><span style="color:#000000;"> </span><span style="color:#808080;">+</span><span style="color:#000000;"> </span><span style="color:#008000;">@ExcelWorkSheet</span><span style="color:#000000;">
    </span><span style="color:#0000FF;">exec</span><span style="color:#000000;"> sp_executesql </span><span style="color:#008000;">@sql</span>








Points to note :






  
  * I’m using dynamic sql because the opendatasource() doesn’t like being passed variables… so be sure to validate the contents of @ExcelFileToImport and @ExcelWorkSheet.


  
  * Because I was forced to use dynamic sql I had to use a global temporary table. I wanted to load the data into a temporary table so that the columns were generated on-the-fly and I could check the correct columns were supplied by examining tempdb.sys.columns. It therefore had to be a global temporary because a standard one would have been thrown away after sp_executesql() had finished! For my situation the global temporary table is ok… but you’ll have to consider your own situation and whether concurrency allows.


  
  * I kept getting the error below on the subsequent re-tries of uploading but worked around this by adding ‘set transaction isolation level read committed’ in the sproc.
      


      
_The requested operation could not be performed because OLE DB provider "Microsoft.Jet.OLEDB.4.0" for linked server "(null)" does not support the required transaction interface._





I was having trouble executing within a transaction but with the changes made above the sql could be called with the following :




    
    <span style="color:#0000FF;">using</span><span style="color:#000000;"> (TransactionScope scope </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TransactionScope())
    {
        UploadExcelIntoDatabase(importTicket, importFilename);
        scope.Complete();
    }
    </span>

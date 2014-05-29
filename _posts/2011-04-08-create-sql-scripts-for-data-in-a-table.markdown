---
author: philiphendry
comments: true
date: 2011-04-08 11:58:55+00:00
layout: post
slug: create-sql-scripts-for-data-in-a-table
title: Create SQL Scripts for Data in a table
wordpress_id: 352
categories:
- T-SQL
---

On occasion I want to get data from a table and into a script – particularly when I want to create a deployment script for standing data that needs to be migrated between servers. Rather than hand-craft it this last time I’ve created the script below which, when given the name of a table, generates a union’d list of select statements that represent the data. With a slight modification this list can then be selected into a temporary table then joined back to the original in an update/insert to create a script that automatically maintains the data.

 

There are some caveats to this script though :

 

  
  * You have to delete the first ‘union’ in the resulting SQL.
   
  * It doesn’t handle dates correctly and potentially some other data types – I did write this in a hurry!
 
```
declare @tablename nvarchar(max) 
set @tablename = 'testtable' 

if not exists ( select [TABLE_NAME] from INFORMATION_SCHEMA.TABLES where [TABLE_NAME] = @tablename ) 
begin 
	declare @message nvarchar(max) 
	set @message = 'The table ' + @tablename + ' does not exist' 
	raiserror ( @message, 18, 0);
	return; 
end 

declare @sql nvarchar(max) 
set @sql = '' 

declare @newline nvarchar(2) 
set @newline = char(13) + char(10) 

select @sql = @sql  
	+ case when ORDINAL_POSITION > 1 then ' + '', '' + ' else '' end  
	+ case when IS_NULLABLE = 'YES' then 'isnull(' else '' end 
	+ case when NUMERIC_SCALE is not null or DATA_TYPE = 'bit' then 'convert(nvarchar, ' else '' end 
	+ case when CHARACTER_MAXIMUM_LENGTH is not null then ' '''''''' + ' else '' end 
	+ '[' + isc.COLUMN_NAME + ']' 
	+ case when CHARACTER_MAXIMUM_LENGTH is not null then ' + ''''''''' else '' end 
	+ case when NUMERIC_SCALE is not null or DATA_TYPE = 'bit' then ')' else '' end 
	+ case when IS_NULLABLE = 'YES' then ', ''null'')' else '' end 
	+ ' + '' as [' + COLUMN_NAME + ']' + '''' 
	+ @newline 
from INFORMATION_SCHEMA.COLUMNS isc 
where 
	isc.TABLE_NAME = @tablename 
order by 
ORDINAL_POSITION 

set @sql = 'select ''union select '' + ' + @sql + ' from [' + @tablename + ']' 
exec sp_executesql @sql
```

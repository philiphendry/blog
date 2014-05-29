---
author: philiphendry
comments: true
date: 2011-02-21 13:38:32+00:00
layout: post
slug: grouping-multiple-consecutive-days-using-t-sql
title: Grouping Multiple Consecutive Days using T-sql
wordpress_id: 346
categories:
- T-SQL
---

I have a timesheet table that contains one entry per day per employee and against each entry is a reason flag which indicates whether the entry is for a holiday or sickness etc. I’ve written a SQL Server report that generates a list of the holidays that drills-down several levels to the actual holidays but I don’t want to list individual days but rather the holiday periods as a date from/to and numbers of days. With some inspirational thinking from [Rob Farley](http://stackoverflow.com/questions/1176011/sql-to-determine-minimum-sequential-days-of-access/1176255#1176255) I’ve created must the same query as his to merge the consecutive days and provide a total. Here’s the initial version :

 
```
set nocount on  
declare @Holidays as table ( PayrollNumber int not null, DateFrom datetime not null, DateTo datetime not null, NoOfDays int not null )  
insert into @Holidays values(14, '2010-02-08', '2010-02-08', 1.00) 
insert into @Holidays values(14, '2010-02-09', '2010-02-09', 1.00) 
insert into @Holidays values(14, '2010-02-10', '2010-02-10', 1.00) 
insert into @Holidays values(14, '2010-02-11', '2010-02-11', 1.00) 
insert into @Holidays values(14, '2010-02-12', '2010-02-12', 1.00) 
insert into @Holidays values(14, '2010-03-18', '2010-03-18', 1.00) 
insert into @Holidays values(14, '2010-03-19', '2010-03-19', 1.00) 
insert into @Holidays values(15, '2010-05-05', '2010-05-05', 1.00) 
insert into @Holidays values(15, '2010-05-06', '2010-05-06', 1.00) 
insert into @Holidays values(15, '2010-05-07', '2010-05-07', 1.00) 
insert into @Holidays values(15, '2010-06-10', '2010-05-10', 1.00) 
insert into @Holidays values(15, '2010-06-11', '2010-05-11', 1.00)  

select  PayrollNumber,  min(DateFrom) as DateFrom,  max(DateTo) as DateTo,  sum(NoOfDays) as DayCount 
from ( 
	select  *,  row_number() over (partition by PayrollNumber rder by DateFrom)  - cast(DateFrom as int) as ConsecutiveOffset 
	from @Holidays 
) x 
group by PayrollNumber, ConsecutiveOffset 
order by PayrollNumber, DateFrom
``` 


This produces the following result set :

```
PayrollNumber DateFrom 				  DateTo 			       DayCount 
------------- ----------------------- ----------------------- ----------- 
14 			  2010-02-08 00:00:00.000 2010-02-12 00:00:00.000 5 
14 			  2010-03-14 00:00:00.000 2010-03-17 00:00:00.000 4 
14 			  2010-03-18 00:00:00.000 2010-03-19 00:00:00.000 2 
15 			  2010-05-05 00:00:00.000 2010-05-07 00:00:00.000 3 
15 			  2010-06-05 00:00:00.000 2010-05-09 00:00:00.000 5 
15 			  2010-06-10 00:00:00.000 2010-05-11 00:00:00.000 2
```

Which is almost perfect. However, there is a slight complexity in the system I’m working on. Holidays are initially planned and appear in a different table. When the timesheet for today is generated the planned holiday will be copied to the timesheet where it can be altered if reality is slightly different. This means that when I generate a holiday report for the current holiday year I have to merge planned and actual records – the problem is actuals are booked as date ranges. However, the code above has a pleasant side effect and already merges single days appearing consecutively **before** a date range holiday. Since timesheets can never appear after planned holiday I don’t need to worry about merging single days that appear **after **a date range. For example, this sql :

```
insert into @Holidays values(14, '2010-02-08', '2010-02-08', 1.00) 
insert into @Holidays values(14, '2010-02-09', '2010-02-09', 1.00) 
insert into @Holidays values(14, '2010-02-10', '2010-02-10', 1.00) 
insert into @Holidays values(14, '2010-02-11', '2010-02-11', 1.00) 
insert into @Holidays values(14, '2010-02-12', '2010-02-12', 1.00) 
insert into @Holidays values(14, '2010-03-14', '2010-03-14', 1.00) -- Here's a date range that came from planned holidays and therefore should not be merged 
insert into @Holidays values(14, '2010-03-15', '2010-03-17', 4.00) 
insert into @Holidays values(14, '2010-03-18', '2010-03-18', 1.00) 
insert into @Holidays values(14, '2010-03-19', '2010-03-19', 1.00) 
insert into @Holidays values(15, '2010-05-05', '2010-05-05', 1.00) 
insert into @Holidays values(15, '2010-05-06', '2010-05-06', 1.00) 
insert into @Holidays values(15, '2010-05-07', '2010-05-07', 1.00) -- Here's a date range that came from planned holidays and therefore should not be merged 
insert into @Holidays values(15, '2010-06-05', '2010-05-09', 5.00) 
insert into @Holidays values(15, '2010-06-10', '2010-05-10', 1.00) 
insert into @Holidays values(15, '2010-06-11', '2010-05-11', 1.00)
```

produces this result :

```
PayrollNumber DateFrom 				  DateTo 				  DayCount	 
------------- ----------------------- ----------------------- ----------- 
14 			  2010-02-08 00:00:00.000 2010-02-12 00:00:00.000 5 
14 			  2010-03-14 00:00:00.000 2010-03-17 00:00:00.000 5 
14 			  2010-03-18 00:00:00.000 2010-03-19 00:00:00.000 2 
15 			  2010-05-05 00:00:00.000 2010-05-07 00:00:00.000 3 
15 			  2010-06-05 00:00:00.000 2010-05-09 00:00:00.000 5 
15 			  2010-06-10 00:00:00.000 2010-05-11 00:00:00.000 2
```

Where the date range 2010-03-15 to 2010-03-17 has been correctly merge with 2010-03-14 but not with 2010-03-18.

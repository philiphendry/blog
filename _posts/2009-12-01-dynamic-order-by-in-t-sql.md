---
author: philiphendry
comments: true
date: 2009-12-01 14:33:23+00:00
layout: post
slug: dynamic-order-by-in-t-sql
title: Dynamic Order By in T-SQL
wordpress_id: 296
tags:
- sql-server
- t-sql
---

I’ve been looking at some code that looked like this:

 
    
```
CREATE PROCEDURE [dbo]., 
    @DraftStatus int,
    @Surname nvarchar(50),
    @PayrollNo nvarchar(50),
    @StartRow int,
    @PageSize int,
    @OrderBy varchar(50),
    @OrgLevelValueID int = null
AS
BEGIN
    SET NOCOUNT ON;
    DECLARE @sql nvarchar(4000)
    set @sql = 'SELECT * FROM '+
            '(SELECT *, ROW_NUMBER() OVER (ORDER BY ' + @OrderBy + ') AS RowNum '+
            'FROM LMv_EmployeeDrafts '+
            'WHERE DraftStatus = ' + CONVERT(nvarchar(10), @DraftStatus) +
                ' AND Surname LIKE ''%'+@Surname+'%'' '+
                ' AND CONVERT(nvarchar(10), PayrollNo) LIKE ''%'+@PayrollNo+'%'') AS tmp '+
        'WHERE RowNum-1 BETWEEN ' + CONVERT(nvarchar(10), @StartRow)+
            ' AND (' + CONVERT(nvarchar(10), @StartRow) + ' + ' + CONVERT(nvarchar(10), @PageSize) + ') - 1'
        --IF NOT(@OrgLevelValueID IS NULL) SET @sql = @sql + ' AND OrganisationLevelValueID = ' + CONVERT(varchar(max), @OrgLevelValueID)
        --IF NOT (@OrgLevelValueID IS NULL) SET @sql = @sql + ' AND (' + CONVERT(varchar(max), @OrgLevelValueID) + ' IN ' +
        SET @sql = @sql + ' AND (' + CONVERT(varchar(max), @OrgLevelValueID) + ' IN ' +
        '(SELECT OrgLvlID FROM RMR_GetPreOrgLvlValues(OrganisationLevelValueID)))'

    EXEC sp_executesql @sql
    PRINT @sql
END
```





Eeeek… not the most user-friendly (and I’ve certainly written dynamic SQL like this before) but it was written like this to allow for different page ordering. There’s also a bug in this code where the ROW_NUMBER() needs to be conditionally partitioned before being filtered. 





The solution isn’t necessarily the prettiest either. However, it’s now not a sql string being built up so the execution planner can pre-compile it and it is easier to read! The one disadvantage is if a new order by is introduced the SQL needs to be updated – however, I think this is a reasonable exchange:




    
```
SELECT * FROM (
    SELECT *, ROW_NUMBER() OVER (
        PARTITION BY
            CASE WHEN @OrgLevelValueID is not null THEN OrganisationLevelValueID END
        ORDER BY 
            CASE WHEN @OrderBy = 'DateAdded Desc' THEN DateAdded END DESC,
            CASE WHEN @OrderBy = 'Title Desc' THEN Title END DESC,
            CASE WHEN @OrderBy = 'Forename Desc' THEN Forename END DESC,
            CASE WHEN @OrderBy = 'Surname Desc' THEN Surname END DESC,
            CASE WHEN @OrderBy = 'PayrollNo Desc' THEN PayrollNo END DESC,
            CASE WHEN @OrderBy = 'DateAdded' THEN DateAdded END,
            CASE WHEN @OrderBy = 'Title' THEN Title END,
            CASE WHEN @OrderBy = 'Forename' THEN Forename END,
            CASE WHEN @OrderBy = 'Surname' THEN Surname END,
            CASE WHEN @OrderBy = 'PayrollNo' THEN PayrollNo END        
        ) AS RowNum  
    FROM LMv_EmployeeDrafts  
    WHERE 
        DraftStatus = @DraftStatus
        AND Surname LIKE '%' + @Surname + '%'
        AND PayrollNo LIKE '%' + @PayrollNo + '%' 
) AS tmp  
WHERE 
    RowNum - 1 BETWEEN @StartRow AND @StartRow + @PageSize - 1 
    AND (@OrgLevelValueID is null OR @OrgLevelValueID 
        IN (SELECT OrgLvlID FROM RMR_GetPreOrgLvlValues(OrganisationLevelValueID)))
```




A couple of final caveats:






  
  * I’ve not checked the final query in the execution planner to make sure it’s efficient!


  
  * The surname and payroll filtering are being performed using a prefixed ‘%’ which means a table scan – pretty inefficient!



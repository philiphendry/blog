---
author: philiphendry
comments: true
date: 2010-09-02 13:38:34+00:00
layout: post
slug: calling-sql-server-stored-procedure-synonym-fails-with-parameter-error
title: Calling SQL Server Stored Procedure Synonym fails with parameter error
wordpress_id: 321
categories:
- Dev Problem
- SQL Server
- T-SQL
---

I’ve just tried to call a stored procedure in another database via a synonym but received an error ‘Procedure or function ‘LM_Employees_HasNewStoreTempsInStore’ expect parameter [‘@OrganisationLevelValueID’](mailto:‘@OrganisationLevelValueID’), which was not supplied.

 

Here’s how I defined the synonym and called it :

 
```
CREATE SYNONYM [dbo].[LM_Employees_HasNewStoreTempsInStore] 
FOR [PHENDRY].[LabourManager_DEV].[dbo].[LM_Employees_HasNewStoreTempsInStore]
GO

exec [dbo].[LM_Employees_HasNewStoreTempsInStore] 1119
GO
```





As you can see, I’m definitely passing the parameter!!





The problem appears to be how the synonym is defined when the two database exist on the same server. The machine name, here ‘[PHENDRY].’ can be removed completely and everything works fine!

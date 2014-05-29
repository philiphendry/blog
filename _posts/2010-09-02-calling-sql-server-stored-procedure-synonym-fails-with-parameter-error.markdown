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

 
    
    <span style="color:#0000FF;">CREATE</span><span style="color:#000000;"> SYNONYM </span><span style="color:#FF0000;">[</span><span style="color:#FF0000;">dbo</span><span style="color:#FF0000;">]</span><span style="color:#000000;">.</span><span style="color:#FF0000;">[</span><span style="color:#FF0000;">LM_Employees_HasNewStoreTempsInStore</span><span style="color:#FF0000;">]</span><span style="color:#000000;"> 
    </span><span style="color:#0000FF;">FOR</span><span style="color:#000000;"> </span><span style="color:#FF0000;">[</span><span style="color:#FF0000;">PHENDRY</span><span style="color:#FF0000;">]</span><span style="color:#000000;">.</span><span style="color:#FF0000;">[</span><span style="color:#FF0000;">LabourManager_DEV</span><span style="color:#FF0000;">]</span><span style="color:#000000;">.</span><span style="color:#FF0000;">[</span><span style="color:#FF0000;">dbo</span><span style="color:#FF0000;">]</span><span style="color:#000000;">.</span><span style="color:#FF0000;">[</span><span style="color:#FF0000;">LM_Employees_HasNewStoreTempsInStore</span><span style="color:#FF0000;">]</span><span style="color:#000000;">
    </span><span style="color:#0000FF;">GO</span><span style="color:#000000;">
    
    </span><span style="color:#0000FF;">exec</span><span style="color:#000000;"> </span><span style="color:#FF0000;">[</span><span style="color:#FF0000;">dbo</span><span style="color:#FF0000;">]</span><span style="color:#000000;">.</span><span style="color:#FF0000;">[</span><span style="color:#FF0000;">LM_Employees_HasNewStoreTempsInStore</span><span style="color:#FF0000;">]</span><span style="color:#000000;"> </span><span style="color:#800000;font-weight:bold;">1119</span><span style="color:#000000;">
    </span><span style="color:#0000FF;">GO</span>





As you can see, I’m definitely passing the parameter!!





The problem appears to be how the synonym is defined when the two database exist on the same server. The machine name, here ‘[PHENDRY].’ can be removed completely and everything works fine!

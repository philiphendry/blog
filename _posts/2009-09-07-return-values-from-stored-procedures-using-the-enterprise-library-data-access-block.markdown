---
author: philiphendry
comments: true
date: 2009-09-07 15:26:13+00:00
layout: post
slug: return-values-from-stored-procedures-using-the-enterprise-library-data-access-block
title: Return values from stored procedures using the Enterprise Library Data Access
  block
wordpress_id: 284
categories:
- Code
- Enterprise Library
---

I’m not sure why I had so much trouble trying to do this… but because it did I think it’s worth a blog. Basically I was trying to return an int value from a stored procedure but couldn’t quite work out the syntax and was initially distracted by trying to use _AddOutParameter_() . However, the key was to use _AddParameter_() specifying the direction as _ReturnValue_ :

 
```
db.AddParameter(cmd, "@return_value", DbType.Int32, ParameterDirection.ReturnValue, null, DataRowVersion.Default, null);
```
 

Then simply access the named parameter :

 
```
Convert.ToInt32(db.GetParameterValue(cmd, "@return_value"))
```
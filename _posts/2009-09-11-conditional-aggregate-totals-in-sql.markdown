---
author: philiphendry
comments: true
date: 2009-09-11 08:03:44+00:00
layout: post
slug: conditional-aggregate-totals-in-sql
title: Conditional Aggregate Totals in SQL
wordpress_id: 286
categories:
- SQL Server
- T-SQL
- Tip
---

I’ve been working on a SQL Server Reporting Services report which had to display several counts on each row. Each row represented a project and each project could have several tasks that were either ordinary tasks or milestones and could also be escalated. The report need to show something like this :

 <table cellpadding="2" cellspacing="0" border="1" width="402" ><tbody >     <tr >       
<td width="101" rowspan="2" valign="top" >Project Name
</td>        
<td width="118" colspan="5" valign="top" >Milestone Counts
</td>        
<td width="107" colspan="5" valign="top" >Task Count
</td>        
<td width="74" rowspan="2" valign="top" >Escalated Task Count
</td>     </tr>      <tr >       
<td width="123" valign="top" >1st Week
</td>        
<td width="123" valign="top" >2nd Week
</td>        
<td width="123" valign="top" >3rd Week
</td>        
<td width="123" valign="top" >>=4th Week
</td>        
<td width="123" valign="top" >Total
</td>        
<td width="123" valign="top" >1st Week
</td>        
<td width="123" valign="top" >2nd Week
</td>        
<td width="123" valign="top" >3rd Week
</td>        
<td width="123" valign="top" >>=4th Week
</td>        
<td width="123" valign="top" >Total
</td>     </tr>   </tbody></table>  

As you can see a task initially has to break down into two major colours of milestone and ordinary task counts but then is broken down into counts in the 1st, 2nd, 3rd and greater than 4th week totals including the total itself.

 

Initially this seemed tricky when thinking in terms of using _count()_ but there’s an incredibly simple technique that solves this but which isn’t immediately obvious. The _Sum()_ aggregate function can contain an expression and in this example that expression could compare the task date against late week dates and return either a 1 or 0 :

 
    
    <span style="color:#0000FF;">select</span><span style="color:#000000;"> ProjectName,
       </span><span style="color:#FF00FF;">sum</span><span style="color:#000000;">(</span><span style="color:#FF00FF;">case</span><span style="color:#000000;"> </span><span style="color:#0000FF;">when</span><span style="color:#000000;"> IsMilestone </span><span style="color:#808080;">=</span><span style="color:#000000;"> </span><span style="color:#800000;font-weight:bold;">1</span><span style="color:#000000;"> 
          </span><span style="color:#808080;">and</span><span style="color:#000000;"> TaskDate </span><span style="color:#808080;"><</span><span style="color:#000000;"> </span><span style="color:#008000;">@LateDate</span><span style="color:#000000;"> 
          </span><span style="color:#808080;">and</span><span style="color:#000000;"> Taskdate </span><span style="color:#808080;">></span><span style="color:#008000;">@OneWeekLateDate</span><span style="color:#000000;">               
          </span><span style="color:#0000FF;">then</span><span style="color:#000000;"> </span><span style="color:#800000;font-weight:bold;">1</span><span style="color:#000000;"> </span><span style="color:#0000FF;">else</span><span style="color:#000000;"> </span><span style="color:#800000;font-weight:bold;">0</span><span style="color:#000000;"> </span><span style="color:#0000FF;">end</span><span style="color:#000000;">) </span><span style="color:#0000FF;">as</span><span style="color:#000000;"> Week1LateMilestoneCount
    </span><span style="color:#0000FF;">from</span><span style="color:#000000;"> Project
    </span><span style="color:#0000FF;">group</span><span style="color:#000000;"> </span><span style="color:#0000FF;">by</span><span style="color:#000000;"> ProjectName</span>





I’ve only included one example there but I think it’s enough to get the idea.

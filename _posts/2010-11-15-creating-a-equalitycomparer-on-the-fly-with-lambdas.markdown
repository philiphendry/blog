---
author: philiphendry
comments: true
date: 2010-11-15 11:12:55+00:00
layout: post
slug: creating-a-equalitycomparer-on-the-fly-with-lambdas
title: Creating a EqualityComparer on the fly with Lambdas
wordpress_id: 341
categories:
- .Net
- Testing
---

When I’m writing unit tests I need the code to be a succinct as possible and one of the issues I’ve had is comparing objects sets/graphs to confirm expectations. Here’s a couple of the unit tests that test my solution and hopefully demonstrate what I’m after : 

 
    
    <span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">class</span><span style="color:#000000;"> TestObject
    {
       </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">string</span><span style="color:#000000;"> FirstProperty { </span><span style="color:#0000FF;">get</span><span style="color:#000000;">; </span><span style="color:#0000FF;">set</span><span style="color:#000000;">; }
       </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">string</span><span style="color:#000000;"> SecondProperty { </span><span style="color:#0000FF;">get</span><span style="color:#000000;">; </span><span style="color:#0000FF;">set</span><span style="color:#000000;">; }
    }
    
    [TestMethod]
    </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">void</span><span style="color:#000000;"> EqualityCompareFactory_Should_DetectEquality_Given_TwoObjectInstancesWithTheSamePropertyValues()
    {
      var comparer </span><span style="color:#000000;">=</span><span style="color:#000000;"> EqualityComparerFactory</span><span style="color:#000000;"><</span><span style="color:#000000;">TestObject</span><span style="color:#000000;">></span><span style="color:#000000;">.Create((x, y) </span><span style="color:#000000;">=></span><span style="color:#000000;"> x.FirstProperty </span><span style="color:#000000;">==</span><span style="color:#000000;"> y.FirstProperty);
      Assert.IsTrue(comparer.Equals(</span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TestObject { FirstProperty </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">first</span><span style="color:#800000;">"</span><span style="color:#000000;"> }, </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TestObject { FirstProperty </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">first</span><span style="color:#800000;">"</span><span style="color:#000000;">}));
    }
    
    [TestMethod]
    </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">void</span><span style="color:#000000;"> EqualityCompareFactory_Should_DetectInequality_Given_TwoObjectInstancesWithTheDifferentPropertyValues()
    {
      var comparer </span><span style="color:#000000;">=</span><span style="color:#000000;"> EqualityComparerFactory</span><span style="color:#000000;"><</span><span style="color:#000000;">TestObject</span><span style="color:#000000;">></span><span style="color:#000000;">.Create((x, y) </span><span style="color:#000000;">=></span><span style="color:#000000;"> x.FirstProperty </span><span style="color:#000000;">==</span><span style="color:#000000;"> y.FirstProperty);
      Assert.IsFalse(comparer.Equals(</span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TestObject { FirstProperty </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">first</span><span style="color:#800000;">"</span><span style="color:#000000;"> }, </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TestObject { FirstProperty </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">different</span><span style="color:#800000;">"</span><span style="color:#000000;"> }));
    }
    </span>







The key here is that, rather than having to create a derived class of EqualityComparer<> I can simply use a factory and pass it a lambda that defines the comparison operation. In my business rule testing I can now write something like the following assertion :




    
    <span style="color:#000000;">var testResults </span><span style="color:#000000;">=</span><span style="color:#000000;"> TimesheetItemBL.TestReturnSavePaymentFlags.ToList();
    var expected </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> List</span><span style="color:#000000;"><</span><span style="color:#000000;">TimesheetItemPaymentFlags</span><span style="color:#000000;">></span><span style="color:#000000;">{
         </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TimesheetItemPaymentFlags { AppliedRule </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">null</span><span style="color:#000000;">, Date </span><span style="color:#000000;">=</span><span style="color:#000000;"> testWeekStartDate, IsPaid </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">null</span><span style="color:#000000;"> },
         </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TimesheetItemPaymentFlags { AppliedRule </span><span style="color:#000000;">=</span><span style="color:#000000;"> AbsenceRules.WaitingDay, Date </span><span style="color:#000000;">=</span><span style="color:#000000;"> testWeekStartDate.AddDays(</span><span style="color:#800080;">1</span><span style="color:#000000;">), IsPaid </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">false</span><span style="color:#000000;"> },
         </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TimesheetItemPaymentFlags { AppliedRule </span><span style="color:#000000;">=</span><span style="color:#000000;"> AbsenceRules.WaitingDay, Date </span><span style="color:#000000;">=</span><span style="color:#000000;"> testWeekStartDate.AddDays(</span><span style="color:#800080;">2</span><span style="color:#000000;">), IsPaid </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">false</span><span style="color:#000000;"> },
         </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TimesheetItemPaymentFlags { AppliedRule </span><span style="color:#000000;">=</span><span style="color:#000000;"> AbsenceRules.WaitingDay, Date </span><span style="color:#000000;">=</span><span style="color:#000000;"> testWeekStartDate.AddDays(</span><span style="color:#800080;">3</span><span style="color:#000000;">), IsPaid </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">false</span><span style="color:#000000;"> },
         </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TimesheetItemPaymentFlags { AppliedRule </span><span style="color:#000000;">=</span><span style="color:#000000;"> AbsenceRules.WaitingDay, Date </span><span style="color:#000000;">=</span><span style="color:#000000;"> testWeekStartDate.AddDays(</span><span style="color:#800080;">4</span><span style="color:#000000;">), IsPaid </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">false</span><span style="color:#000000;"> },
         </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TimesheetItemPaymentFlags { AppliedRule </span><span style="color:#000000;">=</span><span style="color:#000000;"> AbsenceRules.WaitingDay, Date </span><span style="color:#000000;">=</span><span style="color:#000000;"> testWeekStartDate.AddDays(</span><span style="color:#800080;">5</span><span style="color:#000000;">), IsPaid </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">false</span><span style="color:#000000;"> },
         </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> TimesheetItemPaymentFlags { AppliedRule </span><span style="color:#000000;">=</span><span style="color:#000000;"> AbsenceRules.Paid, Date </span><span style="color:#000000;">=</span><span style="color:#000000;"> testWeekStartDate.AddDays(</span><span style="color:#800080;">6</span><span style="color:#000000;">), IsPaid </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">true</span><span style="color:#000000;"> },
     };
    NUnit.Framework.Assert.That(testResults, </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> CollectionEquivalentConstraint(expected).Using</span><span style="color:#000000;"><</span><span style="color:#000000;">TimesheetItemPaymentFlags</span><span style="color:#000000;">></span><span style="color:#000000;">(
         EqualityComparerFactory</span><span style="color:#000000;"><</span><span style="color:#000000;">TimesheetItemPaymentFlags</span><span style="color:#000000;">></span><span style="color:#000000;">.Create((x, y) </span><span style="color:#000000;">=></span><span style="color:#000000;"> x.AppliedRule </span><span style="color:#000000;">==</span><span style="color:#000000;"> y.AppliedRule </span><span style="color:#000000;">&&</span><span style="color:#000000;"> x.Date.Equals(y.Date) </span><span style="color:#000000;">&&</span><span style="color:#000000;"> x.IsPaid </span><span style="color:#000000;">==</span><span style="color:#000000;"> y.IsPaid, x </span><span style="color:#000000;">=></span><span style="color:#000000;"> x.Date.GetHashCode())
     ));</span>





Admittedly this is still pretty verbose and I might extract some helper functions to reduce the size of this but it does demonstrate the simplicity of the solution which, by the way, looks like this :




    
    <span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">class</span><span style="color:#000000;"> DynamicEqualityComparer</span><span style="color:#000000;"><</span><span style="color:#000000;">T</span><span style="color:#000000;">></span><span style="color:#000000;"> : EqualityComparer</span><span style="color:#000000;"><</span><span style="color:#000000;">T</span><span style="color:#000000;">></span><span style="color:#000000;">
    {
       </span><span style="color:#0000FF;">private</span><span style="color:#000000;"> </span><span style="color:#0000FF;">readonly</span><span style="color:#000000;"> Func</span><span style="color:#000000;"><</span><span style="color:#000000;">T, T, </span><span style="color:#0000FF;">bool</span><span style="color:#000000;">></span><span style="color:#000000;"> _equalsFunction;
       </span><span style="color:#0000FF;">private</span><span style="color:#000000;"> </span><span style="color:#0000FF;">readonly</span><span style="color:#000000;"> Func</span><span style="color:#000000;"><</span><span style="color:#000000;">T, </span><span style="color:#0000FF;">int</span><span style="color:#000000;">></span><span style="color:#000000;"> _hashCodeFunction;
    
       </span><span style="color:#0000FF;">internal</span><span style="color:#000000;"> DynamicEqualityComparer(Func</span><span style="color:#000000;"><</span><span style="color:#000000;">T, T, </span><span style="color:#0000FF;">bool</span><span style="color:#000000;">></span><span style="color:#000000;"> equalsFunction, Func</span><span style="color:#000000;"><</span><span style="color:#000000;">T, </span><span style="color:#0000FF;">int</span><span style="color:#000000;">></span><span style="color:#000000;"> hashCodeFunction)
       {
           _equalsFunction </span><span style="color:#000000;">=</span><span style="color:#000000;"> equalsFunction;
           _hashCodeFunction </span><span style="color:#000000;">=</span><span style="color:#000000;"> hashCodeFunction;
       }
    
       </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">override</span><span style="color:#000000;"> </span><span style="color:#0000FF;">bool</span><span style="color:#000000;"> Equals(T x, T y)
       {
           </span><span style="color:#0000FF;">return</span><span style="color:#000000;"> _equalsFunction(x, y);
       }
    
       </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">override</span><span style="color:#000000;"> </span><span style="color:#0000FF;">int</span><span style="color:#000000;"> GetHashCode(T obj)
       {
           </span><span style="color:#0000FF;">return</span><span style="color:#000000;"> _hashCodeFunction(obj);
       }
    }
    
    </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">class</span><span style="color:#000000;"> EqualityComparerFactory</span><span style="color:#000000;"><</span><span style="color:#000000;">T</span><span style="color:#000000;">></span><span style="color:#000000;"> </span><span style="color:#0000FF;">where</span><span style="color:#000000;"> T : </span><span style="color:#0000FF;">class</span><span style="color:#000000;">
    {
       </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">static</span><span style="color:#000000;"> DynamicEqualityComparer</span><span style="color:#000000;"><</span><span style="color:#000000;">T</span><span style="color:#000000;">></span><span style="color:#000000;"> Create(Func</span><span style="color:#000000;"><</span><span style="color:#000000;">T, T, </span><span style="color:#0000FF;">bool</span><span style="color:#000000;">></span><span style="color:#000000;"> equalsFunction)
       {
           </span><span style="color:#0000FF;">return</span><span style="color:#000000;"> </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> DynamicEqualityComparer</span><span style="color:#000000;"><</span><span style="color:#000000;">T</span><span style="color:#000000;">></span><span style="color:#000000;">(equalsFunction, x </span><span style="color:#000000;">=></span><span style="color:#000000;"> x.GetHashCode());
       }
    
       </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">static</span><span style="color:#000000;"> DynamicEqualityComparer</span><span style="color:#000000;"><</span><span style="color:#000000;">T</span><span style="color:#000000;">></span><span style="color:#000000;"> Create(Func</span><span style="color:#000000;"><</span><span style="color:#000000;">T, T, </span><span style="color:#0000FF;">bool</span><span style="color:#000000;">></span><span style="color:#000000;"> equalsFunction, Func</span><span style="color:#000000;"><</span><span style="color:#000000;">T, </span><span style="color:#0000FF;">int</span><span style="color:#000000;">></span><span style="color:#000000;"> hashCodeFunction)
       {
           </span><span style="color:#0000FF;">return</span><span style="color:#000000;"> </span><span style="color:#0000FF;">new</span><span style="color:#000000;"> DynamicEqualityComparer</span><span style="color:#000000;"><</span><span style="color:#000000;">T</span><span style="color:#000000;">></span><span style="color:#000000;">(equalsFunction, hashCodeFunction);
       }
    }</span>





There’s some complexity involved in providing a hasCode function that is meaningful to the comparison, but otherwise it’s fairly basic.

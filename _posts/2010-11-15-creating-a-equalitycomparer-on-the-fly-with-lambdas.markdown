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

```
public class TestObject 
{ 
public string FirstProperty { get; set; } 
public string SecondProperty { get; set; } 
}  

[TestMethod] 
public void EqualityCompareFactory_Should_DetectEquality_Given_TwoObjectInstancesWithTheSamePropertyValues() 
{ 
	var comparer = EqualityComparerFactory<TestObject>.Create((x, y) => x.FirstProperty == y.FirstProperty); 
	Assert.IsTrue(comparer.Equals(new TestObject { FirstProperty = "first" }, new TestObject { FirstProperty = "first"})); 
}  

[TestMethod] 
public void EqualityCompareFactory_Should_DetectInequality_Given_TwoObjectInstancesWithTheDifferentPropertyValues() 
{ 
	var comparer = EqualityComparerFactory<TestObject>.Create((x, y) => x.FirstProperty == y.FirstProperty); 
	Assert.IsFalse(comparer.Equals(new TestObject { FirstProperty = "first" }, new TestObject { FirstProperty = "different" })); 
}
```

The key here is that, rather than having to create a derived class of EqualityComparer<> I can simply use a factory and pass it a lambda that defines the comparison operation. In my business rule testing I can now write something like the following assertion :

```
var testResults = TimesheetItemBL.TestReturnSavePaymentFlags.ToList(); 
var expected = new List<TimesheetItemPaymentFlags>{ 
	new TimesheetItemPaymentFlags { AppliedRule = null, Date = testWeekStartDate, IsPaid = null }, 
	new TimesheetItemPaymentFlags { AppliedRule = AbsenceRules.WaitingDay, Date = testWeekStartDate.AddDays(1), IsPaid = false }, 
	new TimesheetItemPaymentFlags { AppliedRule = AbsenceRules.WaitingDay, Date = testWeekStartDate.AddDays(2), IsPaid = false }, 
	new TimesheetItemPaymentFlags { AppliedRule = AbsenceRules.WaitingDay, Date = testWeekStartDate.AddDays(3), IsPaid = false }, 
	new TimesheetItemPaymentFlags { AppliedRule = AbsenceRules.WaitingDay, Date = testWeekStartDate.AddDays(4), IsPaid = false }, 
	new TimesheetItemPaymentFlags { AppliedRule = AbsenceRules.WaitingDay, Date = testWeekStartDate.AddDays(5), IsPaid = false }, 
	new TimesheetItemPaymentFlags { AppliedRule = AbsenceRules.Paid, Date = testWeekStartDate.AddDays(6), IsPaid = true }, 
}; 

NUnit.Framework.Assert.That(testResults, 
	new CollectionEquivalentConstraint(expected)
	.Using<TimesheetItemPaymentFlags>( 
		EqualityComparerFactory<TimesheetItemPaymentFlags>
			.Create((x, y) => x.AppliedRule == y.AppliedRule && x.Date.Equals(y.Date) && x.IsPaid == y.IsPaid, x => x.Date.GetHashCode()) 
	)
);
```

Admittedly this is still pretty verbose and I might extract some helper functions to reduce the size of this but it does demonstrate the simplicity of the solution which, by the way, looks like this :

```
public class DynamicEqualityComparer<T> : EqualityComparer<T> 
{ 
	private readonly Func<T, T, bool> _equalsFunction; 
	private readonly Func<T, int> _hashCodeFunction;  

	internal DynamicEqualityComparer(Func<T, T, bool> equalsFunction, Func<T, int> hashCodeFunction) 
	{ 
		_equalsFunction = equalsFunction; 
		_hashCodeFunction = hashCodeFunction; 
	}  

	public override bool Equals(T x, T y) 
	{ 
		return _equalsFunction(x, y); 
	}  

	public override int GetHashCode(T obj) 
	{ 
		return _hashCodeFunction(obj); 
	} 
}  

public class EqualityComparerFactory<T> where T : class 
{ 
	public static DynamicEqualityComparer<T> Create(Func<T, T, bool> equalsFunction) 
	{ 
		return new DynamicEqualityComparer<T>(equalsFunction, x => x.GetHashCode()); 
	}  

	public static DynamicEqualityComparer<T> Create(Func<T, T, bool> equalsFunction, Func<T, int> hashCodeFunction) 
	{ 
		return new DynamicEqualityComparer<T>(equalsFunction, hashCodeFunction); 
	} 
}
```

There’s some complexity involved in providing a hasCode function that is meaningful to the comparison, but otherwise it’s fairly basic.

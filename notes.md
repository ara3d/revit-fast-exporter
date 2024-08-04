# Goal

Export data from Revit as early and as fast as possible, to the point that the user doesn't even notice. 

# Notes on High Performance Exporting

## Performance verus Perception

There are two things to consider when we talk about performance. 

1. How long something takes
2. How long something appears to take.

Sometimes just changing the perception of the length of time it takes to do a task can improve things. 

For example: 
* starting processing earlier
* doing work while the user is idle
* amortizing work cost, but breaking it up into smaller chunks   

In general a piece of software is waiting for a person, more than they are waiting for the computer. 
We can improve perceived performance by doing work in advance, or while the software is idle. 

## Faster versus Less

Generally speaking there are two ways to improve performance:

1. Do the processing more quickly
2. Do less processing (fewer computations or consider less data) 

## Filtering Data

One of the most direct ways to improve performance of an export is to *export less data*. 

We then have two options:

1. Only export data that the user is interested in
2. Only export what has changed.

Number 1 is an easily implemented technique, and will have the same result so we want to focus more on 2.     

## Exporting what Changed 

One challenge with only exporting what has changed, is detect changes quickly.
We may have false positives which we need to filter quickly.

When trying to only export changes, we need a fast method to determine: 

> _has something changed or not?_

A general technique to accelerate this is to quickly determine one or more lists: 

* items that are definitely not changed
* items that are definitely changed

Logicially either of these gives us a smaller of things to consider, 
the list of things that might have changed but we don't know for sure. 

# Revit Specific Challenges 

* Revit is single threaded.
* Well-known Revit export techniques are very slow.
* The traditional Revit export process is blocking.
* Tessellating meshes is slow
* There is a bottleneck of marshaling data from the unmanaged (aka native code) application, and the .NET API, which is a wrapper.  
* The language of Revit users is different than that of professional software development. 
 
# Hypotheses 

* Data extraction from Revit is the actual bottleneck 
* Traditional exporters suffer from numerous programming issues    

# Question 

* How fast can we extract data from the API?  
* Can we do so in the background without affecting the user? 
* How fast can we compute what has changed?
* Where are the bottlenecks in the export process?
* What are the bottlenecks in computing differences?  
* Can we export individual elements and compare changes fast enough that no-one notices?
* How can we quickly determine what hasn't changed or what might have changed?

# Terminology 

- **export** - The word export has some confusion for users versus programmers. 
  In this investigation export means any means to extract data from a Revit model.  

- **geometry hash** - What is it? Is it the "GetHashCode()"?

- **non-blocking** - This is generally synonmous with asynchronous. See [notes-on-async.md].

- **asynchronous** - See [notes-on-async.md].
  


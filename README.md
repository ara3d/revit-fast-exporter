# Revit Fast Exporter 

An experiment to extract data from Revit as fast as possible.

We are exploring

  1. Quickly extracting Revit geometry definitions (without tesselation)
  2. Fast grouping of similar geometry
  3. Fast detection of changed geometry
  4. Performing processing asynchronously via Revit idle events.   

For some high-level thoughts on performance optimization in software see https://github.com/ara3d/revit-fast-exporter/blob/main/notes-on-performance.md.

## C# Scripting in Revit with Bowerbird 

Currently the early coding experiments are being done using the [Bowerbird plug-in](https://github.com/ara3d/bowerbird).
Bowerbird is a tool to allow C# code to be edited and re-loaded dynamically in a running instance of Revit. 
Bowerbird scans a directory for code, and recompiles and reloads it whenever a file is changed, added, or deleted.    

The current code experiments can be found at https://github.com/ara3d/bowerbird/blob/main/Ara3D.Bowerbird.RevitSamples/SampleRevitCommands.cs.

---
# Geometric Change Detection

Detecting changes in geometric data is a hard problem to do rigorously. Hashing techniques reduce a high-dimensional data set to a lower-dimensionality data set,
and invariably different objects can result in the same hash code. When two objects have the same hash-code a detailed comparison has to be done on the geometry. 

We do not recommend computing a mesh, as this is an expensive operation, so we propose using the geometry definitions.  

## Note on GetHashCode()

Despite what it may appear the `GetHashCode` function of various Revit elements (including `GeometryObject`) does not actually create a hash based on the value of the geometry, but rather of 
the address of a native pointer. This means that `GetHashCode` is definitely not going to be consistent between Revit sessions, and maybe not even between the same geometry object. 

---
# Fast Geometry Grouping

## Geometry Abstract Syntax Tree (GAST)

A geometry abstract syntax tree (GAST) is a novel method of succinctly expressing BIM or CAD geometric data that can be used for fast detection of similar geometry, and separation of different geometry.

## Background: AST in Computer Science

An abstract syntax tree (AST) is a data structure used in computer science to represent the structure of a program or code snippet. It is a tree representation of the abstract syntactic structure of text (often source code) written in a formal language. The syntax is "abstract" in the sense that it does not represent every detail appearing in the real syntax, but rather just the structural or content-related details.

## Motivation: Grouping Similar Geometry, and Separating Different Geometry

We wanted an algorithm to efficiently group a set of 3D geometric representations: where items in the same group have a high degree of possibility of being the same, and where distinct groups are known to be different.

The 3D representation of BIM elements stored within the native structures of Revit, or expressed as IFC files, are usually not stored as meshes, instead the usual representation is a tree of geometric objects and/or operations.

Comping a GAST and storing it as a string is a much faster operation than performing tessellation, which is the commonly used method for comparing geometric similarity.

Some early code for generating a GAST can be found at: https://github.com/ara3d/bowerbird/blob/main/Ara3D.Bowerbird.RevitSamples/GeometryAbstractSyntaxTree.cs.

## Open Questions

* How quickly can a GAST be computed even in extremely degenerate cases?
* How reliable is it that GASTs will be different for different objects?
* How similar are distance metrics applied to GASTs are to distance metrics applied to the resulting meshes?   

---
# Aysnchronous Processing in Revit

For some clarification on terminology see: https://github.com/ara3d/revit-fast-exporter/blob/main/notes-on-async.md.

Access to the Revit API can only be done through the main thread.

To enable code to appear non-blocking to a user we can use the `UIApplication.OnIdling` event and a queue to store work in some quickly consumed tasks.
An open question is whether we can perform a substantial enough amount of work in a short enough period of time, to still have a positive user experience 
and to not cause the application to appear to lag or stutter. 

Some code examples are being used to experiment with the OnIdling event: https://github.com/ara3d/bowerbird/blob/main/Ara3D.Bowerbird.RevitSamples/SampleRevitCommands.cs

---
# Revit.Async and the Revit Context 

[Revit.Async](https://github.com/KennanChan/Revit.Async) is a confusingly named tool to simplify the programming pattern of communicating with the Revit API outside of the so-called "Revit Context".
The Revit context is a loosely defined terms, that specifies if Revit is in the correct state to receive API requests. 

For example, if you are on a background thread, or responding to a UI event in a modeless WPF Window you are probably not in a valid Revit context. 
If you are correctly responding to a Revit ExternalEvent or the Revit UIApplication.Idling event then you are in a valid Revit context.   

To determine if you are in a [valid Revit context see this link](https://forums.autodesk.com/t5/revit-api-forum/how-to-know-if-revit-api-is-in-context/td-p/12574320).  

Revit.Async allows the user to `await` on an `async` task which is executed within a valid Revit context. 

The Speckle team uses code derived from the Revit.Async package. 

* See [code here](https://github.com/specklesystems/speckle-sharp/blob/main/ConnectorRevit/RevitSharedResources/Models/APIContext.cs).
* See [discussion of PR here](https://github.com/specklesystems/speckle-sharp/pull/2825)

---
# Filtering of Data before Export 

To improve performance of Revit export, a technique we can apply is to filter data to assure we only include specific kinds of data the user cares about, 
or a filter to exclude specific kinds of data we definitely don't care about.

This can increase the performance of export, as well as the performance of change detection.

No code has been written to do this yet. It is not a hard problem computationally, but it requires significant consideration to be given 
to the design of the workflow and UI. 

---
# Experiment: Non-Blocking Revit Background Exporter

The goal was to perform a non-trivial task on a set of objects during `Idling` events. In this experiment I extract geometry information (but without tessellating meshes)
from every element in the scene. The total running time of the algorithm 2.8 seconds but is amortized over a total of 40 seconds. We demonstrate that we can still navigate,
select, and even add or remove objects in the Revit scene.     

Find code at: https://github.com/ara3d/bowerbird/blob/main/Ara3D.Bowerbird.RevitSamples/SampleRevitCommands.cs

https://github.com/user-attachments/assets/02dc86df-7393-4152-b43d-0d3b9961cd20

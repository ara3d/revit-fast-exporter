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

---
# Aysnchronous Processing in Revit

For some clarification on terminology see: https://github.com/ara3d/revit-fast-exporter/blob/main/notes-on-async.md.

Access to the Revit API can only be done through the main thread.

To enable code to appear non-blocking to a user we can use the `UIApplication.OnIdling` event and a queue to store work in some quickly consumed tasks.
An open question is whether we can perform a substantial enough amount of work in a short enough period of time, to still have a positive user experience 
and to not cause the application to appear to lag or stutter. 

---
# Filtering of Data before Export 

To improve performance of Revit export, a technique we can apply is to filter data to assure we only include specific kinds of data the user cares about, 
or a filter to exclude specific kinds of data we definitely don't care about.

This can increase the performance of export, as well as the performance of change detection.


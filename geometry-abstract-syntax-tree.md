# Geometry Abstract Syntax Tree (GAST)

A geometry abstract syntax tree (GAST) is a novel method of succinctly expressing BIM or CAD geometric data that can be used 
for fast detection of similar geometry, and separation of different geometry.  

# Usage of AST in Computer Science 

> An abstract syntax tree (AST) is a data structure used in computer science to represent the structure of a program or code snippet.
> It is a tree representation of the abstract syntactic structure of text (often source code) written in a formal language.
> The syntax is "abstract" in the sense that it does not represent every detail appearing in the real syntax, but rather just the structural or content-related details.

# Motivation: Grouping Similar Geometry, and Separating Different Geometry

We wanted an algorithm to efficiently group a set of 3D geometric representations: where items in the same group have a high degree of possibility of being the same, 
and where distinct groups are known to be different.   

The 3D representation of BIM elements stored within the native structures of Revit, or expressed as IFC files, are usually not stored as meshes, 
instead the usual representation is a tree of geometric objects and/or operations. 

Comping a GAST and storing it as a string is a much faster operation than performing tessellation, which is the commonly used method for comparing geometric similarity. 


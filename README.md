# Revit Fast Exporter 

An experiment to extract data from Revit as fast as possible.

We are exploring

  1. Quickly extracting Revit geometry definitions (without tesselation)
  2. Fast grouping of similar geometry
  3. Fast detection of changed geometry
  4. Performing processing asynchronously via Revit idle events.   

## Current Code 

For example there are some sample Revit commands at https://github.com/ara3d/bowerbird/blob/main/Ara3D.Bowerbird.RevitSamples/SampleRevitCommands.cs.

## C# Scripting in Revit with Bowerbird 

Currently the early coding experiments are being done using the [Bowerbird plug-in](https://github.com/ara3d/bowerbird).
Bowerbird is a tool to allow C# code to be edited and re-loaded dynamically in a running instance of Revit. 
Bowerbird scans a directory for code, and recompiles and reloads it whenever a file is changed, added, or deleted.    


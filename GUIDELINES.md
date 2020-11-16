# CONTRIBUTION GUIDELINES

This document summarizes the general guidelines agreed by the team to the development of this project. Please read carefully before contributing.

## The Project

### Goals

Main goals of this project:

- Illustrate the **relationship** between common **Grasshopper** functionality and its implementation via **RhinoCommon**.

- Help as an **educational resource** for students transitioning from _vanilla Grasshopper_ to lower-level _C#-RhinoCommon_ implementations.

We will achieve this goal by reverse engineering (most) **common GH components**, and creating a dictionary of **open-source C# implementations** that mirror their functionality. 

### General Principles

In general, while contributing to this project, please keep the following in mind:

- This is an **educational project**, so we want to maximize its potential as a learning tool. We are not trying to extend functionality or maximize performance. 

- We will focus on **legibility**: thoroughly commented code. 

- We will focus on **simplicity**: avoid shortcuts, be very verbose about what is happening in the component at all times.

- We will focus on **symmetry**: we will try to mimic the original functionality as close as possible, including all side outputs of components. 

- We will focus on **geometry**: we will probably not work with components used as inputs, have very specific UIs or don't fall under common GH component patterns. 

- Optimization or performance is not of interest: whenever possible, **use direct RhinoCommon implementations or simple algorithms**, even if they may not be the fastest alternatives.

- This project is our **interpretation** of how C# components may work: we do not intend to claim this is the actual code running behind them, but we will do as much as possible to mimic their functionality however we see fit. 

## Development Guidelines

### Project Structure

- The project will be structured in folders in numerical order with tabs names, e.g. `01_Params`, `02_Maths`, `03_Sets` and so on. 

- Each folder will contain a file for each category in that tab, named `TabName.CategoryName.ghx`, e.g. `Vector.Field.ghx`, `Vector.Grid.ghx`, `Vector.Plane.ghx`, etc. 

- Each one of those files will contain a GH definition with all our implementations for the components included in such category. 

- A template for this GH definition can be found in this repo under `./templates`.

- Each file will be saved in the legacy `ghx` format that described the plain-text XML form of the definition. This will optimize the versioning of this project.

### The GH Files

- Each file contains two columns with multiple rows of groups of components, laid out in the same way as they are in the GH category UI (see [the template](templates/README.md)). Horizontal lines mimic the separators in the UI. This should make it easy for the reader to find component groups in the definition based on their location on the ribbon UI.  

- Each group in the definition is a side-by-side comparison of a native GH component and our open-source C# interpretation; this helps legibility and simmetry.

- The group contains all the necessary inputs to make the component/s work (sliders, geometry, etc), the same inputs fed to both components, and output panels showing how the outputs are the same; everything should work out of the box with zero/minimal user input.

- Where parameter boxes with geometry are used as inputs, the data must be _internalised_. This will allow the components to work right away without needing to maintain a parallel rhino file. 

- Groups will be labeled with _group tags_, not with _scribbles_.

- Please adhere to color/style conventions for groups, panels, typefaces, etc. (see [the template](templates/README.md)).

- Whenever a component is _kinda of working but needs more development_ for some reason (not matching the original component exactly, not accounting for multiple input types, not displaying all necessary error messages... ), do as much as you can, but flag the component as _WIP_: add a Warning message to the component indicating why it's not complete, and group it with a different color (see [the template](templates/README.md)).

- Wherever a component has _not been developed yet_ (difficult, not working, not clear, no time...), leave the empty vanilla component on the file, and group it with a different color (see [the template](templates/README.md)). This should allow potential developers to identify where help is needed. 

### The Components

- The core of our work is to develop components that mimic the functionality of a native GH component as close as possible. 

- C# components should be renamed to match the shorthand version of the original component name, with the `GH#` prefix. For example, for "Interpolate" it would be `GH# IntCrv`, for "Divide Surface" it would be `GH# SDivide`, etc. 

- Make sure to use the pain bucket to force the name to show up on the component, regardless of Show Icons state.

- Components should have the same inputs and outputs as the original; naming convention will be to use the short naming version for I/Os. Some original components have identical names for inputs and outputs (`P`, `t`), which we cannot mimic with C# components. In this case, use your best judgement to rename the I/O, and choose to rename the output when possible. 

- We will leave the `out` output of the C# component for easier troubleshooting or future debugging.

- If a component has some kind of special UI (e.g. "Right Trigonometry", "Point On Curve", "Transform Matrix"...), feel free to design a non-UI C# version that mimics the original spirit with regular I/Os.
 
### The Code

When developing the code of your C# component, please remember:

- The purpose of this project is educational, so try to write simple and clear code, with little/no shortcuts, and maximize legibility. Remember that your reader will likely be a beginner programmer, not used to complicated structures, code idioms or esoteric implementations. 

- Be extremely generous with comments, grouping lines of code in step-wise sections and describing what is happening in each one. 

Style guidelines for writing the code:

- Each component should start with a comment referencing the author/s' github username separated by commas. It should be followed by a small description of what the component does. Example:

``` csharp
// Written by @krihterlunn, @johndoe & @garciadelcastillo.

// This component creates a curve that is interpolated through
// a list of points. 
```

- Where possible, direct RhinoCommon implementations will be preferred. The main goal of this project is to facilitate the GH to C# transition. We will decide as a team if we want to implement even lower-level examples for simpler operaations, such as the dot product, cross product, etc. 

- Be verbose: avoid shortcuts or complicated one-liners, declare interim variables, avoid using `var`... remember it's an educational project for beginners :) For example:
``` csharp
// WE WANT TO FAVOR THIS STYLE OF CODE:
// Iterate over all points to compute the distance to
for (int i = 0; i < points.Count; i++) 
{
    // Compute distances to origin on each axis
    Point3d p = points[i];
    double dx = origin.X - p.X;
    double dy = origin.Y - p.Y;
    double dz = origin.Z - p.Z;

    // Use Pythagorean theorem to compute Euclidean distance
    double d = Math.Sqrt(dx * dx + dy * dy + dz * dz);

    // Add distance value to list
    distances.Add(d)
}

// AS OPPOSED TO:
// Compute distances
for (var i = 0; i < points.Count; i++) 
{
    distances.Add(Math.Sqrt(origin.X - points[i].X, origin.Y - points[i].Y, origin.Y - points[i].Y));
}
```

- Stick to [C# naming conventions](https://github.com/ktaranov/naming-convention/blob/master/C%23%20Coding%20Standards%20and%20Naming%20Conventions.md). Use simple, yet clear names for your variables. 

- No endless lines of code, please stick to around 80 characters per line. Makes it easier to read independent of IDE or screen resolution.
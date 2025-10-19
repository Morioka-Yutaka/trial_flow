# trial_flow
A SAS macro package for creating flow diagrams, including CONSORT diagrams, with boxes, lines, and plots for clinical trial workflows.

<img width="360" height="360" alt="Image" src="https://github.com/user-attachments/assets/4d789264-366b-4ced-b825-6d0e52d99073" />

## Usage Example
<img width="489" height="368" alt="Image" src="https://github.com/user-attachments/assets/327e626a-8d09-4c05-8124-3dd6c26be119" />  

~~~sas
%diagram_box(boxid=1,
                    left_x=30,top_y=100
                    ,right_x=70,bottom_y=90,
                    text=Assessed for eligibility (n=100))
%diagram_line(lineid=1,
                      start_x=50,
                      start_y=90,
                      end_x=50,
                      end_y=55)
%diagram_line(lineid=2,
                      start_x=50,
                      start_y=73,
                      end_x=65,
                      end_y=73)
%diagram_box(boxid=2,
                    left_x=65,top_y=87
                    ,right_x=95,bottom_y=58
                    ,text_just=left
                    ,text=Excluded (n=10)#- Not meeting inclusion#  criteria (n=4)
                     #- Declined to Participate#  (n=4)
                     #- Other reasons#  (n=2))
%diagram_box(boxid=3,
                    left_x=37,top_y=55
                    ,right_x=63,bottom_y=45,
                    text=Randomized (n=90))
%diagram_line(lineid=3,
                      start_x=37,
                      start_y=50,
                      midpoint_x=32,
                      midpoint_y=50,
                      end_x=32,
                      end_y=30)
%diagram_line(lineid=4,
                      start_x=63,
                      start_y=50,
                      midpoint_x=68,
                      midpoint_y=50,
                      end_x=68,
                      end_y=30)
%diagram_box(boxid=5,
                    left_x=40,top_y=37
                    ,right_x=60,bottom_y=32
                    ,text=Allocation)
%diagram_box(boxid=6,
                    left_x=20,top_y=30
                    ,right_x=49,bottom_y=10
                    ,text_just=left
                    ,text=Allocated to intervention #A (n=45)
                     #- xxxxxxxxxx (n=xx)
                     #- xxxxxxxxxx (n=xx))
%diagram_box(boxid=7,
                    left_x=54,top_y=30
                    ,right_x=83,bottom_y=10
                    ,text_just=left
                    ,text=Allocated to intervention #B (n=45)
                     #- xxxxxxxxxx (n=xx)
                     #- xxxxxxxxxx (n=xx))
/*plot*/
%diagram_plot();
~~~

~~~sas

~~~


## `%diagram_box()` macro <a name="diagrambox-macro-1"></a> ######
   Purpose:  
     Creates a rectangular box (polygon) and corresponding text annotation dataset for use in flow diagrams.  
     The macro generates two datasets:  
       - diagram_box_<boxid>:  Coordinates of the box  
       - diagram_box_text_<boxid>: Position and content of text  
   
   Parameters:  
   ~~~text
     boxid      = Unique identifier for the box (default=1)
     left_x     = X coordinate of the left edge   (default=10)
     top_y      = Y coordinate of the top edge    (default=100)
     right_x    = X coordinate of the right edge  (default=30)
     bottom_y   = Y coordinate of the bottom edge (default=90)
     text       = Label text to be displayed inside the box
     text_just  = Text justification (CENTER [default] or LEFT)
 ~~~

   Notes:  
     - Coordinates define an axis-aligned rectangle.  
     - Text position is calculated based on justification:  
         CENTER → centered at the box  
          LEFT   → aligned to the left edge, vertically centered  
     - Text can include line breaks using the SPLITCHAR in SGPLOT.  
     - The macro is typically used in combination with diagram_line  
       and diagram_plot to build flow diagrams (e.g., CONSORT).  

  Usage Example:
  ~~~sas
    %diagram_box(
        boxid=1,
        left_x=30,
        top_y=100,
        right_x=70,
        bottom_y=90,
        text=Assessed for eligibility (n=100)
    );
 
    %diagram_box(
        boxid=2,
        left_x=65,
        top_y=86,
        right_x=95,
        bottom_y=60,
        text_just=left,
        text=Excluded (n=10)#- Not meeting inclusion criteria (n=4)
             #- Declined to participate (n=4)
             #- Other reasons (n=2)
    );
~~~
  
---

## `%diagram_line()` macro <a name="diagramline-macro-2"></a> ######
 Purpose    : Create a dataset containing line coordinates (start → [mid] → end).  
              Useful for plotting diagrams where lines are defined by 2 or 3 points.  
  
 Parameters :  
 ~~~text
   lineid      = (numeric, default=1)
                 Identifier for the line. Used as a variable in the output dataset name.

   start_x     = (numeric, default=30)
   start_y     = (numeric, default=90)
                 Coordinates of the starting point of the line.

   midpoint_x  = (numeric, optional)
   midpoint_y  = (numeric, optional)
                 Coordinates of an optional midpoint. If both are provided,
                 the line will include this point.

   end_x       = (numeric, default=30)
   end_y       = (numeric, default=60)
                 Coordinates of the end point of the line.
~~~
 Output     :
   A dataset named diagram_line_<lineid> with variables:  
     - lineid : the line identifier  
     - line_x : x coordinate of points  
     - line_y : y coordinate of points  
  
 Example    :  
~~~sas
   %diagram_line(lineid=2, start_x=10, start_y=20, midpoint_x=15, midpoint_y=25, end_x=30, end_y=40);
   → Produces dataset diagram_line_2 with 3 points: (10,20) → (15,25) → (30,40)
~~~
  
---

## `%diagram_plot()` macro <a name="diagramplot-macro-3"></a> ######
  Purpose:  
    Combines all generated diagram components (boxes, text, and lines) and plots them as a flow diagram using PROC SGPLOT.  
    The macro collects all datasets prefixed with "diagram_" and produces a complete diagram without axes or borders.  
  
  Parameters:  
  ~~~text
    minx  = Minimum value of the X-axis (default=0)
    maxx  = Maximum value of the X-axis (default=100)
    miny  = Minimum value of the Y-axis (default=0)
    maxy  = Maximum value of the Y-axis (default=100)
~~~
  Notes:  
    - This macro expects that %diagram_box and %diagram_line have already been called to create the component datasets.  
    - Text is automatically split by the SPLITCHAR (#) option.  
    - Arrows are drawn for lines with arrowhead at the end.  
  
  Usage Example:  
~~~sas
    %diagram_box(
        boxid=1,
        left_x=30, top_y=100,
        right_x=70, bottom_y=90,
        text=Assessed for eligibility (n=100)
    );

    %diagram_line(
        lineid=1,
        start_x=50, start_y=90,
        end_x=50,   end_y=55
    );

    %diagram_box(
        boxid=2,
        left_x=37, top_y=55,
        right_x=63, bottom_y=45,
        text=Randomized (n=90)
    );

    %diagram_plot(minx=0, maxx=100, miny=0, maxy=100);
~~~
  
---


## Notes on versions history

- 0.1.0(05October2025): Initial version.

---

## What is SAS Packages?

The package is built on top of **SAS Packages Framework(SPF)** developed by Bartosz Jablonski.

For more information about the framework, see [SAS Packages Framework](https://github.com/yabwon/SAS_PACKAGES).

You can also find more SAS Packages (SASPacs) in the [SAS Packages Archive(SASPAC)](https://github.com/SASPAC).

## How to use SAS Packages? (quick start)

### 1. Set-up SAS Packages Framework

First, create a directory for your packages and assign a `packages` fileref to it.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~sas
filename packages "\path\to\your\packages";
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Secondly, enable the SAS Packages Framework.
(If you don't have SAS Packages Framework installed, follow the instruction in 
[SPF documentation](https://github.com/yabwon/SAS_PACKAGES/tree/main/SPF/Documentation) 
to install SAS Packages Framework.)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~sas
%include packages(SPFinit.sas)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


### 2. Install SAS package

Install SAS package you want to use with the SPF's `%installPackage()` macro.

- For packages located in **SAS Packages Archive(SASPAC)** run:
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~sas
  %installPackage(packageName)
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- For packages located in **PharmaForest** run:
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~sas
  %installPackage(packageName, mirror=PharmaForest)
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- For packages located at some network location run:
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~sas
  %installPackage(packageName, sourcePath=https://some/internet/location/for/packages)
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  (e.g. `%installPackage(ABC, sourcePath=https://github.com/SomeRepo/ABC/raw/main/)`)


### 3. Load SAS package

Load SAS package you want to use with the SPF's `%loadPackage()` macro.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~sas
%loadPackage(packageName)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


### Enjoy!



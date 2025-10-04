# trial_flow
A SAS macro package for creating flow diagrams, including CONSORT diagrams, with boxes, lines, and plots for clinical trial workflows.

<img width="360" height="360" alt="Image" src="https://github.com/user-attachments/assets/33eaee00-7741-42aa-9d0c-640f38cd675f" />

## Usage Example
<img width="490" height="374" alt="Image" src="https://github.com/user-attachments/assets/cd40286a-8672-4e23-b28e-60be2c84a4c3" />  

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

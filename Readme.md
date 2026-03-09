Create a simple shape editor using SVG.

https://plattsburgh.video.yuja.com/V/Video?v=14106852&node=61514562&a=128187271

Using shapes (not buttons), implement Create, Move, and Delete; and two shape types: Circles and Rectangles.

Create shapes in the SVG for each command (Create, Move, Delete), and shapes for the shape types that are only visible when required. These are static shapes, created in your HTML. The commands will create, move, and delete dynamic shapes that you create through menu selections.

A few hints:
* Think of this as a state machine.
* Note that the message at the top changes to indicate what's happening.
* You will need a data structure that contains vital information about the shapes. It's helpful to put the element (i.e. what's returned from getElementById) in the structure along with the ID and the shape's location.
* You will also need a data structure that holds the shapes for the commands, and their outlines (look at getBBox()).
* In the SVG, I created the shapes for the commands, then put their data into the command data structure.
* The shapes are groups that contain a rectangle and text. That allows the text to be drawn over the rectangle.

Create a data structure that holds:
* Shape type (i.e. name, like "circle")
* Shape origin (x and y)
* Other required attribute (like "r" for circle radius, "height" and "width" for rectangles)
* Shape's fill color
* Shape's ID (recommend using "id"+n, where n is the nth shape created)
* Element in the DOM (you should create the shapes using createElementNS() and assign the ID you generate. That allows you to link the shapes in the DOM with the entries in this data structure.

A similar data structure for the commands.

My implementation has functions that do the following:
* A master loop (dispatchEvent) that called from the mousedown event listener. It gets the x/y location of the mouse click
* A function to change the message at the top of the screen
* A function that determines if an x/y intersects with a command rectangle and returns the command
* A function that determines if an x/y intersects with a shape, and returns the index in the shapes array.
* A function that determines the distance from x/y to the center of a circle (for rectangles you check if x/y is inside the shape, for circles you need to check the radius)
* A function to blink a shape by alternating the fill color between "transparent" and the shape's fill.
* A shape to expose or hide the shape types.
* A function to move a shape to a new x/y
* A function to delete a shape (from the DOM and the shapes array)
* Functions to create rectangles and circles by creating a new element, setting the attributes, appending to the SVG, and appending to the shapes array.

The only function that exceeds about 10-15 lines is the dispatcher.

Think about the state machine:
* Create => Select Shape Type => Find Location and add shape
  * Something that fooled Gemini (below) is that the create command just makes the shape types visible. You can probably create a rect or circle without clicking on create, it's a bug.
* Delete => Select Shape => delete
* Move => Select Shape => Find location and move

Gemini extracted this state transition table from my dispatcher:

These events trigger transitions between states.

* From Idle:
  * Event: User clicks the 'create' command.  (Gemini missed this one)
    * Action: Expose the shape types
    * Transition To: Idle.
  * Event: User clicks the 'rectangle' command.
    * Transition To: Creating Rectangle.
  * Event: User clicks the 'circle' command.
    * Transition To: Creating Circle.
  * Event: User clicks the 'move' command.
    * Transition To: Select to Move.
  * Event: User clicks the 'delete' command.
    * Transition To: Select to Delete.

* From Creating Rectangle:
  * Event: User clicks on the canvas.
    * Action: A rectangle is created at the click location.
    * Transition To: Idle.

* From Creating Circle:
  * Event: User clicks on the canvas.
    * Action: A circle is created at the click location.
    * Transition To: Idle.

* From Select to Move:
  * Event: User clicks on a shape.
    * Action: The selected shape is marked for moving (e.g., it blinks).
    * Transition To: Placing Shape.

* From Select to Delete:
  * Event: User clicks on a shape.
    * Action: The selected shape is deleted.
    * Transition To: Idle.

* From Placing Shape:
  * Event: User clicks on the canvas.
    * Action: The previously selected shape is moved to the new location.
    * Transition To: Idle.


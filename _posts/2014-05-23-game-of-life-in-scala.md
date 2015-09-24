---
layout: post
---
![](/images/GameOfLifeApp-300x277.png)

Find the sources on GitHub: <a href="https://github.com/danwatford/gameoflife-scala.git">https://github.com/danwatford/gameoflife-scala.git</a>

Game of Life in Scala is a simple GUI program making use of the scala.swing package. Scala.swing provides wrappers around the Java Swing component removing a lot of the boiler plate associated with common tasks.

This program's UI consists of a single button and OnOffGrid, a panel based class which draws 'cells' according to whether they are On (Alive) or Off (Dead). OnOffGrid.scala is listed below.
<script src="https://gist.github.com/danwatford/db1564be85c0f570d5e5.js"></script>
In its constructor, OnOffGrid sets its preferred size, declares an interest in mouse clicks, then defines a handler for MouseClicked events.

Mouse clicks are transformed into CellClicked events and published to any components that are listening to the OnOffGrid. This facility allows the listener to alter any model that might be driving the OnOffGrid.

When the OnOffGrid is painted, the paintComponent method determines the On/Off state of each cell by calling the function assigned to onOff. The On/Off state of each cell is recalculated each time painting is required which may not be the most efficient approach. An alternative may be to have the OnOffGrid cache the state of each cell and only query the onOff function when the client invalidates the cache.

The main frame of the program is set up in:
<script src="https://gist.github.com/danwatford/6c5581301d3b18e28db6.js"></script>
The GofLUI object extends SimpleSwingApplication which provides the main method and sets up a Frame according to the <em>top</em> method. Top creates a MainFrame setting a title and assigning the contents into a BorderLayout. Interest is registered in clicks on the Button and clicks on the OnOffGrid's Cells. Both of these events drive changes to the model and trigger the OnOffGrid to be redrawn.
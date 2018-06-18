## Adding Circle And Line Transitions 

#### Starter Code

The starter code for this section has been provided so please fork the following codepen: 

[D3 - Scatterplot - Circle\Line Transitions - Starter](https://codepen.io/jkeohan/pen/NyZvNL)

#### Intro

It is my opinion, that transitions are a key factor in capturing the viewer and directing their attention. 

#### Getting Started

Let's take a look at the solution and use it to help define what we think needs to be done. 

- mouseover/mouseout events
- circle stroke and/or radius increase/decrease
- lines are drawn and then removed


#### Adding Mouseover/Mouseout Events

Events occur all the time as a user interacts with a web site, application, or data visualization.  D3 can leverage any DOM event type supported by your browser.  All that's required is attaching an event listener to the selection and providing instructions for what to do once that event is triggered.  To do that, we use the **.on()** method and assign it to the entering circles. 

```
circlesEnter
 .on("mouseover", mouseOver)
 .on("mouseout", mouseOut);
```

In the above example we've also assigned corresponding functions to the events, so we need to create them as well.  

```
function mouseOver(d) {}

function mouseOout(d) {}
```

Although the actual callback function within the event listener isn't passed any arguments directly, it is indeed being passed the following:

- d: the element itself 
- this: the current context
- event: the event object

Let's console them all and see what they return. 

```
function mouseOver(d) {
	console.log(d);
	// => {2002: "49.5", 2012: "46.9", Location: "Norway", Region: "Scandanavia"}
	
	console.log(this)
	// => <circle cx=​"186.33809523809526" cy=​"113.28" r=​"5" stroke-width=​"1" stroke=​"rgba(230,230,230, .8)​" title=​"Norway" style=​"fill:​ rgb(140, 86, 75)​;​">​</circle>​
	
	console.log(event)
   // => 	MouseEvent {isTrusted: true, screenX: 967, screenY: 345, clientX: 374, clientY: 156, …}
}
```

#### Circle Transitions

Since **this** represents the element that triggered the event, we can use **d3.select(this)** to grab it and transition its properties.  The properties we're looking to alter are:

- stroke-width 
- stroke
- radius (r)



```
function mouseOver(d) {
	d3.select(this)    
	  .attr("stroke-width", 20)
	  .attr("stroke", "rgba(230,230,230, .8)")
	  .attr("r", 15);
}
```

Although the above code makes the changes, it does so abruptly which isn't visually appealing.  In order to transition those properties we will use **.transition()** to transition those property values and **.duration()** for the amount of time it should take for that transition to complete. 

```
function mouseOver(d) {
	d3.select(this)    
	 .transition().duration(1000)
	  .attr("stroke-width", 20)
	  .attr("stroke", "rgba(230,230,230, .8)")
	  .attr("r", 15);
}
```

The **mouseOut** function merely reverses the previous transition. 

```
function mouseOut() {
	d3.select(this)
	 .transition().duration(1000)
	  .attr("stroke-width", 1)
	  .attr("stroke", "rgba(230,230,230, .8)")
	  .attr("r", 5);
}
```

#### Line Transitions

Since we want the lines transitions to coincide with the circles, we will add two new functions to the end of the **mouseOver** function, that will create and transition the corresponding lines.  

```
function mouseOver(d) {
 // previous code

  createLineY(d);
  createLineX(d); 
} 
```

There are at least two distinct ways to create lines which are:

- append a line and assign it distinct x1,x2,y1,y2 values
- append a path and use the d3.line() generator to create a path

In our example, I've opted to go with the first option which means we need to append a new line to the gMain element and bind the existing data so that we can access the values 2002 & 2012. 

```
function createLineY(d) {
 const lineY = gMain.append("line")
 	.datum(d).attr("class", "lineY");
}
```

Drawing the line requires that we first provide values for x1,x2,y1,y2. 

```
function createLineY(d) {
 const lineY = gMain.append("line")
 	.datum(d).attr("class", "lineY");
 
 lineY
  .attr("x1", xScale(+lineY.datum()["2002"]))
  .attr("y1", yScale(+lineY.datum()["2012"]))
  .attr("x2", xScale(-5))
  .attr("y2", yScale(+lineY.datum()["2012"]));
}
```

You may have noticed that the x2 value is -5 which is directly related to how we set up the y scale:  **yScale.domain([-5, maxY + 10])**.  The -5 was meant to create distance between the x/y axis lines since some of the data points were .5.  

If we examined the elements in developer tools then it would be clear that the line is there, however, it's not visible.  By default, lines aren't assinged a stroke color so let's add a few properties to create the line in our design:

- stroke: color for the line
- stroke-width: thickness of the line
- stroke-dasharray: line should include dashes

```
function createLineY(d) {
 const lineY = gMain.append("line").datum(d).attr("class", "lines");
 
 lineY
  .attr("x1", xScale(+lineY.datum()["2002"]))
  .attr("y1", yScale(+lineY.datum()["2012"]))
  .attr("x2", xScale(-5))
  .attr("y2", yScale(+lineY.datum()["2012"]))
  .attr("stroke", d => colorScale(d['Region']))
  .attr("stroke-width", 2)
  .attr("stroke-dasharray", "10,3")
}
```
We're still tasked with transitioning those lines which requires a small refactor to the code.  Since it's the x2,y2 values that will transition, we're going to set them initially to their x1,y1 equivalents. Then we will use **.transition().duration()** to make the transition to their new positions. 


```
function createLineY(d) {
 const lineY = gMain.append("line").datum(d).attr("class", "lines");
 
 lineY
  .attr("x1", xScale(+lineY.datum()["2002"] - 2 ))
  .attr("y1", yScale(+lineY.datum()["2012"]))
  .attr("x2", xScale(+lineY.datum()["2002"]))
  .attr("y2", yScale(+lineY.datum()["2012"]))
  .attr("stroke", d => colorScale(d['Region']))
  .attr("stroke-width", 2)
  .attr("stroke-dasharray", "10,3")
  .attr("opacity", 0)
  .attr("class", "lines")
  .transition()
  .duration(750)
  .delay(250)
  .attr("opacity", 0.4)  
  .attr("x2", xScale(-5))
  .attr("y2", yScale(+lineY.datum()["2012"]));
}
```

A few things to note about the above is that the x1 value is set to subtract 2 from its initial starting position.  One issue that I came across during the initial design, was that the **mouseover** and **mouseout** events might cycle without the user moving their mouse. I attributed this to the line being drawn at the exact coords where the mouse was positioned.  So the solution I came up with was to have the line start at the end of the circle.  I also added a starting opacity as one could see the initial start of the line before it started its transition. 

As you can imagine, a similar code is needed to draw and transition the x line. 

```
  function createLineX(d) {
    var lineX = gMain.append("line").datum(d).attr("class", "lines")
    lineX
      .attr("x1", xScale(+lineX.datum()["2002"]))
      .attr("y1", yScale(+lineX.datum()["2012"] - 2 ))
      .attr("x2", xScale(+lineX.datum()["2002"]))
      .attr("y2", yScale(+lineX.datum()["2012"]))
      .attr("stroke", d => colorScale(d['Region']))
      .attr("stroke-width", 2)
      .style("stroke-dasharray", "10,3")
      .style("opacity", 0)
      .attr("class", "lines")
      .transition()
      .duration(750)
      .delay(250)
      .style("opacity", 0.4)
      .attr("x2", xScale(+lineX.datum()["2002"]))
      .attr("y2", yScale(-5));
  }
```

The only thing left now is to transition the removal of those lines on the **mouseout** event. This is done by first selecting all the lines and transitioning the opacity first to 0 and then using **.remove()** to remove them completely. 

```
  function mouseOut() {
    // previous code

    d3
      .selectAll(".lines")
      .transition()
      .duration(1000)
      .style("opacity", 0)
      .remove();
  }
```

#### Solution Code

Here is the full solution code for the project thus far:

[D3 - Scatterplot - Circle\Line Transitions - Solution ](https://codepen.io/jkeohan/pen/EQBvNV?editors=0010)

#### References

[Handling Events](https://github.com/d3/d3-selection/blob/master/README.md#handling-events)
[Drawing A Straight Line](https://www.dashingd3js.com/svg-basic-shapes-and-d3js)
[D3 Lines](https://github.com/d3/d3-shape#lines)

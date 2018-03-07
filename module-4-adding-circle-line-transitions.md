## Adding Circle And Line Transitions 

#### Starter Code

The starter code for this section has been provided so please fork the following codepen: 

[D3 - Scatterplot - Circle\Line Transitions - Starter](https://codepen.io/jkeohan/pen/NyZvNL)

#### Intro

It is my opinion that transitions are a key factor in capturing the viewer and directing their attention. 

#### Getting Started

Let's take a look at the solution and use it to help define what we think needs to be done. 

- mouseover\mouseout events
- circle stroke and\or radius increase\decrease
- lines are drawn and then removed


#### Adding Mouseover\Mouseout Events

Events occur all the time as a user interacts with a web site, application or data visualization.   D3 can leverage any DOM event type supported by your browser.  All that required is attaching an event listener to the selection and providing instructions for what to do once that event is triggered.  To do that we use the **.on()** method and assign it to the entering circles. 

```
circlesEnter
 .on("mouseover", mouseOver)
 .on("mouseout", mouseOut);
```

In the above example we've also assigned corresponding functions to the events so we need to create them as well.  

```
function mouseOver(d) {}

function mouseOout(d) {}
```

Although the actual function assigned within the event listener doesn't appear to have been passed any parameters it is indeed being passed one directly as well as two additional ones indirectly. 

- d : this is the data assigned to the 
- this : current element
- event : the event that occured and it's properties

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

Since **this** represents the element that triggered the event we can use **d3.select(this)** to grab it and transition it's properties. The properties were looking to alter are:

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

Although the above code makes the changes it does so abruptly which isn't visually appealing.  In order to transition those properties we will use **.transition()** to indicate that the sand **.duration()**

```
function mouseOver(d) {
	d3.select(this)    
	 .transition().duration(1000)
	  .attr("stroke-width", 20)
	  .attr("stroke", "rgba(230,230,230, .8)")
	  .attr("r", 15);
}
```

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

```
function mouseOver(d) {
 // previous code

  createLineY(d);
  createLineX(d); 
} 
```

```
function createLineY(d) {
 const lineY = gMain.append("line");
 lineY.datum(d).attr("class", "lineY");
}
```

```
function createLineY(d) {
 const lineY = gMain.append("line");
 lineY.datum(d).attr("class", "lineY");
 
 lineY
  .attr("x1", xScale(+lineY.datum()["2002"]))
  .attr("y1", yScale(+lineY.datum()["2012"]))
  .attr("x2", xScale(-5))
  .attr("y2", yScale(+lineY.datum()["2012"]));
}
```

```
function createLineY(d) {
 const lineY = gMain.append("line");
 lineY.datum(d).attr("class", "lineY");
 
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

```
function createLineY(d) {
 const lineY = gMain.append("line");
 lineY.datum(d).attr("class", "lineY");
 
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

```
  function createLineX(d) {
    var lineX = gMain.append("line").datum(d)
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
[D3 Lines](https://github.com/d3/d3-shape#lines)
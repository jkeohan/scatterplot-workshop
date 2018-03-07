## Adding Circle And Line Transitions 

#### Starter Code

The starter code for this section has been provided so please fork the following codepen: 

[D3 - Scatterplot - Circle\Line Transitions - Starter](https://codepen.io/jkeohan/pen/NyZvNL)

#### Intro

We learned in the last module that scales are an important when trying to plot data within a defined range of space. That works for rendering the data but the graph still needs an additional component to give the data context and that would be x\y axes.  

#### Getting Started

Once again let's take a look at the final design and use it to help define what we think needs to be done. 


#### Adding Mouseover\Mouseout Events

```
circlesEnter
 .on("mouseover", mouseOver)
 .on("mouseout", mouseOut);
```

```
function mouseOver(d) {

}

function mouseOout(d) {

}
```

```
function mouseOver(d) {
	console.log(d);
	// => {2002: "49.5", 2012: "46.9", Location: "Norway", Region: "Scandanavia"}
	
	console.log(this)
	// => <circle cx=​"186.33809523809526" cy=​"113.28" r=​"5" stroke-width=​"1" stroke=​"rgba(230,230,230, .8)​" title=​"Norway" style=​"fill:​ rgb(140, 86, 75)​;​">​</circle>​
}
```

#### Circle Transitions

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

[D3 Lines](https://github.com/d3/d3-shape#lines)
## Adding Axes

#### Starter Code

The starter code for this section has been provided so please fork the following codepen: 

[D3 - Scatterplot - X\Y Axes - Starter](https://codepen.io/jkeohan/pen/yvdyyr)

#### Intro

We learned in the last module that scales are an important when trying to plot data within a defined range of space. That works for rendering the data but the graph still needs an additional component to give the data context and that would be x\y axes.  

#### Getting Started

Once again let's take a look at the final design and use it to help define what we think needs to be done. 

- width and height of scatterplot need to be adjusted (decreased)
- axes need to be drawn along with axis titles

#### Implement The X\Y Axes


```
const m = { left: 80, right: 20, top: 20, bottom: 70 };
```

```
  // Add the Y Axis
  gMain.append("g").call(
    d3.axisLeft(yScale)
  );
```

D3 provides some [additional methods](https://github.com/d3/d3-axis) to format the styling and output of the axes.   Were going to use use the following:

- tickFormat() : 
- tickPadding() : 
- ticks() : 
- tickSizeOuter()

```
  gMain.append("g").call(
    d3.axisLeft(yScale)
    .tickFormat(function(d) {
      return d + "%";
    })
    .tickPadding([10])
    .ticks(5)
    .tickSizeOuter(0)
```

```
	// Add the X Axis
  gMain
    .append("g").classed('xAxis', true)
    .attr("transform", "translate(0," + height + ")" )
    .call(
      d3.axisBottom(xScale)
    .tickFormat(function(d) {
        return d + "%";
      })
    .tickPadding([10])
    .ticks(5)
    .tickSizeOuter(0)
    );
```


```
 gMain
    .append("g").classed('yLabel',true)
    .append("text")
    .attrs({
      y: -60,
      x: -(height / 2),
      dy: ".71em",
      "text-anchor": "middle",
      "transform": "rotate(-90)",
      'font-size':15
    })
    .text("2012 Renewable Energy Output")
```

```
xScale.domain([-5, maxX + 10]).range([0, width]);
yScale.domain([-5, maxY + 10]).range([height, 0]);
```
#### Solution Code

Here is the full solution code for the project thus far:

[D3 - Scatterplot - X\Y Axes - Solution ](https://codepen.io/jkeohan/pen/BYejOg)

#### References

[placeholder for official d3 axes docs]()
[D3 Axes](https://github.com/pshrmn/notes/blob/master/d3/axes.md)

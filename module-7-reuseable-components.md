### Alternate Legend Solution Using D3-Legend

Since legends are something that most people incorporate when building charts and graphs and they are require only a handful of code to create it seems like the perfect choice for making them reusable.  This is just what Susie Lu did when she built the [d3-legend](http://d3-legend.susielu.com/) library.  The library provides standard legend solutions that are configrable and easy to implement.  Let's take a moment and refactor the previous Vertical legend example ot leverage d3-legend. 

#### CodePen Starter Code

Here is the starter code for the vertical legend using d3-legend:

[D3 - Vertical Legend - D3-Legend - Starter](https://codepen.io/jkeohan/pen/EQeaMg?editors=0010)

#### Initial Setup

So the first thing we will do if import the d3-legend.js library.  To do this we will add the following url add an additional JS library in our CodePen starter code. 

```
https://cdnjs.cloudflare.com/ajax/libs/d3-legend/2.25.5/d3-legend.min.js
```

#### The Refactor

Since the renderLegend function is what we used to build out the legend that's what we are going to refactor.  First were going to add the legendValues to the color domain as d3-legend requires a scale to build out it's legend. 

```
colorScale.domain(legendValues)
```

Next we will edit the transform\translate to move it down on the y axis a bit. 

```
let legend = svg.append("g").attr("class","legend")
  .attr("transform", "translate(20,20)");
```

Now we will focus on using d3-legend to build out the legend.  We will first create a new variable to store the legend which we will then use .call() to pass the svg and build out the legend.  There are a few different types of legends that are provided and were focused on building a basic ordinal color legend so were going to use [d3.legendColor()](http://d3-legend.susielu.com/#color-ordinal).  

```
let legendOrdinal = d3.legendColor()
```

It provides several additional methods that we will use to configure the shape used, padding, passing it the scale and event listeners  

Let's add the rectangles and use .shape() to pass it a specific shape using 

<details>
<summary>Full Solution</summary>

```
function renderLegend(legendValues) {
  colorScale.domain(legendValues)
  let legend = svg.append("g").attr("class","legend")
  .attr("transform", "translate(20,20)");

  let legendOrdinal = d3.legendColor()
  .shape("path", d3.symbol().type(d3.symbolSquare).size(100)())
  .shapePadding(2)
  .scale(colorScale )
  .on("cellclick", function(d){alert("clicked " + d);});;
  
  svg.select(".legend")
  .call(legendOrdinal);
}
```
</details>

### Additional Legend Resources

- [d3-legend](http://d3-legend.susielu.com/#color-ordinal)
- [d3noob.org](http://www.d3noob.org/2014/02/styles-in-d3js.html)

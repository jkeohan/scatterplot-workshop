## Reusable Components - (EST: 30 min)

D3V4 is a collection of modules that are designed to work together; you can use the modules independently, or you can use them together as part of the default build.  The modules include components that provide the user a degree of reusability.  The D3 community has also extended this concept by creating additional components that facilitate creating elements such as tooltips, legends, maps, ect...

In this module we will explore some of these reusable components as well as create our own reusable legend component. 

### The D3-Legend Component 

Since legends are something that most people incorporate when building charts and graphs and they are require only a handful of code to create it seems like the perfect choice for making them reusable.  This is just what Susie Lu did when she built the [d3-legend](http://d3-legend.susielu.com/) library.  The library provides standard legend solutions that are configrable and easy to implement.  Let's take a moment and refactor the previous Vertical legend example ot leverage d3-legend. 

#### Starter CodePen 

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

It provides several additional methods that we will use to configure the shape used, padding, passing it the scale and event listeners.  

Let's add the rectangles using the .shape() method.  In d3v4 the **d3-shape** module was added and includes generators to create shapes such as arcs, pie, lines and amongst other things some commonly used symbols.  Below the shape method is passed the **path** attribute and uses d3 to generate the shape. 

```
let legendOrdinal = d3.legendColor()
  .shape("path", d3.symbol().type(d3.symbolSquare).size(100)())
```

The additional methods we will add to complete the legend are: .shapePadding(), .scale() and .on()

```
let legendOrdinal = d3.legendColor()
  .shape("path", d3.symbol().type(d3.symbolSquare).size(100)())
  .shapePadding(2)
  .scale(colorScale)
  .on("cellclick", function(d){alert("clicked " + d);});;
```

With the legend properly configured all that's left to do is pass it the svg element using .call().

```
svg.select(".legend").call(legendOrdinal);
```

<details>
<summary>Full Solution</summary>

```
function renderLegend(legendValues) {
  let legend = svg.append("g").attr("class","legend")
  .attr("transform", "translate(20,20)");

  colorScale.domain(legendValues)

  let legendOrdinal = d3.legendColor()
  .shape("path", d3.symbol().type(d3.symbolSquare).size(100)())
  .shapePadding(2)
  .scale(colorScale)
  .on("cellclick", function(d){alert("clicked " + d);});;
  
  svg.select(".legend").call(legendOrdinal);
}
```
</details>

#### Solution CodePen 

Here is the solution code for the vertical legend using d3-legend:

[D3 - Vertical Legend - D3-Legend - Solution](https://codepen.io/jkeohan/pen/rJZaYN?editors=0010)

### Creating Our First Reusable Component 

I'm sure by now you've come to the conclusion that it's much easier to incorporate a reusable legend then create your own from scratch and with that in mind let's take a step back and create our own reusable legend.

#### Starter CodePen 

Here is the starter code for the vertical legend using d3-legend:

[D3 - Custom Reusable Legend - Starter](https://codepen.io/jkeohan/pen/EQeaMg?editors=0010)

#### Additional Resources

- [d3-legend](http://d3-legend.susielu.com/#color-ordinal)
- [d3-shape](https://github.com/d3/d3-shape)
- [dragging d3 shapes](https://bl.ocks.org/feyderm/4d143591b66725aed0f1855444752fd9)

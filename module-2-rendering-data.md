## Rendering Data 

#### Starter Code

The starter code for this section has been provided so please fork the following codepen: 

[D3 - Scatterplot - Rendering Data - Starter](https://codepen.io/jkeohan/pen/wyOzxM?editors=0010)

#### Intro

The most important feature of D3 is most notably it's ability to represent data in many different forms such as circles, rectangles, lines, ect.  Although these basic shapes are almost always enough to build out the most common charts and graphs they can be used in innovative ways that allow one to add flare to their visualization such as **ex1** or **ex2**

#### Functionality Defined

Although building out a basic scatterplot is pretty straighforward using D3 there are things that we should consider that will affect how we structure the code for both this design and for future resuability. Let's make sure we first understand the full functionality of our visualization before we start adding additional lines of code. 

Here is the [final version](https://codepen.io/jkeohan/pen/OQwwKO?editors=0010) of the scatterplot so let's take a moment to first examine it and then come up with a list of functionality that we will need to build out. 

Here is a list of the functionality that has been implemented:

- Renders basic circles color coded by region
- Legend with regional names and defined colors
- X\Y Axes and axis titles
- Filtering and rerendering data
- Animations for circles, lines, opacities, axes

#### Getting Started

The objective of this module isn't to implement all the functionality defined above but to both structure our code in such a way that takes into consideration our final design. For this module we will focus on the following specifically:

- create\append an svg and g element
- render the data as the circles

We are going to be writing quite of bit of code in this module and it will fall into several buckets:

- code that runs only once on page load
- code that runs on every time the data set changes

Let's start with the code that only runs once on page load. 

#### Code Run On Page Load Only

Several elements are going to be defined that need to key to both support generating the svg\g elements but also for our need to scale the data to readjust according to our responsive design.  Something that you will see quite often in the code used to build out a D3 desing is defining values that we can use to help position g elements.  So let's create a new const variable and assign some values. 

```
const m = { left: 20, right: 20, top: 20, bottom: 20 };
```

The left and bottom values are so that we make space for the x\y axis text. Most of the values will be increased as we add add the axes and legend but are suitable for the time being for a basic scatterplot. 

The svg that will contain our visualization will be appended to a div with a class of ".scatterplot" so let's use d3 to grab that element from the DOM using **d3.select()**.  Once it's grabbed we will also console.log() the output. 

```
const scatterplot = d3.select(".scatterplot");
console.log('this is scatterplot:', scatterplot);

// => pt {_groups: Array(1), _parents: Array(1)}
```

The console output shows how D3 stores the data anytime .select() is used. It's clearly aware of the elements themselves as well as the parent element.  If we examine it a bit further we can see all of it's properties. 

Now as part of the responsive design layout we want to append an svg that uses the width and height of it's parent so that's an additional set that needs to be performed before we draw the svg.  D3 provides the **.node()** method to obtain the properties of a DOM element so let's add that and console.log the results.

```
let node = scatterplot.node();
console.log('this is node: ', node);

// => <div class="scatterplot"></div>
```

The output is very different from .select() and shows the full html element but none of it's properties.  Although visually this is the case we can access all the property values that we saw when using .select(), such as **clientWidth** and **clientHeight**.  I prefer storing those 2 values as a collection (array) and then create new variables that will reference them when needed. 

```
let svgDimensions = [node.clientWidth, node.clientHeight];
let svgWidth = svgDimensions[0];
let svgHeight = svgDimensions[1];
```

Now it's just a matter of appending an svg and assigning it a width and height. 

```
let svg = scatterplot.append("svg")
  .attrs({ width: svgWidth, height: svgHeight })
```

Once that is done we should see an svg that is the width\height assigned to the scatterplot in css. A black border was previously added in css for the svg so that we can clearly make out it's dimensions. 

Although we could create all our elements directly within the svg a much better approach is to group them using g elements.  That allow us to move whole segments of elements at once vs defining those new coordinates per element. So let's add our main g element. 

```
let width = svgWidth - (m.left + m.right);
let height = svgHeight - (m.top + m.bottom);

let gMain = svg.append("g").attr('class','gMain')
	.attr("transform", "translate(" + m.left + "," + m.top + ")");
```

Were only going to add a few more lines of code that are needed to generate and position the circles and they are both scales.

```
let colorScale = d3.scaleOrdinal(d3.schemeCategory10);
let xScale = d3.scaleLinear()
let yScale = d3.scaleLinear()
```

D3 has many different types of [scales](https://github.com/d3/d3-scale) and they could be the topic of their own module so we won't get into the finer concepts of scales but only the two that we have defined above. The two scales defined are **scaleLinear()** and **scaleOrdinal()**.  A linear scale was assigned to the x\y axes as this represents a continuous that  maps a quantitative input domain to a continuous output range.  An ordinal scale was assigned to the legend as it represents an explicit mapping from a set of data values to a corresponding set of visual attributes (such as colors). We will see them both in action shortly so be patient. 

#### Code To Render Data

In module 1 we created the **render** function which we used only to view the data via the console but now it's time to draw those data points as circles.  The first thing we need to do is determine what the max value of all circles for years 2002 and 2012 in the current data set.  Let's keep in mind that these values will change as the original data set is filtered based on region so it's something that needs to be run everytime we render the circles. Those values will them be passed onto the scales to proportionately draw the data.  

```
const maxX = d3.max(data, d => +d["2002"]);
const maxY = d3.max(data, d => +d["2012"]);
```

Now that we have those values let's pass them to the x\y scale as well as set the range of available space to draw the data.  Scales use **.domain()** for the actual data and **.range()** for the available space.  

```
xScale.domain([0, maxX]).range([0, width]);
yScale.domain([0, maxY]).range([height, 0]);
```

Now it's time to draw the circles.  D3 is know for it's ability to bind to data and keep track of new data entering, existing data needing updating and data that needs to be removed otherwise refernced as it's Enter\Update\Exit cycle.  The initial data binding process is done by selecting all the elements and then using **.data()** to bind to the data set. 

```
// DATA BINDING
let circles = gMain.selectAll("circle").data(data);
```

After data binding has been done it's time to have the circle enter using **.enter()**

```
// ENTER PHASE
const circlesEnter = circles.enter().append("circle")
```

However circles in an svg require a cx,cy and r values to be drawn so let's add that as well.

```
const circlesEnter = circles.enter().append("circle")
	.attrs({
	  cx: (d, i) => xScale(d["2012"]),
	  cy: (d, i) => yScale(d["2002"]), 
	  r:5
	 })
```

Although the circles are drawn there is no way to distinquish which circle are related so we need to assign a few more properties such as: **stroke**, **stroke-width** and **fill**.

```
const circlesEnter = circles.enter().append("circle")
	.attrs({
	  cx: (d, i) => xScale(d["2012"]),
	  cy: (d, i) => yScale(d["2002"]), 
	  r:5,
     'stroke-width':1, 
      stroke: 'rgba(230,230,230, .8)',
      'fill': d => colorScale(d.Region)
    })
```

Even though were not presently filtering the data set we are going to handle updating cirlces when the time comes.

```
const circlesUpdated = circlesEnter.merge(circles)
	.attrs({
	  cx: (d, i) => xScale(d["2012"]),
	  cy: (d, i) => yScale(d["2002"]), 
    })
``` 

#### Solution Code

Here is the full solution code for the project thus far:

[D3 - Scatterplot - Rendering Data - Solution ](https://codepen.io/jkeohan/pen/BYeNvp?editors=0010)

#### References

[update-enter-merge-exit](https://bl.ocks.org/EmbraceLife/efb531e68ce46c51cb1df2ca360348bb)
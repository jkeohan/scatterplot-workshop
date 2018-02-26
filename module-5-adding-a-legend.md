## Adding A Legend - (EST: 60 min)

Here is the starter code we will use for this section for the time being, in order to focus on creating just the legend.  Once this has been completed we will reimplement the legend in the context of the scatterplot.

**Starter Code:** [D3 - Vertical Legend - Starter](https://codepen.io/jkeohan/pen/qxYeNj?editors=0010)

One important feature of any visualization is the ability to quicky associate the data based on a related property value.  Thus far, the scatterplot has, in part, achieved this by assigning the countries a specific color based on their region. This relationship, however, has not yet been conveyed to the user and a legend is the best way to quickly establish this association. 

### Append An SVG

The first thing we need to do is grab the DOM element where we will append the svg.

```
const chart = d3.select(".chart");
```

In order to add some responsiveness to the svg we will set its width/height to that element's defined width/height. D3 uses the .node() method to grab that element's properties and then we can use .clientWidth & .clientHeight to obtain those respective values. 

```
const node = chart.node();
const width = node.clientWidth;
const height = node.clientHeight;
```

Since our goal is to assign colors to each legend item we also need to add a color scale.

```
const colorScale = d3.scaleOrdinal(d3.schemeCategory10);
```

Finally, we append the svg and set its width and height.

```
const svg = chart
  .append("svg")
  .attrs({ height, width });
```

### Extracting Region Values From Dataset

Each item in the existing dataset array is an object with key/value pairs. The key that stores the values we need is called: **Region**.

Let's create a new function called **renderLegend** that takes in our current dataset and console logs it to confirm it's working.

```
function renderLegend(data) {
 console.log(data)
}
```

Now inside of the **getData** function let's add the function to the if/else statement. 

```
if (storage) {
 storage = JSON.parse(storage);
 legendData(storage);
} else {
   d3.csv(url, function(data) {
    localStorage.setItem("countries", JSON.stringify(data));
    legendData(data);
   });
}
```

The output is something we've already seen in the previous iterations of the scatterplot, however, I removed years 2003-2011 to keep it concise.

```
{2002: "0.4", 2012: "0.7", Location: "Korea", Region: "Asia"}
```

### Using d3.set

D3 offers a set of convenience methods for manipulating data which are available in the full d3 library or in the **d3-collection module**.  One method in particular is [d3.set](https://github.com/d3/d3-collection/blob/master/README.md#set) which is like ES6 Sets in that it creates a new object that stores unique values. 

Let's use d3.set to create a new set and then filter it to remove duplicate region names as well as filter out the "World" object.   

```
const legendValues = d3.set(
 data.map(d => d.Region)
  .filter(function(d) {
    return !(d == "World");
  })
)

// OUTPUT
// legendValues => {$Asia: "Asia", $Europe: "Europe", $Latin America: …}
```

Although the key/value pairs are unique, it's not in an array format the d3 expects.  A few additional methods that we can append will be **.value()** to return an array of unique values, and **.sort()** to sort them in alphabetical order.


```
const legendValues = d3.set(
  data.map(d => d.Region)
   .filter(function(d) {
     return !(d == "World");
    })
 ).values().sort()

// OUTPUT
// legendValues => ["Asia", "Europe", "Latin America", "Scandanavia", "Africa",...]
```

Only one last line needs to be added which will call the function responsible for rendering the actual legend.  It is called **renderLegend** and will be passed  **legendValues**.

```
function renderValues(data) {
  // ...previous code   
  renderLegend(legendValues)
}
```

### Creating The renderLegend Function

Let's create the renderLegend function. Its first task will be to append a **g** element to the svg with a class of **legend**.

```
function renderLegend(legendValues) {
  const legend = svg.append("g").attr('class','legend')
}
```

With our container elements in place, it's time to do some data binding and add the rect and text elements. We're going to use additional g elements as a wrapper for both the rect and text which provides the ability to move them both together as a group instead of each one individually. Scales aren't being used either so we are going to use a multiple of the index value to position the g elements along the y axis. 

```
const legendItems = legend
  .selectAll("legendItem")
  .data(legendValues)
 .enter()
  .append("g")
  .attr("class", "legendItem")
  .attr("transform", (d, i) => {
    return "translate(0," + i * 20 + ")";
  });
```

#### Appending The Rect and Text Elements

Appending the rect and text elements is pretty straightforward. We're going to use colorScale to assign a unique color to each rectangle. 

```
legendItems.append("rect")
  .attrs({ x: 5, y: 5, width: 10, height: 10 })
  .style('fill',d => colorScale(d) )
    
legendItems.append("text")
  .attrs({ x: 25, y: 15 })
  .text((d,i) =>  { return d );
```

#### Full Sample Legend Solution

Below is the full solution for implementing a legend. 

<details>
<summary>Full Solution</summary>

```
const chart = d3.select(".legend");
const node = chart.node();
const width = node.clientWidth;
const height = node.clientHeight;
const colorScale = d3.scaleOrdinal(d3.schemeCategory10);
const svg = chart
  .append("svg")
  .attrs({ height: height });

function legendValues(data) {
  legendValues = d3
    .set(
      data.map(d => d.Region).filter(function(d) {
        return !(d == "World");
      })
    )
    .values().sort(d3.decscending);
  renderLegend(legendValues);
}

function renderLegend(legendValues) {
  let legend = svg.append("g").attr("class","legend");

  let legendItems = legend
    .selectAll("legendItem")
    .data(legendValues)
    .enter()
    .append("g")
    .attr("class", "legendItem")
    .attr("transform", (d, i) => {
      return "translate(0," + i * 20 + ")";
    });

  legendItems
    .append("rect")
    .attrs({ x: 5, y: 5, width: 10, height: 10 })
    .style("fill", d => colorScale(d));

  legendItems
    .append("text")
    .attrs({ x: 25, y: 15 })
    .text((d, i) => {
      return d;
    });
}

function getData() {
  const url =
 "https://gist.githubusercontent.com/jkeohan/974c071a5d4d0185a846/raw/971a9b8dfc0ebe238ee271611991cd98e6cac434/data_regions.csv";
  let storage = localStorage.getItem("countries");
  if (storage) {
    storage = JSON.parse(storage);
    storage.sort(function(x, y) {
      return d3.ascending(x["2002"], y["2002"]);
    });
    legendValues(storage);
  } else {
    d3.csv(url, function(data) {
      localStorage.setItem("countries", JSON.stringify(data));
      legendValues(data);
    });
  }
}

getData();
```
</details>


#### CodePen Solution Code

Here is the full solution for the vertical legend:

[D3 - Vertical Legend - Solution](https://codepen.io/jkeohan/pen/oEEvJB?editors=0010)


### Applying The Legend To Scatterplot

Now it's time to apply the legend to the scatterplot.  We will be working with the below starter code for the scatterplot so please fork the following codepen and then we will begin: 

**Starter Code:** [D3 - Scatterplot - Legend - Starter](https://codepen.io/jkeohan/pen/qxXRzB)

#### Making Room For The Legend

In order to make room for the legend the svg width needs to be increased while still maintaining the chart's existing width.  To do this, the svg width has already been increased in CSS from 400 => 600px.  

Now we reset the chart width by increasing the right margin:

```
// PREVIOUS 
// const m = { left: 80, right: 20, top: 20, bottom: 70 };

// UPDATED RIGHT VALUE TO PROVIDE SPACE NEEDED FOR LEGEND
const m = { left: 80, right: 150, top: 20, bottom: 70 };
```

#### Adding Supporting Functions

Since the functions needed to generate a legend have already been created, all we need to do now is copy/paste them below the render function and then call the **legendData** function inside the **getData** function.

```
// DISPLAY LEGEND
function legendData(data) {
    //...legendData code
}
function renderLegend(legendValues, data) {
    //...renderLegend code
}

// Remember to add legendData to getData so that it's initially run on first render
```

At this point, the legend should have been drawn with the colors conveying the relationship between the circles and their corresponding region. 

#### CodePen Solution Code

Here is the full solution code for the project thus far:

[D3 - Scatterplot - Legend (Non-Interactive) - Solution ](https://codepen.io/jkeohan/pen/wyyRJa?editors=0010)

### Additional Legend Resources

- [d3-legend](http://d3-legend.susielu.com/#color-ordinal)
- [d3noob.org](http://www.d3noob.org/2014/02/styles-in-d3js.html)

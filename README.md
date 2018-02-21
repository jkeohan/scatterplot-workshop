# Building A Scatterplot Using D3

## Importing Data & Rendering Circles

## Adding X & Y Scales

## Adding X & Y Axes

## Adding Circle & Line Transitions

## [Adding A Tooltip](#adding-a-tooltip-60min) 

## [Adding A Legend](#adding-a-legend-60min)


## Adding A Tooltip 60min

The starter code for this section has been provided so please fork the following codepen: 

**Starter Code:** [D3 - Scatterplot - ToolTip - Starter](https://codepen.io/jkeohan/pen/wyWjXq)

Tooltips help elicit additional information about a specific data point. In the case of the scatterplot it will be used to show the percent output of the country for years 2012 & 2002.  It can however to be extended and show all the years and their corresponding values as a table or even a mini bar chart.  When approaching the design one should always consider what information would be most relevant and\or useful to the user as they investigate the data. 

Adding a tooltip will require that we do the following:

- Implement the tooltip design using HTML\CSS
- Create a **displayToolTip** function to render the tooltip
- Extend **mouserOver** function to call the displayToolTip function
- Extend the **mouseOut** function to remove the tooltip

### Implement the Tooltip design 

The design were looking to implement is the following:

<img src="http://res.cloudinary.com/jkeohan/image/upload/v1518555150/Screen_Shot_2018-02-13_at_3.45.45_PM_yada2o.png" width="150" />

Since recreating the above design would add additional time to the tutorial, the following [CodePen](https://codepen.io/jkeohan/pen/wyqpPj) has been provided with all the required HTML\CSS. It will however require some editing to be dynamic and show the info specific to that element. 

Let's first use the below static html to get a tooltip up and running and then we will refactor to make it dynamic.  

```html
<div  class="d3tooltip">
  <span class="regionName">Norway</span>
  <hr class="d3tooltiphr" style="border: 2px solid #ffbb78"> 
  <span class="key">2002:</span> <span class="value">70%</span><br/>
  <span class="key">2012:</span> <span class="value">84%</span><br/>
</div>  
```  

#### Adding the displayToolTip function

With the basic template in place we can now create the function responsible for generating the tooltip. We will create the basic function for now, add a console.log to confirm it's receiving the circle element and then slowly expand on it to include the additional elements as needed. 
 
Here is the basic structure:

```js
function displayToolTip(selection) {
	console.log(selection)
}
```

Let's now call the function in the **mouseOver** assigned to the circle and pass it the **d3.select(this)** object.

```javascript
function mouseOver(d) {
 // ....previously added code here
 	displayToolTip(d3.select(this))
}
```

Mousing over a circle now will console.log the element, so let's take a look and see the output. 

<img src="http://res.cloudinary.com/jkeohan/image/upload/v1518568508/Screen_Shot_2018-02-13_at_7.34.24_PM_gzqmjd.png" width="400"/>



The pieces of information we now need from the element are:

- the actual data 
- it's current cx & cy coordinates

The data can be enumerated using the elements .datum() method and the coordinates using .attr().

```
const d = selection.datum()
const cx = +selection.attr("cx")
const cy = +selection.attr("cy")
```
 
The last thing that needs to be done are in order to render the tooltip are the following:

- append a div with the previous html
- position it using the cx\cy coords
- update html to replace color and values

Creating and positioning the tooltip is fairly easy and is done as follows:

```
const tooltip = d3.select(".regionalstats")
	.append("div").attr("class","d3tooltip")

tooltip   
	.style("opacity",0)
	.style('left', (cx + 160) + 'px')
	.style("top", (cy + 30) + "px") 
```

Opacity was added so that we have something to transition. The extra values added to both cx\cy are done so that the tooltip is positioned right and vertically centered with the element. 


#### Refactoring the HTML to be Dynamic

All that's left now is to add the html and dynamically replace the following:

- d3tooltiphr border color
- values for both 2002 and 2012

 The **d** variable defined earlier is an object that contains the data needed to replace the static values and of course a transition has been added for aesthetics:
 
 ```
  const tooltip = d3.select(".regionalstats").append("div").attr("class","d3tooltip")
  
  tooltip   
	    .style("opacity",0)
	    .style('left', (cx + 160) + 'px')
	    .style("top", (cy + 30) + "px") 
	    .style("border" , "3px solid " + d.color )
	   
  tooltip.append('span').attr('class','regionName').html(d.Location)
  tooltip.append('hr').attr('class','d3tooltiphr').style('border',`2px solid ${d.color}`)
  tooltip.append('span').attr('class','key-2002').html('2002')
  tooltip.append('span').attr('class','value').html(`${d['2002']}%`).append('br')
  tooltip.append('span').attr('class','key-2012').html('2012')
  tooltip.append('span').attr('class','value').html(`${d['2012']}%`)
 ```

Of course a smooth transition is what were looking for so one more line is all we need to change it's opacity.

```
tooltip.transition().duration(1000).style("opacity",1)
```

#### Removing the Tooltip

I suppose the last thing needed is to remove the tooltip on **mouseout**.  Create the following function and add it as the last line to the mouseout function.

```
function removeToolTip(){
   d3.selectAll(".d3tooltip")
     .transition().duration(1000)
     	.style("opacity",0).remove( )
}
```

#### Solution Code

Here is the full solution code for the project thus far:

[D3 - Scatterplot - v4 - ToolTip - Solution ](https://codepen.io/jkeohan/pen/paNpyN?editors=0110)

#### Additional ToolTip Resources

Here are some additional resources on d3 tooltips:

- [d3-tip](https://github.com/Caged/d3-tip) - a resuable tooltip component
- [blockbuilder.org - filtered by d3-tip](http://blockbuilder.org/search#d3modules%3Dd3-tip%2Cd3-legend)
- [D3.js Step by Step: Adding Tooltips](http://zeroviscosity.com/d3-js-step-by-step/step-5-adding-tooltips)

## Adding A Legend 60min

Here is the starter code for this section that we will use for the time being in order to focus on creating just the legend.  Once this has been completed we will reimplement the legend in the context of the scatterplot.

**Starter Code:** [D3 - Vertical Legend - Starter](https://codepen.io/jkeohan/pen/qxYeNj?editors=0010)

One important feature of any visualization is the ability to quicky associate the data based on a related property value.  Thus far the scatterplot has, in part, achieved this by assigning the countries a specific color based on their region. This relationship however has not yet been conveyed to the user and a legend is the best way to quickly establish this association. 

### Append An SVG

The first thing we need to do is grab the DOM element where we will append the svg.

```
const chart = d3.select(".chart");
```

In order to add some responsiveness to the svg we will set it's height\width to that elements defined width\height. D3 uses the .node() method to grab that elements properties and then we can use .clientWidth & .clientHeight to obtain those respective values. 

```
const node = chart.node();
const width = node.clientWidth;
const height = node.clientHeight;
```

Finally we append the svg and set it's height and width.


```
const svg = chart
  .append("svg")
  .attrs({ height, width });
```

### Extracting Region Values From Dataset

Each item in the existing dataset array is an object with key\value pairs and the key that stores the values we need is called: **Region**

D3 offers a set of convenience methods for manipulating data which are available in the full d3 library or in the **d3-collection module**.  One method in particular is [d3.set](https://github.com/d3/d3-collection/blob/master/README.md#set) which is like ES6 Sets in that it creates a new object that stores unique values. 

Let's create a new function called **renderLegend** that takes in our current dataset and console logs it to confirm it's working.

```
function renderLegend(data) {
 console.log(data)
}
```

Now inside of the **getData** function let's add the function to the if\else statement. 

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

The output is something we've already seen in the previous iterations of the scatterplot however i've removed the years from 2003 - 2011 to keep it concise.

```
{2002: "0.4", 2012: "0.7", Location: "Korea", Region: "Asia"}
```

### Using d3.set

Now let's use d3.set to create a new set and then filter it to remove duplicate region names as well as filter out the "World" object.   

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

Although the key\value pairs are unique it's not in an array format the d3 expects.  A few additional method that we can append will be **.value()** to return an array of unique values and **.sort()** to sort them in alphabetical order.


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

Only one last line needs to be added which will call the function reponsbible for renering the actual legend.  It is called **renderLegend** and will be passed  **legendValues**.

```
function renderValues(data) {
	// ...previous code	
	renderLegend(legendValues)
}
```

### Creating The renderLegend Function

Let's create the renderLegend function and it's first task will be to append a **g** element to the svg with a class of **legend**

```
function renderLegend(legendValues) {
	const legend = svg.append("g").attr('class','legend')
}
```

With our container elements in place it's time to do some data binding and add the rect and text elements. Were going to use additional g elements as a wrapper for both the rect and text which provides the ability to move them both together as a group instead of each one individually. Scales aren't being used either so we were going to use a multiple of the index value to position the g elements along the y axis. 

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

Appending the rect and text elements are pretty straightforward and were going to use colorScale to assign a unique color to the each rectangle. 

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

getData();
```

#### CodePen Solution Code

Here is the full solution code for the project thus far:

[D3 - Vertical Legend - Solution ) - Solution ](https://codepen.io/jkeohan/pen/oEEvJB?editors=0010)

</details>


### Applying The Legend To Scatterplot

Now it's time to apply the legend to the scatterplot.  We will be working with the below starter code for the scatterplot so please fork the following codepen and then we will begin: 

**Starter Code:** [D3 - Scatterplot - Legend](https://codepen.io/jkeohan/pen/qxXRzB)

#### Making Room For The Legend

In order to make room for the legend the svg width needs to be increased while still maintaining the charts existing width.  To do this the svg width has already been increased in CSS from 400 => 600px.  

Now we reset the chart width by increasing the right margin:

```
// PREVIOUS 
// const m = { left: 80, right: 20, top: 20, bottom: 70 };

// UPDATED RIGHT VALUE TO PROVIDE SPACE NEEDED FOR LEGEND
const m = { left: 80, right: 150, top: 20, bottom: 70 };
```

#### Adding Supporting Functions

Since the functions needed to generate a legend have already been created all we need to do now is copy\paste them below the render function and then call the **legendData** function inside the **getData** function.

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

At this point the legend should have been drawn with the colors conveying the relationship between the circles and their corresponding region. 

#### CodePen Solution Code

Here is the full solution code for the project thus far:

[D3 - Scatterplot - Legend (Non-Interactive) - Solution ](https://codepen.io/jkeohan/pen/wyyRJa?editors=0010)


## Filtering Data Based On Legend Choice 

The starter code for this section has been provided so please fork the following codepen: 

[D3 - Scatterplot - Legend (Interactive) - Starter](https://codepen.io/jkeohan/pen/vddbmG)

Although animations almost always enhance the end user experience they should be applied based on end user expectations.  Users that have already worked with interactive data visualizations expect that some functionality be provided that allows them to filter the data set. The most intuitive way to do this in our current scatterplot would be to click on the legend values so that is where we will be implemented. 

The user iteraction with the legend that we were looking to implement in this scenario will involve the user clicking on one legend item which rerender of the scatterplot displaying only those countries from the choosen region.   The user can click additional regions which will once again filter the data set and cause a rerender.  One thing to consider is how to get the user back to viewing all the regional data.  In order to make that happen the user will only need to click the same region a second time.  Hopefully this will be an intuitive choice but if not we can always provide a **reset** button or link that would provide a clear and direct path to reseting the scatterplot. 

###  Steps To Filter Data

Here is a breakdown of the steps we will perform to implement the filtering and redrawing of the scatterplot based on the users selected region:


- Create a new variable called **activeLegend** to keep track of which legend is currently active.
- Add **.onclick** event to legendItems that will call a new function called **filterByRegion** and store the results in the **region** variable
- Create the **filterByRegion** function that will return a new array of cities filtered by region
- Create a new function called **legendTranstion** that will transition the opacity of the legend items to indicate which one is active 
- Call the **render** function passing it the filtered dataset


#### Adding onclick Event

Before we add the click event let's just take a moment and create the **activeLegend** variable.  We will set it to an empty string and reassign it with the user selected region. It will be reset to an empty string when the user clicks the same region again. 

```
let activeLegend = "";
```

Now let's add an the onclick event and create the **region** variable that calls the **filterByRegio** function. The function will require the whole dataset and the current object which is represented as **d**.
```
let legendItems = legend
 // ...PREVIOUS CODE
 .on("click", function(d) {
    const region = filterByRegion(data, d);
  });;
```

#### Creating The filterByRegion Function

Clicking on any legend items at this point will display an error in the console being that the filterByRegion function has not yet been created so let's add it as the last line of **renderLegend** function.

```
function filterByRegion(data, region) {
 
}
```

The function will need to run some conditonal logic to first determine if the activeLegend variable has already been set to the current choice and if so do the folowing:

1.  reset activeLegend to an empty string 
2.  return the whole data set. 

If not then do the following:

1. filter the data set based on the region choice
2. set the activeLegend variable to that region
3. return the filtered data set


```
function filterByRegion(data, region) {
  if (activeLegend == region) {
    activeLegend = "";
    return data;
    } else {
      let filteredData = data.filter(d => d["Region"] == region);
      activeLegend = region;
      return filteredData;
    }
}
```

One last thing we need is to transition the legend items opacity to visually indicate to the user which region is currently active.  To do that we will add the **legendTranstion** function as the last line of **renderLegend**

```
function filterByRegion(data, region) {
  if (activeLegend == region) {
    activeLegend = "";
    legendTransition(region);
    return data;
    } else {
      let filteredData = data.filter(d => d["Region"] == region);
      activeLegend = region;
      legendTransition(region);
      return filteredData;
    }
}
```

#### Creating the legendTransition Function

The legendTransition function will first grab all of the legendItems and then initiate a transition.  If activeLegend is set to an empty string or the current items is the active legend then it will return an opacity of 1, else it will return an opacity of 0.3. 

```
function legendTransition(region) {
  let legends = d3.selectAll(".legendItem");
  legends
    .transition()
    .duration(500)
    .attr("opacity", d => {
      if (activeLegend == "" || d == activeLegend) {
        return 1;
      } else {
        return 0.3;
      }
    });
}
```

#### CodePen Solution Code

Here is the full solution code for the project thus far:

[D3 - Scatterplot - Legend (Interactive) - Solution) - Solution ](https://codepen.io/jkeohan/pen/KQRKGj?editors=0010)

### Additional Legend Resources

- [d3-legend](http://d3-legend.susielu.com/#color-ordinal)
- [d3noob.org](http://www.d3noob.org/2014/02/styles-in-d3js.html)



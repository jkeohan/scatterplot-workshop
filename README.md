# Building A Scatterplot Using D3

## Importing Data & Rendering Circles

## Adding X & Y Scales

## Adding X & Y Axes

## Adding Circle & Line Transitions

## Adding A Tooltip

Fork and clone the starter code: [D3 - Scatterplot - ToolTip - Starter](https://codepen.io/jkeohan/pen/wyWjXq)

Adding a tooltip will require that we do the following:

- Implement the tooltip design using HTML\CSS
- Create a **displayToolTip** function to render the tooltip
- Extend **mouserOver** function to call the displayToolTip function
- Extend the **mouseOut** function to remove the tooltip

### Implement the Tooltip design 

The design were looking to implement is the following:

![d3-tooltip](http://res.cloudinary.com/jkeohan/image/upload/v1518555150/Screen_Shot_2018-02-13_at_3.45.45_PM_yada2o.png)

Since recreating the above design would add additional time to the tutorial, the following [CodePen](https://codepen.io/pen/?editors=0100) has been provided with all the required HTML\CSS. It will however require some editing to be dynamic and show the info specific to that element. 

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

<img src="http://res.cloudinary.com/jkeohan/image/upload/v1518568508/Screen_Shot_2018-02-13_at_7.34.24_PM_gzqmjd.png" style="width:200px"/>

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
const tooltip = d3.select(".regionalstats").append("div").attr("class","d3tooltip")

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

 The d const variable created earlier contains the data needed we can grab those values easily   Here is the code that does just that and ends with a nice transition for opacity:
 
 ```
 tooltip   
		.style("opacity",0)
		.style('left', (cx + 160) + 'px')
		.style("top", (cy + 30) + "px") 
	    .style("border" , "3px solid " + d.color )
	    .html(`
	      <span class="regionName">${d.Location}</span>
	      <hr class="d3tooltiphr" style="border: 2px solid ${d.color}"> 
	      <span class="key">2002:</span> <span class="value">${d["2002"]} %</span><br/>
	      <span class="key">2012:</span> <span class="value">${d["2012"]}%</span><br/>
	    `)
	  .transition().duration(1000).style("opacity",1)
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


## Adding A Legend 



## Extend Legend To Filter Dataset


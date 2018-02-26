## Adding A Tooltip - (EST: 60 min)

The starter code for this section has been provided so please fork the following codepen: 

**Starter Code:** [D3 - Scatterplot - ToolTip - Starter](https://codepen.io/jkeohan/pen/wyWjXq)

Tooltips help elicit additional information about a specific data point. In the case of the scatterplot it will be used to show the percent output of the country for years 2002 & 2012.  It can, however, be extended to show all the years and their corresponding values as a table or even a mini bar chart.  When approaching the design one should always consider what information would be most relevant and/or useful to the user as they investigate the data. 

Adding a tooltip will require that we do the following:

- Implement the tooltip design using HTML\CSS
- Create a **displayToolTip** function to render the tooltip
- Extend **mouserOver** function to call the displayToolTip function
- Extend the **mouseOut** function to remove the tooltip

### Implement the Tooltip design 

The design we're looking to implement is the following:

<img src="http://res.cloudinary.com/jkeohan/image/upload/v1518555150/Screen_Shot_2018-02-13_at_3.45.45_PM_yada2o.png" width="150" />

Since recreating the above design would add additional time to the tutorial, the following [CodePen](https://codepen.io/jkeohan/pen/wyqpPj) has been provided with all the required HTML\CSS. It will, however, require some editing to be dynamic and show the info specific to that element. 

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

With the basic template in place we can now create the function responsible for generating the tooltip. We will create the basic function for now, add a console.log to confirm it's receiving the circle element, and then slowly expand on it to include the additional elements as needed. 
 
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
- its current cx & cy coordinates

The data can be enumerated using the elements .datum() method and the coordinates using .attr().

```
const d = selection.datum()
const cx = +selection.attr("cx")
const cy = +selection.attr("cy")
```
 
The last thing that needs to be done in order to render the tooltip are the following:

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

Of course a smooth transition is what we're looking for, so one more line is all we need to change its opacity.

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

- [d3-tip](https://github.com/Caged/d3-tip) - a reusable tooltip component
- [blockbuilder.org - filtered by d3-tip](http://blockbuilder.org/search#d3modules%3Dd3-tip%2Cd3-legend)
- [D3.js Step by Step: Adding Tooltips](http://zeroviscosity.com/d3-js-step-by-step/step-5-adding-tooltips)

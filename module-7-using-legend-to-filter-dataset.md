## Filtering Data Based On Legend Choice - (EST: 60 min)

#### CodePen Solution 

The starter code for this section has been provided so please fork the following codepen: 

[D3 - Scatterplot - Legend (Interactive) - Starter](https://codepen.io/jkeohan/pen/vddbmG)

### Intro 

Although animations almost always enhance the end user experience they should be applied based on end user expectations.  Users that have already worked with interactive data visualizations expect that some functionality be provided that allows them to filter the data set. The most intuitive way to do this in our current scatterplot would be to click on the legend values so that is where we will be implemented. 

The user iteraction with the legend that we were looking to implement in this scenario will involve the user clicking on one legend item which rerender of the scatterplot displaying only those countries from the choosen region.   The user can click additional regions which will once again filter the data set and cause a rerender.  One thing to consider is how to get the user back to viewing all the regional data.  In order to make that happen the user will only need to click the same region a second time.  Hopefully this will be an intuitive choice but if not we can always provide a **reset** button or link that would provide a clear and direct path to reseting the scatterplot. 

###  Steps To Filter Data

Here is a breakdown of the steps we will perform to implement the filtering and redrawing of the scatterplot based on the users selected region:


- Create a new variable called **activeLegend** to keep track of which legend is currently active.
- Add **.onclick** event to legendItems that will call a new function called **filterByRegion** and store the results in the **region** variable
- Create the **filterByRegion** function that will return a new array of cities filtered by region
- Create a new function called **legendTranstion** that will transition the opacity of the legend items to indicate which one is active 
- Call the **render** function passing it the newly filtered dataset.
- Update the **render** function to transition the circles from their current to their new position. 


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

Clicking on any legend items at this point will display an error in the console being that the filterByRegion function has not yet been created so let's add it as the last line of **renderLegend** function and add some pseudocode.

```
function filterByRegion(data, region) {
 // if activelenend is already set to the region reset it to an empty string
 // and return the whole dataset
 // else set activelegend to region and filter\return new dataset
}
```

With the steps thought out let's translate that into code:


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

#### Updating The Render Function 

As the render function is presently configured all the circles are redrawn with every update.  A much better idea is to transition the ones that are filtered from their current to their new position once the axes are redrawn. 

To do this first requires one small change to how we bound the data initially. D3's default data binding is done based on position and not on the actual values inside the array, that is unless we use a callback function to tell it to do so.

```
let group = svg.selectAll("circle").data(data, d => d["Location"]);
```


Now we need to retrieve their current cx\cy values and then transition them to their new positions.  To do this we will rereference the group variable again which segments these items for updating and use **d3.select(this)** to reference the current items cx\cy values. Once this is done we can then transition them to their new position. 

```
  group
    .attr("cx", function(d, i) {
      return d3.select(this).attr("cx");
    })
    .attr("cy", function(d, i) {
      return d3.select(this).attr("cy");
    })
    .transition()
    .duration(500)
    .attr("cx", (d, i) => xScale(d["2002"]))
    .attr("cy", (d, i) => yScale(d["2012"]))
    .attr("opacity", 1);
```

#### CodePen Solution Code

Here is the full solution code for the project thus far:

[D3 - Scatterplot - Legend (Interactive) - Solution](https://codepen.io/jkeohan/pen/KQRKGj?editors=0010)

### Extending Filtering To Mouseover\Mouseout Events

#### Starter CodePen 

Here is the starter code:

[D3 - Scatterplot - Legend (Interactive) - Legend Opacity - Starter ](https://codepen.io/jkeohan/pen/VQGBvR?editors=0010)

One addition we can add to the legend to enhance the user experience is to allow them to temporarily lower the opacity of the other data points which would highlight the ones chosen. 

Here are the steps needed to implement this design:

- add **mouseover** and **mouseout** events to the legend items that call thier supporting functions.
- create the **legendMouseOver** and **legendMouseOut** functions

#### Adding The Mouseover and Mouseout Events

Adding the on click events is done exactly as before but this time we define the event type as mouseover and mouseout. 

```
  let legendItems = legend
   // ...additional code
   .on("click", function(d) {
      let filter = filterByRegion(d);
      render(filter);
    })
    .on('mouseover', legendMouseOver)
    .on('mouseout', legendMouseOut)
```

#### Creating The legendMouseOver Function

Now it's time to create the legendMouseOver function which will do the following:

- check to see if scatterplot has already been filtered and if so end the function immediately
- select all the non-hovered legend items and change thier opacity to .3 
- select all the circles and change thier opacity to .1 except for the ones related to that region 

Let's create the function and get things started by having it first evaluate if it should continue executing or stop. 

```
function legendMouseOver(region){
  if(activeLegend) return
}
```
  
If that's not the case then it needs to reset the opacities of both the legends and circles.  In order to do that we will select and store them in their corresponding variables.

```
function legendMouseOver(region){
  if(activeLegend) return
  let legends = d3.selectAll(".legendItem");
  let circles = d3.selectAll('circle')
}
```

The only remaining step for the function to perform is to change the opacities. 

```
legends.transition().duration(500).attr('opacity',(d) => {
    return d === region ? 1 : 0.3
})
circles.transition().duration(500)
  .attr('opacity',function(d) {
    return d["Region"] == region ? 1 : 0.1
}) 
```

Here is the entire legendMouseOut function:

<details>
<summary>legendMouseOut</summary>

```
function legendMouseOver(region){
  if(activeLegend) return
  let legends = d3.selectAll(".legendItem");
  let circles = d3.selectAll('circle');
  legends.transition().duration(500).attr('opacity',(d) => {
      return d === region ? 1 : 0.3;
  });
  circles.transition().duration(500)
    .attr('opacity',function(d) {
      return d["Region"] == region ? 1 : 0.1;
  });  
}
```
</details>

#### Creating The legendMouseOut Function

This code for this function is almost identical to legendMouseOver however it resets all the opacities back to 1. 

```
  function legendMouseOut(region) {
    if (filtered) return;
    let legends = d3.selectAll(".legendItem");
    let circles = d3.selectAll("circle");
    legends.transition().duration(500).attr("opacity", 1);
    circles.transition().duration(500).attr("opacity", 1);
  }
```

#### Solution CodePen 

Here is the full solution code for the project thus far:

[D3 - Scatterplot - Legend (Interactive II) - Solution](https://codepen.io/jkeohan/pen/XEreNd)

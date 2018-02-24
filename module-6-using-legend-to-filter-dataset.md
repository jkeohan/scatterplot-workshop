## Filtering Data Based On Legend Choice 

#### CodePen Solution Code

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

[D3 - Scatterplot - Legend (Interactive) - Solution](https://codepen.io/jkeohan/pen/KQRKGj?editors=0010)
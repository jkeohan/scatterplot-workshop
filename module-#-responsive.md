#### Code For Responsiveness

The last bit of code needed is to handle window resize.  This requires that we do the following:

- add an event listener to the window
- create a function that is called when the event is fired


D3 uses the same method to select the window as it does any other dom element and then listens for when **resize** event is fired and then calls a function.

```
d3.select(window).on('resize', resize)
```

The **resize** function needs to determine the current width\height of it's parent and then update the svg and g elements with their new sizes. The **node** variable that we defined globaly plays the part of event listener as well.  It's properties are updated dynamically as the scatterplot dimensions ajdust so we will update the **svgDimensions** variable along with the  **svgWidth**, **svgHeight**, **height** and **width** variables as well.  

```
function resize() {
 svgDimensions = [node.clientWidth, node.clientHeight]
 svgWidth = svgDimensions[0];
 svgHeight = svgDimensions[1];
 width = svgDimensions[0] - (m.left + m.right);
 height = svgDimensions[1]  - (m.top + m.bottom);
 svg.attrs({ width: svgWidth, height: svgHeight })
}
```

The width variable is also used to place the legend so **gLegend** needs to be updated and then finally we call the **render** function to redraw everything.

```
function resize() {
 svgDimensions = [node.clientWidth, node.clientHeight]
 svgWidth = svgDimensions[0];
 svgHeight = svgDimensions[1];
 width = svgDimensions[0] - (m.left + m.right);
 height = svgDimensions[1]  - (m.top + m.bottom);
 svg.attrs({ width: svgWidth, height: svgHeight })
 // LEGEND AND RENDER
 gLegend.attr("transform", "translate(" + width + "," + m.top + ")");
 render(data)
}
```

## Importing And Rendering Data 

The starter code for this section has been provided so please fork the following codepen: 

**Starter Code:** [D3 - Scatterplot - Rendering Data - Starter](https://codepen.io/jkeohan/pen/RQPEox?editors=0010)

Although D3's main focus is rendering charts and graphs, one of it's additional strengths is that it has many [convenience methods() that allow D3 to import data remotely. These methods makes standard api calls as do axios and fetch. These methods range from .json to .csv, .tsv and several more. 

Let's make use of d3.csv to import data from the url provided and see what it returns. 

```
d3.csv(url, function(data) {
  console.log(data)
});
```



#### Additional Resources

- [d3-shape](https://github.com/d3/d3-shape)
- [d3-queue-mental-model](https://macwright.org/2016/12/09/a-d3-queue-mental-model.html)

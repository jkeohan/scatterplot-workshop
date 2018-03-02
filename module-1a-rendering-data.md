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
- Responsive design

#### Getting Started

The objective of this module isn't to implement all the functionality defined above but to both structure our code in such a way that takes into consideration our final design. We will focus on the following specifically:

-  render just the circles
-  make the design responsive

We are going to be writing quite of bit of code in this module and it will fall into several buckets:

- code that runs only once on page load
- code that runs on every time the data set changes
- code that runs as per our responsive design

Let's start with the code that only runs once on page load. 

#### Code On Page Load

Something that you will see quite often in the code used to build out a D3 desing is defining values that we can use to position our g elements.  So let's create a new const variable and assign some values. 

```
const m = { left: 20, right: 150, top: 20, bottom: 20 };
```

The left and bottom values are so that we make space for the x\y axis text. The top is to create some distance from the parent div but the largest values of 150 is meant to provide the space needed to add the legend to the right of the scatterplot. 

The svg that will contain our visualization will be appended to a div with a class of ".scatterplot" so let's use d3 to grab that element from the DOM using **d3.select()**.

```
const scatterplot = d3.select(".scatterplot");
```

As part of the responsive design we want to create append an svg that uses the width and height of it's parent as it's values as well.  D3 provides the **.node()** method to obtain the properties of a DOM element so let's add that as well. 

```
let node = scatterplot.node()
```

#### Solution Code

Here is the full solution code for the project thus far:

[D3 - Scatterplot - Rendering Data - Solution ](https://codepen.io/jkeohan/pen/dWbmOM)
# Data Visualisations with d3.js

This Dojo is based on the article [The Largest Ever Analysis of Film Dialogue by Gender](https://pudding.cool/2017/03/film-dialogue/)

The file `solution.html` is a template where you will be working on your solution. The d3 and jQuery libraries have already been added so you should be ready to go straight away :smiley:.

The visualisations will be added to the grey div with the id `#my-container`

I have added links to the solutions at each step in case you get stuck and want to skip to the next step.

### Part 1: Draw a square

The first thing we want to do is **append** a div our container and **style** add a style to it.
*(style hint: background-color, border, left and top css attributes)*

[Useful Docs - Append](https://github.com/d3/d3-selection/blob/master/README.md#selection_append)

![Part 1](./examples/part-1.png)
[Solution](https://bl.ocks.org/hanahanderson/c77e430d40d5cccdb484d6eb1771953c)

___
### Part 2: Draw squares from data

We now want to pass **data** through to our visualisation (an array of ints from 1-100) and display them as divs in a grid.

*(hint: you can calculate x/y coordinates aka the left and top css attributes dynamically based on the index of the data)*

```javascript
  \\Static style
  ...
    .style("left", "10px");

  \\Dynamic style
  ...
    .style("left", (data, index) => {
      return /* SOME CALCULATION WITH index */ + "px"
    });

```

[Useful Docs - Enter and Exit](https://d3js.org/#enter-exit)

![Part 2](./examples/part-2.png)
[Solution](https://bl.ocks.org/hanahanderson/37bc7965987016c7e8bdb09b36455d5c)

___
### Part 3: Load data from remote source

We want to be able to load data from a remote source and print our results in the console.

We will be using the film data that was used for original article, which is hosted [here](https://raw.githubusercontent.com/matthewfdaniels/scripts/graphs/meta_data7.csv).

[Useful Docs - CSV](https://github.com/d3/d3-request/blob/master/README.md#csv)


![Part 3](./examples/part-3.png)
[Solution](https://bl.ocks.org/hanahanderson/13a3613383a8369ed3765d6202e715ac)

___
### Part 4: Draw divs from loaded data

We want to now get the data loaded from part 3 and pass it to our visualisation like we did in part 2.

These divs will be styled as rectangles:
  1. The **width** is 1px and **height** is 10px
  2. There is no space between divs so it looks like one continuous bar
  3. The **background-color** is calculated dynamically based on the percent of lines spoken by a woman per film (pink is more than 50% spoken by women blue is less than 50%).

Read [here](https://github.com/matthewfdaniels/scripts) to see how the parse the data.

[Useful Docs - Continuous Domain (to calculate the colour based on a value)](https://github.com/d3/d3-scale/blob/master/README.md#continuous_domain)

![Part 4](./examples/part-4.png)
[Solution](https://bl.ocks.org/hanahanderson/09f97bc936db2e370c6c18776c0c4b0a)

___
### Part 5: Trigger an animation

Now we have our visualisation in a horizontal bar, we want to represent the films as a bar chart to quantify how many films have over 50% lines spoken by women in film.

In the original article the animation was triggered on page scroll, but we are just going to trigger it when the button `#trigger-animation` is clicked.

The transition of styles for the div will be toggling:
  1. position (top and left css style attributes)
  2. height and width
  3. border-radius (to display a circle rather than a rectangle)

We can also stagger the transition of each of the divs to make the animation smooth based on the index of the data with the **delay** function.

(ps. if you are completely lost, here is one way of grouping the films into the percent of female lines spoken, where the attribute `yOrder` can be used to calculate the y position aka the **top** css style attribute).

```javascript
  //LOAD DATA  (from Part 3)
  d3.csv("https://raw.githubusercontent.com/matthewfdaniels/scripts/graphs/meta_data7.csv", (films) => {
     var femalePercentOrder = {};

     films.forEach((film) => {

       //PARSE LINE DATA PER FILM (from Part 4)
       var lineData = []
       var lineInfo = film.lines_data.match(/.{1,2}/g);
       film.female_words = 0;
       film.total_words = 0

       for (line in lineInfo){
         var minuteTotal = +lineInfo[line].slice(0,1) +
             							 +lineInfo[line].slice(1,2);
         var row = [minuteTotal,14-minuteTotal];
         film.female_words += 14-minuteTotal;
         film.total_words += d3.sum(row)
         lineData.push(row);
       }
       film.lineData = lineData

       //CALCULATE GENDER BREAKDOWN (use this for background-colour, also from Part 4)
       film.female_percent
          = Math.round(100 * film.female_words/ film.total_words)/100

       //GROUP FILM PER PERCENT BUCKET
       var femalePercentBucket = film.female_percent;
       if(femalePercentBucket in femalePercentOrder){
         femalePercentOrder[femalePercentBucket] =
          femalePercentOrder[femalePercentBucket] + 1
       } else {
         femalePercentOrder[femalePercentBucket] = 0;
       }

       film.yOrder = femalePercentOrder[femalePercentBucket];
     });

     films.sort((a, b) => { return a.female_percent - b.female_percent})

    /*  
      YOUR CODE GOES HERE
    */

   });
 });

```

[Useful Docs - Transitions](https://d3js.org/#transitions)

[![Part 5](./examples/part-5.png)](./examples/part-5.mov)
[Solution](https://bl.ocks.org/hanahanderson/b912694acb71b6aed3b1396fbfc0dc7f)

___
### Part 6: Interacting with the Visualisation

To add another level of interactivity to our visualisation, we want to be able to see which film each div represents. We can do this by:
  1. Adding a _new_ div to our container to act as a key (like in Part 1)
  2. Adding an event to our film divs which is triggered on **mouseover** to update the contents of the key with the film's name and gender breakdown

*(hint: the contents of the key can just be written as a html table. easy peasy)*

[Useful Docs - Mouse Events](http://rajapradhan.com/blogs/d3-js-v4-essentials/interactivity/)

[Useful Docs - Append HTML](https://github.com/d3/d3-selection/blob/master/README.md#selection_html)


[![Part 6](./examples/part-6.png)](./examples/part-6.mov)
[Solution](https://bl.ocks.org/hanahanderson/08b0e3ec69344584c58a166556cbfa2c)

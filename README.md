# Data Visualisations with d3.js

[D3.js](https://d3js.org/) is a really cool Javascript library which "allows you to bind arbitrary data to a Document Object Model (DOM), and then apply data-driven transformations to the document."

This Dojo is based on the article [The Largest Ever Analysis of Film Dialogue by Gender](https://pudding.cool/2017/03/film-dialogue/)


## Getting Started

Ok so we are gonna play it a bit fast and loose, there will be no tests, it's pretty much all a hack. Also, Tanya has been very kind as to go through the dojo and give a difficulty rating at each step. I hope you enjoy!

The file `template-solution.html` is a template where you will be working on your solution. Copy and paste it into [blockbuilder](http://blockbuilder.org/), where you can get realtime updates of your visualisation as you change your code. The d3 and jQuery libraries have already been added so you should be ready to go straight away.

I have added links to the solutions at each step in case you get stuck and want to skip to the next step.

The visualisations will be added to the grey div with the id `#my-container`


### Part 1: Draw a square
_Tanya's difficulty rating: :smiley: (easy peasy lemon squeezy)_

The first thing we want to do is to use the **append** function to add a div to our container and use the **style** function to add css attributes match to the picture below.

*(style hint: background-color, border, left, top, position=absolute)*

[Useful Docs - Append](https://github.com/d3/d3-selection/blob/master/README.md#selection_append)

[Useful Docs - Basic CSS Styles](https://www.w3schools.com/w3css/default.asp)

![Part 1](./examples/part-1.png)
[Solution](https://bl.ocks.org/hanahanderson/c77e430d40d5cccdb484d6eb1771953c)

___
### Part 2: Draw squares from data
_Tanya's difficulty rating: :grimacing: (easy peasy-ish)_

We now want to pass data through to our visualisation (an array of ints from 1-100) and display them as divs in a grid.

We use the **data** function, which takes an array of objects and binds a div with a corresponding data and index value.

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

*(hint: you can calculate x/y coordinates aka the left and top css attributes dynamically based on the index of the data)*

NB. Don't worry this is **not** the Game of Life. Think `Math.floor` and `%` (mod) functions. You're all very smart :wink: #QuickMaths

[Useful Docs - Data, Enter and Exit](https://d3js.org/#enter-exit)

![Part 2](./examples/part-2.png)
[Solution](https://bl.ocks.org/hanahanderson/37bc7965987016c7e8bdb09b36455d5c)

___
### Part 3: Load data from remote source
_Tanya's difficulty rating: :smiley: (easy peasy lemon squeesy)_

We want to be able to load data from a remote source and print our results in the console.

We will be using the film data that was used for original article, which is hosted [here](https://raw.githubusercontent.com/matthewfdaniels/scripts/graphs/meta_data7.csv).

[Useful Docs - CSV](http://learnjsdata.com/read_data.html)


![Part 3](./examples/part-3.png)
[Solution](https://bl.ocks.org/hanahanderson/13a3613383a8369ed3765d6202e715ac)

___
### Part 4: Draw divs from loaded data
_Tanya's difficulty rating: :confounded: (difficult difficult lemon difficult)_

We want to now get the data loaded from part 3 and pass it to our visualisation like we did in part 2.

Read [here](https://github.com/matthewfdaniels/scripts) for an explanation of how to interpret and parse the data.

(If you are completely lost, just copy the following. We are iterating through each of the film data and calculating the percent of lines spoken by female characters, which will be added to each film as the attribute `female_percent`)

```javascript
  //LOAD DATA  (from Part 3)
  d3.csv("https://raw.githubusercontent.com/matthewfdaniels/scripts/graphs/meta_data7.csv", (films) => {

     var femalePercentOrder = {};

     films.forEach((film) => {

       //Parse Line data per film
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

       film.female_percent
          = Math.round(100 * film.female_words/ film.total_words)/100  
    });

    films.sort((a, b) => { return a.female_percent - b.female_percent})

    /*
        PUT YOUR D3 GRID CODE HERE
        Don't forget to pass through the val "films" to the data function @Tanya
    */
  });
```

These divs will be styled as rectangles:
  1. The **width** is 0.4px and **height** is 40px
  2. There is no border
  3. There is no space between divs so it looks like one continuous bar
  4. The **background-color** is calculated dynamically based on the percent of lines spoken by a woman per film (pink is more than 50% spoken by women blue is less than 50%).


[Useful Docs - Continuous Domain (to calculate the colour based on a value)](https://github.com/d3/d3-3.x-api-reference/blob/master/Quantitative-Scales.md#linear_domain)

![Part 4](./examples/part-4.png)
[Solution](https://bl.ocks.org/hanahanderson/09f97bc936db2e370c6c18776c0c4b0a)

___
### Part 5: Trigger an animation

Now we have our visualisation in a horizontal bar, we want to represent the films as a bar chart to quantify how many films have over 50% lines spoken by women in film.

In the original article the animation was triggered on page scroll, but we are just going to trigger it when the button `#trigger-animation` is clicked.

The transition of styles for the div will be toggling:
  1. **position** (top and left css style attributes)
  2. **height** and **width**
  3. **border-radius** (to display a circle rather than a rectangle)

(ps. if you are completely lost, here is one way of grouping the films into the percent of female lines spoken, where the attribute `yOrder` can be used to calculate the y position aka the **top** css style attribute. You can just copy and paste it into the chunk of code just after we assign the female_percent attribute).

```javascript

     //GROUP FILM PER PERCENT BUCKET
     var femalePercentBucket = film.female_percent;
     if(femalePercentBucket in femalePercentOrder){
       femalePercentOrder[femalePercentBucket] =
        femalePercentOrder[femalePercentBucket] + 1
     } else {
       femalePercentOrder[femalePercentBucket] = 0;
     }

     film.yOrder = femalePercentOrder[femalePercentBucket];

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

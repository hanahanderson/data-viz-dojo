# Data Visualisations with d3.js

This Dojo is based on the article [The Largest Ever Analysis of Film Dialogue by Gender](https://pudding.cool/2017/03/film-dialogue/)

The file `solution.html` is the workspace where you will be working on your solution. The d3 and jQuery libraries have already been added so you should be ready to go straight away :smiley:.

The visualisations will be appended to the grey div with the id `#my-container`

### Part 1: Draw a square
[Useful Docs](https://d3js.org/#selections)

The first thing we want to do is append a div our container.
*(style hint: background color, border, position)*

![Part 1](./examples/part-1.png)
[Solution](https://bl.ocks.org/hanahanderson/c77e430d40d5cccdb484d6eb1771953c)

___
### Part 2: Draw squares from data
[Useful Docs](https://d3js.org/#enter-exit)

We now want to pass data through to our visualisation (an array of ints from 1-100) and display them as divs in a grid.

*(hint: you can calculate x/y coordinates based on the index of the data)*
![Part 2](./examples/part-2.png)
[Solution](https://bl.ocks.org/hanahanderson/37bc7965987016c7e8bdb09b36455d5c)

___
### Part 3: Load data from remote source
[Useful Docs](https://github.com/d3/d3-request/blob/master/README.md#csv)

We want to be able to load data from a remote source and print our results in the console.

We will be using the film data that was used for original article, which is hosted [here](https://raw.githubusercontent.com/matthewfdaniels/scripts/graphs/meta_data7.csv).

![Part 3](./examples/part-3.png)
[Solution](https://bl.ocks.org/hanahanderson/13a3613383a8369ed3765d6202e715ac)

___
### Part 4: Draw squares from loaded data

[Useful Docs](https://github.com/d3/d3-scale/blob/master/README.md#continuous_domain)

We want to now get the data loaded from part 3 and pass it to our visualisation like we did in part 2. This time however the style is conditional and based on the data passed to each div, i.e. the colour of the div now represents the percent of lines spoken by a woman per film, where pink is more than 50% spoken by women blue is less than 50% (very cliché, I know).

Read [here](https://github.com/matthewfdaniels/scripts) to see how the parse the data.

![Part 4](./examples/part-4.png)
[Solution](https://bl.ocks.org/hanahanderson/09f97bc936db2e370c6c18776c0c4b0a)

___
### Part 5: Trigger an animation
[Useful Docs](https://d3js.org/#transitions)

Now we have our visualisation in a horizontal bar, we want to represent the films on a linear scale to quantify how many films have over 50% lines spoken by women in film.

(ps. if you are completely lost, here is one way of grouping the films into the percent of female lines spoken, where the attribute `yOrder` can be used to quantify the y position).

```
  d3.csv("https://raw.githubusercontent.com/matthewfdaniels/scripts/graphs/meta_data7.csv", (films) => {
       var femalePercentOrder = {};

       films.forEach((film) => {

         var lineData = []
         var lineInfo = film.lines_data.match(/.{1,2}/g);
         var female_words = 0;
         var total_words = 0

         for (line in lineInfo){
           var minuteTotal = +lineInfo[line].slice(0,1) +
               							 +lineInfo[line].slice(1,2);
           var row = [minuteTotal,14-minuteTotal];
           female_words += 14-minuteTotal;
           total_words += d3.sum(row)
           lineData.push(row);
         }
         film.lineData = lineData

         film.female_percent = Math.round(100*female_words/total_words)/100
         film.female_words = female_words;
         film.total_words = total_words;

         var femalePercentBucket = film.female_percent;
         if(femalePercentBucket in femalePercentOrder){
           femalePercentOrder[femalePercentBucket] =
            femalePercentOrder[femalePercentBucket] + 1
         }
         else{
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

[![Part 5](./examples/part-5.png)](./examples/part-5.mov)
[Solution](https://bl.ocks.org/hanahanderson/b912694acb71b6aed3b1396fbfc0dc7f)

___
### Part 6: Interacting with the Visualisation

[![Part 6](./examples/part-6.png)](./examples/part-6.mov)
[Solution](https://bl.ocks.org/hanahanderson/08b0e3ec69344584c58a166556cbfa2c)

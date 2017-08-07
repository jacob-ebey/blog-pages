# Building a mobile-first Grid with SCSS 

Today we are going to explore how we can create a responsive grid framework with scss in ~20 lines of code that can be minified to 2kb.


## The HTML

Let's start with what we want our HTML to look like. I believe in being as minimal as possible while remaining descriptive:

```html
<div class="grid">
  <div class="col s12 m6 l8">1</div>
  <div class="col s12 m6 l4">2</div>
</div>
```

The above markup is am example of a grid with two columns that both show 100% (across all 12 columns) on small devices, 50/50 columns on medium devices and 8/4 on large devices. We can actually obmit the **s12** as columns will always default to 100%.


## The SCSS

As defined above, we are using a 12 column grid system so the first thing we are going to do is create some variables to store our grid configuration:
```css
// The number of columns the grid can have
$grid-items: 12;
// The responsive breakpoints for columns
$breakpoints: (s: 320px, m: 641px, l: 1025px);
```

Now, if you've been stuck in CSS and are new to the SCSS game you may be saying: 'Wait... Variables? What could that be for?'. Well, let me introduce you to dynamic class creation :D

SCSS give us the ability to loop from one number to another:

```css
 @for $i from 1 through $grid-items {
   .col#{$i} {
     flex: 0 0 percentage($i / $grid-items);
   }
 }
```

This will generate the css:
```css
.col1 {
  flex: 0 0 8.33333%;
}
.col2 {
  flex: 0 0 16.66667%;
}
... etc ...
```

That's good and all, but it's not responsive :( Let's create one more variable below **$grid-items** that will define our responsive breakpoints:

```css
// Loop through each breakpoint
@each $key,
$value in $breakpoints {
  // Create the responsive media query
  @media (min-width: $value) {
    // Loop number of items in grid
    @for $i from 1 through $grid-items {
      // Create a responsive class for each breakpoint and column size
      .col.#{$key}#{$i} {
        flex: 0 0 percentage($i / $grid-items);
      }
    }
  }
}
```

Now we can introduce the base grid and col classes to finish up our grid implementation:

```css
// The number of columns the grid can have
$grid-items: 12;
// The responsive breakpoints for columns
$breakpoints: (s: 320px, m: 641px, l: 1025px);

// The grid class
.grid {
  display: flex;
  flex-flow: row wrap;
  
  // Base column
  .col {
    flex: 0 1 100%;
  }
  
  // Responsive column classes
  @each $key,
  $value in $breakpoints {
    @media (min-width: $value) {
      @for $i from 1 through $grid-items {
        .col.#{$key}#{$i} {
          flex: 0 0 percentage($i / $grid-items);
        }
      }
    }
  }
}
```

### Bonus
One last thing I like to do is to define classes to determine at what screen size to show an item:

```css
@each $key,
$value in $breakpoints {
  .show-#{$key} {
    display: none;
    @media (min-width: $value) {
      display: unset;
    }
  }
}
```

These classes can be used like so to go from a single column on mobile to two columns on anything large:
```css
<div class="grid"
  <div class="col m6">1</div>
  <div class="col m6 show-m">2</div>
</div>
```

I hope this can help you on your responsive web adventures.

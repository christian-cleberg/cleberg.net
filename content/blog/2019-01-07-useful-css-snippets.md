+++
date = 2019-01-07
title = "Useful CSS Snippets"
description = "Learn more about CSS variables, box shadows, and flexbox."
+++

## Introduction to CSS

CSS, the language used to markup HTML code and make it pretty, is one of the most effective ways to increase the
attractiveness of a website. It can also lead to increased customer retention and satisfaction. In fact, there are whole
career fields are dedicated to the improvement of user experiences, known as UI design and UX design.

Web developers are used to the common CSS code snippets, such as changing sizes, fonts, colors, etc. but are not as well
versed in less-used methods such as flex boxes (although I hope the flex properties are used everywhere before too long)
. This article will provide some insight into the less-used and unique CSS properties.

## CSS Variables

The first topic today is CSS variables. Variables are not often used by smaller developers. CSS variables allow you to
give your website a well-defined structure, where you can easily reuse CSS properties throughout the project.

You can use variables to define things, such as color palettes. Then, you can use these colors for backgrounds
anywhere else in the HTML. This could be extended, where extra variables could be defined for "primary-text", "
quoted-text", etc. Variables can also be used to define spacing (e.g. 32px or 2rem), which can then be applied to
margins, padding, font sizes, and more.

## CSS Box Shadows

Box shadows were once my mortal enemy. No matter how hard I tried, I just couldn't get them to work how I wanted.
Because of this, my favorite discovery has been CSSMatic's [box shadow generator](https://www.cssmatic.com/box-shadow).
It provides an excellent tool to generate box shadows using their simple sliders. Surprisingly, this is the reason I
learned how box shadows work! You can use the sliders and watch how the CSS code changes in the image that is displayed.
Through this, you should understand that the basic structure for box shadows is:

```css
box-shadow: inset horizontal vertical blur spread color;
```

Now, let's look at some basic examples! You can copy and paste the following code into a site like CodePen or your own
HTML files. Feel free to play around with the code, experiment, and learn.

**Box Shadow \#1**
```html
<div class="shadow-examples">
  <div class="box effect1">
    <h3>Effect 1</h3>
  </div>
</div>
```
```css
.box h3 { text-align: center; position: relative; top: 80px }
.box { width: 70%; height: 200px; background: #fff; margin: 40px auto }
.effect1 { box-shadow: 0 10px 6px -6px #777 }
```

**Box Shadow \#2**
```html
<div class="shadow-examples">
  <div class="box effect2">
    <h3>Effect 2</h3>
  </div>
</div>
```
```css
.box h3 { text-align:center; position:relative; top:80px }
.box { width:70%; height:200px; background:#FFF; margin:40px auto }
.effect2 { box-shadow: 10px 10px 5px -5px rgba(0,0,0,0.75) }
```

## CSS Flexbox

Now, let's move on to the best part of this article: flexbox. The flexbox is by far my favorite new toy. I originally
stumbled across this solution after looking for more efficient ways of centering content horizontally AND vertically. I
had used a few hack-ish methods before, but flexbox throws those out the window. The best part of it all is that flexbox
is *dead simple*.

Flexbox pertains to the parent div of any element. You want the parent to be the flexbox in which items are arranged
using the flex methods. It's easier to see this in action that explained, so let's see an example.

**Flexbox**
```html
<div class="flex-examples">
  <div class="sm-box">
    <h3>1</h3>
  </div>
  <div class="sm-box">
    <h3>2</h3>
  </div>
</div>
```
```css
.flex-examples { display: flex; flex-wrap: wrap; justify-content: flex-start; align-items: center; padding: 10px; background-color: #f2f2f2 }
.sm-box { display: flex; justify-content: center; align-items: center; width: 20%; height: 100px; background: #fff; margin: 40px 10px }
```

You may notice that we no longer need to use the `top` property for the `h3` elements in our code. This is because we
set the display box to be a flex container for the small boxes, AND we made the small boxes flex containers for their
elements (the h3 tags). Flex boxes can be nested like this to center content that is inside centered content.

For the example above, we designated the `justify-content` property to be `flex-start` so that the boxes stack from the
left side of the screen. This property can be changed to `center` to make the boxes appear in the center of the screen.

For an interactive example, [check out this CodePen](https://codepen.io/LandonSchropp/pen/KpzzGo)
from [LandonSchropp](https://codepen.io/LandonSchropp/). Resize the window with dice to see how they collapse and
re-align.

## Even More CSS

For more inspiration, you can [visit CodePen](https://www.codepen.io), [Dribbble](https://dribbble.com),
or [UI Movement](https://uimovement.com) to browse the collections of many amazing web designers.



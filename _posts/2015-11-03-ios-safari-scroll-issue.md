---
layout: post
title:  "Hanging Scroll Issue on iOS Safari"
date:   2015-11-03 06:24:25
categories: ios safari css
img: safari-icon.jpg
image: safari.jpg
---

I came across an issue on the mobile view (iOS Safari) of a website I was working on. A particular page with some form fileds kept hanging when scrolled. Scrolling of the page somehow freezes randomly at some points. The same page works fine on other browsers (both desktop and mobile).

![Page hangs while scrolling](http://f.cl.ly/items/150c2S060d0W402L0g3J/Screen%20Recording%202015-09-13%20at%2010.27%20PM.gif)


### Replication

At first I couldn't make sense of why it was happening, so I created a simpler version of the page on [Codepen] (and was able to replicate the bug). 

<p data-height="573" data-theme-id="0" data-slug-hash="vNNGbQ" data-default-tab="result" data-user="impactmass" class='codepen'>See the Pen <a href='http://codepen.io/impactmass/pen/vNNGbQ/'>iOS Scroll Issue</a> by Seun Martins (<a href='http://codepen.io/impactmass'>@impactmass</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>
Demo on Codepen


### Isolation (Understanding the bug)

After scrolling many times, I found out that the issue happens when the finger used in scrolling is on any of the form inputs (and that can be seen in the gif above).

To be sure, I decided to place an element on top of the form fields to confirm if the form fields were the problem. With an overlay element on the form fields, page scrolling worked fine. My guess here was that the device was trying to figure out if the user was about to focus on (i.e type in) the form field (at which point, there's no need to scroll the page).

But then I couldn't use this as a solution because the form fields were unable to receive focus once an element is placed directly above them. I thought of dynamically taking away the element when a user clicks on the form field area, but that won't be a good path to follow.

To double check, I took away the form elements totally and replace them with plain text (paragraphs of lorem ipsum) . With that, everything looked and worked fine. Scrolling seemed to be happening as it should, BUT the bug still happened once in a while. This meant that the form fields weren't actually the problem. I had to look elsewhere....CSS rules.

I started cutting out parts of my CSS code (divide and conquer) to figure out if any of them is the culprit. On taking out the part below, the scroll issue disappeared.

```css
  body {
    position: relative;
    width: 100%;
    height: 100%;
  }
  .content {
    position: absolute;
    left: 0px;
    top: 0;
    width: 100%;
    height: 100%;
  }
```

In particular, setting `height: 100%;` on the parent contentainer was preventing the scroll.


### Elimination

Why `height: 100%;`? 

Part of the requirement for the site is a slide-in animation from page to page on mobile as shown in this [demo]. The height CSS style to enable the animation of the view.

It's still not very clear to me how the `height` declaration gets the scrolll to hang; and why it only happens on iOS Safari browser. I've also not seen this issue documented anywhere online. So, I had to overwrite the height style rule for this page using JavaScript.

```js
  if (checkMobile && $('.hanging-page-sepcific-div').length > 0) {
    if ($('.form-input').length > 5) {
      $('body').addClass('remove-height-rule');
    } else {
      $('body').removeClass('remove-height-rule');
    }
  }
```
I had to live with this basic solution.  _*NB:* I modified this to use some explanatory CSS names._



[Codepen]:http://codepen.io/impactmass/pen/vNNGbQ
[demo]:http://dfsq.github.io/ngView-animation-effects/app/#/page/1

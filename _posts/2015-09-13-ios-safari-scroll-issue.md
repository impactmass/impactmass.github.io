---
layout: post
title:  "Percentage Height Content Scroll Issue on iOS Safari"
date:   2015-09-13 23:24:25
categories: ios safari css
img: safari-icon.jpg
image: safari.jpg
---

I came across an issue on a website I was working on. A particular page with a form had kept hanging when scrolled on mobile Safari (iOS). Scrolling of the page freezes when the finger used to scroll is on any of the form inputs (as shown in the gif below). It works fine on other browsers (both desktop and mobile).

![Scrolling hangs when finger is on the form input](http://f.cl.ly/items/150c2S060d0W402L0g3J/Screen%20Recording%202015-09-13%20at%2010.27%20PM.gif)

At first I couldn't make sense of what was happening. My initial thought was to place an element on top of the form fields. This was to confirm if the form fields were really the problem. With an overlay element on the form fields, page scrolling worked fine. The guess here would then be that maybe the device was trying to figure out if the user was about to begin typing into the form fields (at which point, there's no need to scroll the page).

But then I couldn't use this as a solution because the form fields were unable to receive focus once an element is placed directly above them. I thought of dynamically taking away the element when a user clicks on the form field area, but that won't be a good solution.

My second approach was to take away the form elements totally and replace them with plain text. Everything looked fine. Scrolling seem to be happening as it should BUT the bug still happens once in a while. Which means the problem is not exactly the form fields.

Then, I started cutting out parts of my CSS code (divide and conquer) to figure out the culprit. Till I got to this part:

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

Turns out that having `height: 100%;` on the content was preventing the scroll.

Here's a simple version demo:

<p data-height="573" data-theme-id="0" data-slug-hash="vNNGbQ" data-default-tab="result" data-user="impactmass" class='codepen'>See the Pen <a href='http://codepen.io/impactmass/pen/vNNGbQ/'>iOS Scroll Issue</a> by Seun Martins (<a href='http://codepen.io/impactmass'>@impactmass</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

#### Why `height: 100%;`? 

Part of the requirement for the site is smooth scrolling from page to page on mobile. So, I needed that CSS style to enable the animation of the view (ui-view in this case). Like so:

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
.content.ng-enter,
.content.ng-leave {
  -webkit-transition: all .5s ease;
  transition: all .5s ease;
}
.content.ng-enter {
  left: 100%;
}
.content.ng-enter-active {
  left: 0;
}
.content.ng-leave {
  left: 0;
}
.content.ng-leave-active {
  left: -100%;
}
```

I'm still not sure about the cause of this. To resolve it, I had to overwrite the height style rule  for this page only which meant writing some JS to add a class for this page alone.



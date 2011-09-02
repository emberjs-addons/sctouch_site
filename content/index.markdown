---
title: TransformJS - 3D and 2D Transforms for jQuery
---

# TransformJS 1.0 Beta

### 2D and 3D transforms as regular CSS properties you can set using `.css()` and animate using `.animate()`

<div id="sections" class="clearfix">
  <div class="section">
    <h4><a href="http://www.github.com/sproutcore/transformjs/downloads">Download</a></h4>
    <p>
      Download current and previous versions of TransformJS, feel free
to fork and/or clone the repository as well. 
    </p>
  </div>
  <div class="section">
    <h4><a href="http://www.github.com/sproutcore/transformjs/issues">Report Bugs</a></h4>
    <p>
      TransformJS is currently in beta. If you encounter any odd
behavior or have suggestions for improving the API, please let us know. 
    </p>
  </div>
</div>

## Demo

<div id="demobox" class="clearfix">
  <div id="code">
    <javascript>
      $(document).ready(function() {
        $('#forward').click(function() {
          $('#test').animate({
            translateX:'-=150',
            translateY:'+=150',
            scale:'+=2',
            rotateY: '+='+(2*Math.PI),
            rotateX: '+='+Math.PI,
            rotateZ: '+='+Math.PI
          },1500);    
        });  

        $('#backward').click(function() {
          $('#test').animate({
            translateX:'+=150',
            translateY:'-=150',
            scale:'-=2',
            rotateY: '-='+(2*Math.PI),
            rotateX: '-='+Math.PI,
            rotateZ: '-='+Math.PI
          },1500);
        })
      });
    </javascript>
  </div>
  <div id="preview">
    <button id="forward" class="btn large">Run Animation Forward</button>
    <button id="backward" class="btn large">Run Animation Back</button>
    <img id="test" src="images/demo.jpg">
    <h6>Photo Credit to <a
href="http://www.flickr.com/photos/mikebehnken/4998169342/">MikeBehnken</a></h6>
  </div>
</div>

## Overview

  CSS Transforms  were first introduced in WebKit in 2007, and have now 
reached mass-adoption by all the major browswer vendors. This is great news 
for web developers, especially in the case of 3D transforms which are
hardware-accelerated, resulting in extremely smooth animations and
responsive applications. 

  The API for applying transforms however, does not scale to complex applications 
which require intricate and complex management of transformations. TransformJS
attempts to identify and address these problems, allowing developers to
make use of transforms without having to be encumbered by cross browser
issues, and low-level APIs.

  Here's a snippet of code that uses TransformJS to apply multiple 3d
transformations to the same element, relative to their current value,
and animate the changes:

  <javascript>
    $('#test').animate({
      translateY:'+=150',
      scale:'+=2',
      rotateY: '+='+(2*Math.PI),
      rotateX: '+='+Math.PI,
      rotateZ: '+='+Math.PI
    },1500);    
  </javascript>

## Browser Support

  TransformJS supports all major browsers: Safari, Chrome, Firefox,
Opera, and IE. However, since all browsers don't share the same featureset,
TransformJS will make its best attempt at using the best feature
available. The table below outlines which properties are supported on
which browser. If a browser supports multiple transform styles, 3D
will be prefered to 2D, and 2D will be prefered to filter transforms.

  <table class="zebra-striped">
    <thead>
      <tr>
        <th></th>
        <th>3D Support</th>
        <th>2D Support</th>
        <th>Filter Support</th>
      </tr>
    </thead>
      <tr>
        <td>rotateX</td>
        <td>&#x2714;</td>
        <td></td>
        <td></td>
      </tr>
      <tr>
        <td>rotateY</td>
        <td>&#x2714;</td>
        <td></td>
        <td></td>
      </tr>
      <tr>
        <td>rotateZ</td>
        <td>&#x2714;</td>
        <td>&#x2714;</td>
        <td>&#x2714;</td>
      </tr>
      <tr>
        <td>translateX</td>
        <td>&#x2714;</td>
        <td>&#x2714;</td>
        <td>&#x2714; *</td>
      </tr>
      <tr>
        <td>translateY</td>
        <td>&#x2714;</td>
        <td>&#x2714;</td>
        <td>&#x2714; *</td>
      </tr>
      <tr>
        <td>translateZ</td>
        <td>&#x2714;</td>
        <td></td>
        <td></td>
      </tr>
      <tr>
        <td>scale</td>
        <td>&#x2714;</td>
        <td>&#x2714;</td>
        <td>&#x2714;</td>
      </tr>
    <tbody>

    </tbody>
  </table>

\* Since IE does not support translation transformations,
TransformJS will instead apply them to the `top` and `left` properties
which means the element must have `position: absolute` set.


## The Problems

  The following outlines the problems that TransformJS tries to solve,
and outlines the ways in which it solves them. 

#### Cross Browser Support

  Transforms have yet to be adopted by all the major browser vendors as
non-proprietary CSS properties. Therefore, to apply a 10x10 pixel translation 
to an element involves setting 5 properties: 
    
  <javascript>
    $('#test').css({
      '-webkit-transform': 'translate(10px,10px)',
      '-moz-transform': 'translate(10px,10px)',
      '-o-transform': 'translate(10px,10px)',
      '-ms-transform': 'translate(10px,10px)',
      'transform': 'translate(10px,10px)'
    });
  </javascript>

  Clearly, setting that property as a string and maintaining it is
very clumsy, and most people don't even bother with Mozilla, 
Opera, and IE support.  

#### Multiple Transforms

  Setting multiple transforms on the same element involves packing
multiple transforms to the same CSS property. Once the values you are
setting to your transforms become dynamic, setting a property begins to
involve fickle string manipulation:
  
  <javascript>
    $('#test').css({
      '-webkit-transform': 'translate('+tX+'px,'+tY+'px) scale('+sX+','+sY+')',
      '-moz-transform': 'translate('+tX+'px,'+tY+'px) scale('+sX+','+sY+')',
      '-o-transform': 'translate('+tX+'px,'+tY+'px) scale('+sX+','+sY+')',
      '-ms-transform': 'translate('+tX+'px,'+tY+'px) scale('+sX+','+sY+')',
      'transform': 'translate('+tX+'px,'+tY+'px) scale('+sX+','+sY+')'
    });
  </javascript>

#### Data Consistency

  If multiple classes in your application are affecting the display of
an element at once, you may run into consistency issues. For example, if
the animation library is animating the translation in the z-axis, while
your touch library is updating the translation in the x and y axis, they
will be overriding the same CSS string and have to write boilerplate to
ensure there's no jerking.

## The Solution

#### Feature Detection

  To solve the cross-browser support problem, TransformJS uses feature 
detection to analyze the supported features of the browser it is running in 
and adapts accordingly. For example, if you apply a `rotateY` transform to 
an element in Safari or Chrome, it will apply it as a 3D-transformation so 
it can take advantage of hardware-acceleration. However, on Firefox or Opera, 
it will apply a 2D transformation, and on IE, it will fallback to applying a 
transformation Filter.

#### CSS Hooks

  To solve the multiple transforms problem, TransformJS applies a cssHook for 
every transformation exposing them to `.css()` and `.animate()`. This
allows you to treat them as separate properties, rather than a single
one, and also allows you to more easily set a computed value to them.

#### In-Memory Matrix

  To solve the data consistency problem, TransformJS maintains an
in-memory matrix which it uses to apply the transforms. This
single-entry system ensures that all parts of your applications that
want to apply a transformation to an element can do so without any
regard to what other transformations have already been applied.

## License


  <javascript>
    Copyright (c) 2011 Strobe Inc.

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in
    all copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
    THE SOFTWARE.
  </javascript>
    


---

[Back to top](#)

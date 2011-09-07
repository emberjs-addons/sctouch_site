---
title: SproutCore Touch - Touch Gesture Support for SproutCore 2.0
---

# SproutCore Touch

### Multi-touch gestures for SproutCore 2.0 applications

<div id="sections" class="clearfix">
  <div class="section">
    <h4><a href="http://www.github.com/sproutcore/sproutcore-touch/downloads">Download</a></h4>
    <p>
      Clone the github repository, and download the starter-kit: A zip
file containing everything you need to get started.
    </p>
  </div>
  <div class="section">
    <h4><a href="http://www.github.com/sproutcore/sproutcore-touch/issues">Report Bugs</a></h4>
    <p>
      SproutCore Touch is currently in beta. If you encounter any odd
behavior or have suggestions for improving the API, please let us know. 
    </p>
  </div>
</div>

## Table of Contents

<ul id="toc">
  <li><a href="#demo">Demo</a></li>
  <li><a href="#overview">Overview</a></li>
  <li><a href="#usage">Usage</a></li>
  <li><a href="#customizing-gesture-recognizers">Customizing Gesture Recognizers</a></li>
  <li><a href="#creating-custom-gesture-recognizers">Creating Custom Gesture Recognizers</a></li>
  <li><a href="#discrete-vs-continuous-gestures">Discrete vs Continuous Gestures</a></li>
  <li><a href="#license">License</a></li>
</ul>

## Demo

Since the demos only work on a multitouch device, I've opted to record 
videos instead. If you'd like to play with the demos, please visit 
[SproutGram Demo](http://sproutgram.strobeapp.com).

#### Pinch

SC.PinchGestureRecognizer waits for two touches to begin on
the view, then tracks the distance between them, reporting any changes
through the `scale` property. You can also inspect the velocity property
to check whether the user was pinching in (positive), or out (negative).

<div id="demobox" class="clearfix">
  <div id="code">
    <javascript>
      MyApp.PinchableView = SC.View.extend({
        pinchChange: function(recognizer) {    
          this.$().css('scale',function(index, value) {
            return recognizer.get('scale') * value
          });
        },

        pinchEnd: function(recognizer) {
          var velocity = recognizer.get('velocity');
          
          if (velocity >= 0) {
            this._centerPhoto();
          }
          else {
            this._resetTransforms();
          }
        }
      });
    </javascript>
  </div>
  <div id="preview">
    <iframe class="video" src="http://player.vimeo.com/video/28529523?title=0&amp;byline=0&amp;portrait=0" width="220" height="150" frameborder="0"></iframe>
    <h6>Pinch Demo</h6>
  </div>
</div>

  ---

#### Pan

SC.PanGestureRecognizer tracks the center-point between a number of touches, 
and reports any changes through the x and y properties of the `translation` 
property of the gesture recognizer. By default, pan is a one-touch
gesture recognizer, though that can be changed by specifying the
`numberOfRequiredTouches` property through `panOptions` hash.

<div id="demobox" class="clearfix">
  <div id="code">
    <javascript>
      MyApp.PanableView = SC.View.extend({
        panOptions: {
          numberOfRequiredTouches: 2
        },

        panChange: function(recognizer) {
          var val = recognizer.get('translation');
        
          this.$().css({
            translateX: '+=%@'+val.x,
            translateY: '+=%@'+val.y
          });
        }
      });
    </javascript>
  </div>
  <div id="preview">
    <iframe class="video" src="http://player.vimeo.com/video/28529502?title=0&amp;byline=0&amp;portrait=0" width="220" height="150" frameborder="0"></iframe>
    <h6>Pan Demo</h6>
  </div>
</div>

  ---

#### Tap

SC.TapGestureRecognizer detects a touchstart and touchend and only
succeeds if the finger does not move beyond a threshold.

<div id="demobox" class="clearfix">
  <div id="code">
    <javascript>
      MyApp.TapableView = SC.View.extend({
        isZoomedIn: false,

        tapEnd: function() {
          if (this.get('isZoomedIn')) {
            this._resetTransforms();
          }
          else {
            this._centerPhoto();
          }
        }
      });
    </javascript>
  </div>
  <div id="preview">
    <iframe class="video" src="http://player.vimeo.com/video/28529533?title=0&amp;byline=0&amp;portrait=0" width="220" height="150" frameborder="0"></iframe>
    <h6>Tap Demo</h6>
  </div>
</div>

## Overview

Gestures coalesce multiple touch events to a single higher-level gesture
event. For example, a tap gesture recognizer takes information about a
touchstart event, a few touchmove events, and a touchend event and uses
some heuristics to decide whether or not that sequence of events qualifies
as a tap event. If it does, then it will notify the view of the higher-level
tap events.

Gesture events follow the format:

  * **[GESTURE_NAME]** Start - Sent when a gesture has gathered enough information
      to begin tracking the gesture

  * **[GESTURE_NAME]** Change - Sent when a gesture has already started and has
      received touchmove events that cause its state to change

  * **[GESTURE_NAME]** End - Sent when a touchend event is received and the gesture
      recognizer decides that the gesture is finished.

  * **[GESTURE_NAME]** Cancel - Sent when a touchcancel event is received.

There are two types of gestures: Discrete and Continuous gestures. In contrast
to continuous gestures, discrete gestures don't have any change events. Rather,
the end event is the only one that gets sent to the view.

#### Advanced Features

  * **Simultaneous Gesture Recognizers**: SproutCore Touch allows you to
    define multiple gesture recognizers (pinch/pan/tap) together at the
    same time, and allows the same gesture recognizers to operate on
    multiple views at the same time.

  * **Nested Gesture Recognizers**: SproutCore Touch does its best to
    try to determine how to respond to nested gesture recognizers. The
    more deeply nested gestures get priority when handling touches.

## Usage

While you wouldn't use SC.Gesture directly, all its subclasses implement the 
same API. For example, to implement pinch on a view, you implement one or more 
of the pinch events. For example:

  <javascript>
    var myView = SC.View.create({
      pinchStart: function(recognizer) {
        this.$().css('background','red');
      },

      pinchChange: function(recognizer) {
        var scale = recognizer.get('scale');
        this.$().css('scale',function(index, value) {
          return recognizer.get('scale') * value
        });
      },

      pinchEnd: function(recognizer) {
        this.$().css('background','blue');
      },

      pinchCancel: function(recognizer) {
        this.$().css('background','blue');
      }
    });
  </javascript>

pinchStart(), pinchEnd() and pinchCancel() will only get called once per
gesture, but pinchChange() will get called repeatedly called every time
one of the touches moves.

## Customizing Gesture Recognizers

Some of the gesture recognizers include properties that can be customized by 
the user for a specific instance of a view. For example, a pan gesture defaults 
to being a one-finger gesture, but in some scenarios, it must be defined as a 
two-finger gesture. In that case, you can override defaults by specifying an 
Options hash. 

  <javascript>
    var myView = SC.View.create({
      panOptions: {
        numberOfRequiredTouches: 2
      }
    });      
  </javascript>

## Creating Custom Gesture Recognizers

SC.Gesture also defines an API which its subclasses can implement to build
custom gestures. The methods are:

  * **didBecomePossible** - Called when a gesture enters a possible state. This
      means the gesture recognizer has accepted enough touches to match 
      the number of required touches. You would usually initialize your state
      in this callback.

  * **eventWasRejected** - Called if a view returns false from a gesture event.
      This callback allows you to reset internal state if the user rejects
      an event.

  * **shouldBegin** - Allows a gesture to block itself from entering a began state.
      This callback will continuously be called as touches move until it begins.

  * **shouldEnd** - Allows a gesture to block itself from entering an ended state.
      This callback gets called whenever a tracked touch gets a touchEnd event.

  * **didBegin** - Called when the gesture enters a began state. Called before the
     view receives the Start event.

  * **didChange** - Called when the gesture enters a began state, and when one of the
      touches moves. Called before the view receives the Change event.

  * **didEnd** - Called when the gesture enters an ended state. Called before the
     view receives the End event.

  * **didCancel** - Called when the gesture enters a cancelled state. Called before the
     view receives the Cancel event.

In all the callbacks, you can use the `touches` protected property to access the
touches hash. The touches hash is keyed on the identifiers of the touches, and the
values are the jQuery.Event objects. You can also access the length property to inspect 
how many touches are active, this is mostly useful in shouldBegin since every other 
callback can assume that there are as many active touches as specified in the 
numberOfRequiredTouches property.

## Discrete vs Continuous Gestures

There are two main classes of gesture recognizers: Discrete and Continuous 
gestures. Discrete gestures do not get Start, Change nor Cancel events sent, 
since they represent a single, instantaneous event, rather than a continuous 
motion. If you are implementing your own discrete gesture recognizer, you must 
set the isDiscreteGesture property to yes, and SC.Gesture will adapt its behavior.

Discrete gestures use the shouldEnd callback to either accept or decline the gesture
event. If it is declined, then the gesture will enter a Cancelled state.

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

### [Back to top](#)

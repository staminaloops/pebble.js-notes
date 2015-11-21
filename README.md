# pebble.js-notes

## 1. Window

`Window` is the basic building block in your Pebble.js application. All windows share some common properties and methods.

Pebble.js provides 3 types of Windows:

 * **[Card]**: Displays a title, a subtitle, a banner image and text on a screen. The position of the elements are fixed and cannot be changed.
 * **[Menu]**: Displays a menu on the Pebble screen. This is similar to the standard system menu in Pebble.
 * **[Window]**: The Window by itself is the most flexible. It allows you to add different **[Element]s** (*[Circle]*, *[Image]*, *[Rect]*, *[Text]*, *[TimeText]*) and to specify a *position* and *size* for each of them. You can also *animate them*.

### 1.1 Common Window properties and methods

| Name           | Type      | Default   | Description                                                                                     |
| ----           | :-------: | --------- | -------------                                                                                   |
| `clear`        | boolean   |           |                                                                                                 |
| `action`       | actionDef | None      | An action bar will be shown when configured with an `actionDef`.                                |
| `fullscreen`   | boolean   | false     | When true, the Pebble status bar will not be visible and the window will use the entire screen. |
| `scrollable`   | boolean   | false     | Whether the user can scroll this Window with the up and down button. When this is enabled, single and long click events on the up and down button will not be transmitted to your app. |

<a id="window-actiondef"></a>
#### Window actionDef
[Window actionDef]: #window-actiondef

A `Window` action bar can be displayed by setting its Window `action` property to an `actionDef`:

| Name              | Type      | Default   | Description                                                                                            |
| ----              | :-------: | --------- | -------------                                                                                          |
| `up`              | Image     | None      | An image to display in the action bar, next to the up button.                                          |
| `select`          | Image     | None      | An image to display in the action bar, next to the select button.                                      |
| `down`            | Image     | None      | An image to display in the action bar, next to the down button.                                        |
| `backgroundColor` | Image     | 'black'   | The background color of the action bar. You can set this to 'white' for windows with black backgrounds |

````js
var card = new UI.Card({
  action: {
    up: 'images/action_icon_plus.png',
    down: 'images/action_icon_minus.png'
  }
});
````

You will need to add images to your project according to the [Using Images] guide in order to display action bar icons.

#### Window.show()

This will push the window to the screen and display it. If user press the 'back' button, they will navigate to the previous screen.

#### Window.hide()

This hides the window.

If the window is currently displayed, this will take the user to the previously displayed window.

If the window is not currently displayed, this will remove it from the window stack. The user will not be able to get back to it with the back button.

````js
var splashScreen = new UI.Card({ banner: 'images/splash.png' });
splashScreen.show();

var mainScreen = new UI.Menu();

setTimeout(function() {
  // Display the mainScreen
  mainScreen.show();
  // Hide the splashScreen to avoid showing it when the user press Back.
  splashScreen.hide();
}, 400);
````

#### Window.on('click', button, handler)

Registers a handler to call when `button` is pressed.

````js
wind.on('click', 'up', function() {
  console.log('Up clicked!');
});
````

You can register a handler for the 'up', 'select', 'down', and 'back' buttons.

**Note:** You can also register button handlers for `longClick`.

#### Window.on('longClick', button, handler)

Just like `Window.on('click', button, handler)` but for 'longClick' events.

#### Window.on('show', handler)

Registers a handler to call when the window is shown. This is useful for knowing when a user returns to your window from another. This event is also emitted when programmatically showing the window. This does not include when a Pebble notification popup is exited, revealing your window.

````js
// Define the handler before showing.
wind.on('show', function() {
  console.log('Window is shown!');
});

// The show event will emit, and the handler will be called.
wind.show();
````

#### Window.on('hide', handler)

Registers a handler to call when the window is hidden. This is useful for knowing when a user exits out of your window or when your window is no longer visible because a different window is pushed on top. This event is also emitted when programmatically hiding the window. This does not include when a Pebble notification popup obstructs your window.

It is recommended to use this instead of overriding the back button when appropriate.

````js
wind.on('hide', function() {
  console.log('Window is hidden!');
});
````

#### Window.action(actionDef)

This is a special nested accessor to the `action` property which takes an `actionDef`. It can be used to set a new `actionDef`. See [Window actionDef].

````js
card.action({
  up: 'images/action_icon_up.png',
  down: 'images/action_icon_down.png'
});
````

#### Window.action(field, value)

You may also call `Window.action` with two arguments, `field` and `value`, to set specific fields of the window's `action` propery. `field` is a string refering to the [actionDef] property to change. `value` is the new property value to set.

````js
card.action('up', 'images/action_icon_plus.png');
````

#### Window.fullscreen(fullscreen)

Accessor to the `fullscreen` property. See [Window].

### 1.1 Properties and methods just for the Window type (dynamic)

A [Window] instantiated directly is a dynamic window that can display a completely customizable user interface on the screen. Dynamic windows are **initialized empty and will need [Element]s added to it**. [Card] and [Menu] will not display elements added to them in this way.

````js
// Create a dynamic window
var wind = new UI.Window();

// Add a rect element
var rect = new UI.Rect({ size: new Vector2(20, 20) });
wind.add(rect);

wind.show();
````

#### Window.add(element)

Adds an element to to the [Window]. The element will be immediately visible.

#### Window.insert(index, element)

Inserts an element at a specific index in the list of Element.

#### Window.remove(element)

Removes an element from the [Window].

#### Window.index(element)

Returns the index of an element in the [Window] or -1 if the element is not in the window.

#### Window.each(callback)

Iterates over all the elements on the [Window].

````js
wind.each(function(element) {
  console.log('Element: ' + JSON.stringify(element));
});
````

## 2. A Window can have Elements

### Element

There are 4 types of [Element] that can be instantiated at the moment: **[Circle]**, **[Image]**, **[Rect]** and **[Text]**.

They all share some common properties:

| Name              | Type      | Default   | Description                                                                    |
| ------------      | :-------: | --------- | -------------                                                                  |
| `position`        | Vector2   |           | Position of this element in the window.                                        |
| `size`            | Vector2   |           | Size of this element in this window. Note that [Circle] uses `radius` instead. |
| `borderColor`     | string    | ''        | Color of the border of this element ('clear', 'black',or 'white').             |
| `backgroundColor` | string    | ''        | Background color of this element ('clear', 'black' or 'white').                |

All properties can be initialized by passing an object when creating the Element, and changed with accessors functions who have the name of the properties. Calling an accessor without a parameter will return the current value.

````js
var Vector2 = require('vector2');

// passing an object when creating the Element
var element = new Text({ 
  position: new Vector2(0, 0), 
  size: new Vector2(144, 168) 
});

// change/add properties with accessors functions who have the name of the properties
element.borderColor('white');

// Calling an accessor without a parameter will return the current value.
console.log('This element background color is: ' + element.backgroundColor());
````

#### Element.index()

Returns the index of the element in its [Window] or -1 if the element is not part of a window.

#### Element.remove()

Removes the element from its [Window].

#### Element.animate(animateDef, [duration=400])

The `position` and `size` are currently the only Element properties that can be animated. An `animateDef` is object with any supported properties specified. See [Element] for a description of those properties. The default animation duration is 400 milliseconds.

````js
// Use the element's position and size to avoid allocating more vectors.
var pos = element.position();
var size = element.size();

// Use the *Self methods to also avoid allocating more vectors.
pos.addSelf(size);
size.addSelf(size);

// Schedule the animation with an animateDef
element.animate({ position: pos, size: size });
````

Each element has its own animation queue. Animations are queued when `Element.animate` is called multiple times at once with the same element. The animations will occur in order, and the first animation will occur immediately. Note that because each element has its own queue, calling `Element.animate` across different elements will result all elements animating the same time. To queue animations across multiple elements, see [Element.queue(callback(next))].

When an animation begins, its destination values are saved immediately to the [Element].

`Element.animate` is chainable.

#### Element.animate(field, value, [duration=400])

You can also animate a single property by specifying a field by its name.

````js
var pos = element.position();
pos.y += 20;
element.animate('position', pos, 1000);
````

<a id="element-queue-callback-next"></a>
#### Element.queue(callback(next))
[Element.queue(callback(next))]: #element-queue-callback-next

`Element.queue` can be used to perform tasks that are dependent upon an animation completing, such as preparing the element for a different animation. `Element.queue` can also be used to coordinate animations across different elements. It is recommended to use `Element.queue` instead of a timeout if the same element will be animated after the custom task.

The `callback` you pass to `Element.queue` will be called with a function `next` as the first parameter. When `next` is called, the next item in the animation queue will begin. Items includes callbacks added by `Element.queue` or animations added by `Element.animate` before an animation is complete. Calling `next` is equivalent to calling `Element.dequeue`.

````js
element
  .animate('position', new Vector2(0, 0)
  .queue(function(next) {
    this.backgroundColor('white');
    next();
  })
  .animate('position', new Vector2(0, 50));
````

`Element.queue` is chainable.

#### Element.dequeue()

`Element.dequeue` can be used to continue executing items in the animation queue. It is useful in cases where the `next` function passed in `Element.queue` callbacks is not available. See [Element.queue(callback(next))] for more information on the animation queue.

#### Element.position(position)

Accessor to the `position` property. See [Element].

#### Element.size(size)

Accessor to the `size` property. See [Element].

#### Element.borderColor(color)

Accessor to the `borderColor` property. See [Element].

#### Element.backgroundColor(color)

Accessor to the `backgroundColor` property. See [Element].

### Circle

An [Element] that displays a circle on the screen.

Default properties value:

 * `backgroundColor`: 'white'
 * `borderColor`: 'clear'

[Circle] also has the additional property `radius` which it uses for size rather than `size`. [Circle] is also different in that it positions its origin at the position, rather than anchoring by its top left. These differences are to keep the graphics operation characteristics that it is built upon.

````js
var wind = new UI.Window();

var circle = new UI.Circle({
  position: new Vector2(72, 84),
  radius: 25,
  backgroundColor: 'white',
});

wind.add(circle);
wind.show(circle);
````

#### Circle.radius(radius)

Accessor to the `radius` property. See [Circle]

### Rect

An [Element] that displays a rectangle on the screen.

The [Rect] element has the following properties. Just like any other [Element] you can initialize those properties when creating the object or use the accessors.

| Name              | Type      | Default   | Description                                                        |
| ------------      | :-------: | --------- | -------------                                                      |
| `backgroundColor` | string    | "white"   | Background color of this element ('clear', 'black' or 'white').    |
| `borderColor`     | string    | "clear"   | Color of the border of this element ('clear', 'black',or 'white'). |

### Text

An [Element] that displays text on the screen.

The [Text] element has the following properties. Just like any other [Element] you can initialize those properties when creating the object or use the accessors.

| Name              | Type      | Default   | Description                                                                                                                                                                                                                                                                                                                                                |
| ------------      | :-------: | --------- | -------------                                                                                                                                                                                                                                                                                                                                              |
| `text`            | string    | ""        | The text to display in this element.                                                                                                                                                                                                                                                                                                                       |
| `font`            | string    |           | The font to use for that text element. See [Using Fonts] for more information on the different fonts available and how to add your own fonts.                                                                                                                                                                                                              |
| `color`           |           | 'white'   | Color of the text ('white', 'black' or 'clear').                                                                                                                                                                                                                                                                                                           |
| `textOverflow`    | 'string'  |           | How to handle text overflow in this text element ('wrap', 'ellipsis' or 'fill').                                                                                                                                                                                                                                                                           |
| `textAlign`       | 'string'  |           | How to align text in this element ('left', 'center' or 'right').                                                                                                                                                                                                                                                                                           |
| `borderColor`     | string    | 'clear'   | Color of the border of this element ('clear', 'black',or 'white').                                                                                                                                                                                                                                                                                         |
| `backgroundColor` | string    | 'clear'   | Background color of this element ('clear', 'black' or 'white').                                                                                                                                                                                                                                                                                            |

### TimeText

A [Text] element that displays time formatted text on the screen.

#### Displaying time in a TimeText element

If you want to display the current time or date, use the `TimeText` element with a time formatting string in the `text` property. The time to redraw the time text element will be automatically calculated based on the format string. For example, a `TimeText` element with the format `'%M:%S'` will be redrawn every second because of the seconds format `%S`.

The available formatting options follows the C `strftime()` function:

| Specifier   | Replaced by                                                                                                                                                | Example                    |
| ----------- | -------------                                                                                                                                              | ---------                  |
| %a          | An abbreviation for the day of the week.                                                                                                                   | "Thu"                      |
| %A          | The full name for the day of the week.                                                                                                                     | "Thursday"                 |
| %b          | An abbreviation for the month name.                                                                                                                        | "Aug"                      |
| %B          | The full name of the month.                                                                                                                                | "August"                   |
| %c          | A string representing the complete date and time                                                                                                           | "Mon Apr 01 13:13:13 1992" |
| %d          | The day of the month, formatted with two digits.                                                                                                           | "23"                       |
| %H          | The hour (on a 24-hour clock), formatted with two digits.                                                                                                  | "14"                       |
| %I          | The hour (on a 12-hour clock), formatted with two digits.                                                                                                  | "02"                       |
| %j          | The count of days in the year, formatted with three digits (from `001` to `366`).                                                                          | "235"                      |
| %m          | The month number, formatted with two digits.                                                                                                               | "08"                       |
| %M          | The minute, formatted with two digits.                                                                                                                     | "55"                       |
| %p          | Either `AM` or `PM` as appropriate.                                                                                                                        | "AM"                       |
| %S          | The second, formatted with two digits.                                                                                                                     | "02"                       |
| %U          | The week number, formatted with two digits (from `00` to `53`; week number 1 is taken as beginning with the first Sunday in a year). See also `%W`.        | "33"                       |
| %w          | A single digit representing the day of the week: Sunday is day 0.                                                                                          | "4"                        |
| %W          | Another version of the week number: like `%U`, but counting week 1 as beginning with the first Monday in a year.                                           | "34"                       |
| %x          | A string representing the complete date.                                                                                                                   | "Mon Apr 01 1992"          |
| %X          | A string representing the full time of day (hours, minutes, and seconds).                                                                                  | "13:13:13"                 |
| %y          | The last two digits of the year.                                                                                                                           | "01"                       |
| %Y          | The full year, formatted with four digits to include the century.                                                                                          | "2001"                     |
| %Z          | Defined by ANSI C as eliciting the time zone if available; it is not available in this implementation (which accepts `%Z` but generates no output for it). |                            |
| %%          | A single character, `%`.                                                                                                                                   | "%"                        |

#### Text.text(text)

Sets the text property. See [Text].

#### Text.font(font)

Sets the font property. See [Text].

#### Text.color(color)

Sets the color property. See [Text].

#### Text.textOverflow(textOverflow)

Sets the textOverflow property. See [Text].

#### Text.textAlign(textAlign)

Sets the textAlign property. See [Text].

#### Text.updateTimeUnit(updateTimeUnits)

Sets the updateTimeUnits property. See [Text].

#### Text.borderColor(borderColor)

Sets the borderColor property. See [Text].

#### Text.backgroundColor(backgroundColor)

Sets the backgroundColor property. See [Text].

### Image

An [Element] that displays an image on the screen.

The [Image] element has the following properties. Just like any other [Element] you can initialize those properties when creating the object or use the accessors.

| Name              | Type      | Default   | Description                                                                                                                                                                                                                                                                                                                                                |
| ------------      | :-------: | --------- | -------------                                                                                                                                                                                                                                                                                                                                              |
| `image`           | string    | ""        | The resource name or path to the image to display in this element. See [Using Images] for more information and how to add your own images. |
| `compositing`     | string    | "normal"  | The compositing operation used to display the image. See [Image.compositing(compop)] for a list of possible compositing operations.                |


#### Image.image(image)

Sets the image property. See [Image].

<a id="image-compositing"></a>
#### Image.compositing(compop)
[Image.compositing(compop)]: #image-compositing

Sets the compositing operation to be used when rendering. Specify the compositing operation as a string such as `"invert"`. The following is a list of compositing operations available.

| Compositing | Description                                                            |
| ----------- | :--------------------------------------------------------------------: |
| `"normal"`  | Display the image normally. This is the default.                       |
| `"invert"`  | Display the image with inverted colors.                                |
| `"or"`      | White pixels are shown, black pixels are clear.                        |
| `"and"`     | Black pixels are shown, white pixels are clear.                        |
| `"clear"`   | The image's white pixels are painted as black, and the rest are clear. |
| `"set"`     | The image's black pixels are painted as white, and the rest are clear. |



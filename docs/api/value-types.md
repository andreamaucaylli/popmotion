---
title: Value Types
description: "Parsers, transformers and tests for common style value types, eg: %, hex codes etc."
---

# Value Types

Popmotion works with raw numbers, which are sometimes composed into more complex values using `composite`.

To help convert these into commonly-used special value types, like `px` or `hex`, we provide an optional module called `style-value-types`:

```bash
npm install style-value-types --save 
```

Each value type has three functions:

- `test`: Returns `true` if the provided value is of that type.
- `parse`: Returns the value in a format suitable for animation. Either a `number` or `{ [key: string]: number }`.
- `transform`: The reverse of `parse`. Accepts a `number` or map of named numbers and converts that into the value type.

## Example

```javascript
import { color } from 'style-value-types';

// Test
color.test('#fff'); // true
color.test(0); // false

// Parse
color.parse('rgba(255, 255, 255, 0)');
// { red: 255, green: 255, blue: 255, alpha: 0 }

// Transform
color.transform({ hue: 200, saturation: 100, lightness: 50, alpha: 0.5 });
// 'hsla(200, 100%, 50%, 0.5)'
```

## Included value types

- `alpha`: `Number` between `0` and `1`
- `complex`: `String` containing arbitrary sequence of numbers mixed with other characters. See below.
- `color`: `String` of either `hex`, `hsla` or `rgba` type
- `degrees`: `String` ending in `deg`
- `hex`: `String` beginning with `#` and followed by 3 or 6-digit hex code
- `hsla`: `String` with valid `hsla` property
- `percent`: `String` ending in `%`
- `px`: `String` ending in `px`
- `scale`: `Number` with a `default` of `1` instead of `0`
- `rgbUnit`: Integer between `1` and `255`
- `rgba`: String in `rgba(rgbUnit, rgbUnit, rgbUnit, alpha)` format

## Complex type

The `complex` value type is slightly different to the others. Instead of a `transform` method, it has a `createTransformer` method which returns the `transform` method:

```javascript
const svgPath = 'M150 0 L75 200';
const transform = complex.createTransformer(svgPath);
```

The returned `transform` function is unique to the string given to it. When this function is provided an object of the same format as returned by `complex.parse()` (in this example `complex.parse(svgPath)`), it will use the original string as a template.

Example: 

```javascript
transform({
  '0': 300,
  '1': 0,
  '2': 100,
  '3': 200
}); // Returns 'M300 0 L100 200'
```

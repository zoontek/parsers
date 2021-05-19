# TO SCSS MAP

## Description

Generates scss files containing scss map and function / mixin to access the values of the tokens.

## Interface

```ts
interface parser {
  name: 'to-scss-map';
  options?: options: Partial<{
    fileName?: string | PartialRecord<TokensType, string>;
    functionName?: string | PartialRecord<ToScssMapTokenType, string>;
    mixinName?: string | PartialRecord<ToScssMapTokenType, string>;
    variableName?: string | PartialRecord<TokensType, string>;
    formatName?: 'camelCase' | 'kebabCase' | 'snakeCase';
    formatConfig: Partial<{
      endOfLine: 'auto' | 'lf' | 'crlf' | 'cr';
      tabWidth: number;
      useTabs: boolean;
      singleQuote: boolean;
    }>;
    formatTokens?: {
      colorFormat?: {
        format: 'rgb' | 'prgb' | 'hex' | 'hex6' | 'hex3' | 'hex4' | 'hex8' | 'name' | 'hsl' | 'hsv';
      };
      fontSizeFormat?: {
        unit?: 'px' | 'rem';
      };
    };
    splitBy?: string;
    omitFunctionAndMixin?: boolean;
  }>;
}
```

### Options

| Parameter                          | Required | Type                                                              | Default     | Description                                                                                                                                                                                                                                                |
| ---------------------------------- | -------- | ----------------------------------------------------------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `filename`                         | optional | `string` `object`                                                 |             | How to name the map variable. Can be a mapping with token typeas key and file name as a value or a RegExp pattern. See [advanced usage example](https://github.com/Specifyapp/parsers/tree/feat/parser/add-to-sass-maps/parsers/to-scss-map#config-1).     |
| `functionName`                     | optional | `string` `object`                                                 |             | How to name the map variable. Can be a mapping with token typeas key and function name as a value or a RegExp pattern. See [advanced usage example](https://github.com/Specifyapp/parsers/tree/feat/parser/add-to-sass-maps/parsers/to-scss-map#config-1). |
| `mixinName`                        | optional | `string` `object`                                                 |             | How to name the map variable. Can be a mapping with token typeas key and mixin name as a value or a RegExp pattern. See [advanced usage example](https://github.com/Specifyapp/parsers/tree/feat/parser/add-to-sass-maps/parsers/to-scss-map#config-1).    |
| `variableName`                     | optional | `string` `object`                                                 |             | How to name the map variable. Can be a mapping with token typeas key and variable name as a value or a RegExp pattern. See [advanced usage example](https://github.com/Specifyapp/parsers/tree/feat/parser/add-to-sass-maps/parsers/to-scss-map#config-1). |
| `formatName`                       | optional | `camelCase` `kebabCase` `snakeCase`                               | `kebabCase` | The case transformation you want to apply to your design token name.                                                                                                                                                                                       |
| `formatConfig.endOfLine`           | optional | `auto` `lf` `crlf` `cr`                                           | `auto`      | [Prettier documentation](https://prettier.io/docs/en/options.html#end-of-line)                                                                                                                                                                             |
| `formatConfig.tabWidth`            | optional | `number`                                                          | `2`         | [Prettier documentation](https://prettier.io/docs/en/options.html#tab-width)                                                                                                                                                                               |
| `formatConfig.useTabs`             | optional | `boolean`                                                         | `true`      | [Prettier documentation](https://prettier.io/docs/en/options.html#tabs)                                                                                                                                                                                    |
| `formatConfig.singleQuote`         | optional | `boolean`                                                         | `false`     | [Prettier documentation](https://prettier.io/docs/en/options.html#quotes)                                                                                                                                                                                  |
| `formatTokens.colorFormat.format`  | optional | `rgb` `prgb` `hex` `hex6` `hex3` `hex4` `hex8` `name` `hsl` `hsv` | `rgb`       | The color format you want to apply to your potential color design token.                                                                                                                                                                                   |
| `formatTokens.fontSizeFormat.unit` | optional | `px` `rem`                                                        | `none`      |                                                                                                                                                                                                                                                            |
| `splitBy`                          | optional | `string`                                                          |             | The character used to define the nesting of the values in the map object (e.g. The name of the color in [this example](https://github.com/Specifyapp/parsers/tree/feat/parser/add-to-sass-maps/parsers/to-scss-map#input-2))                               |
| `omitFunctionAndMixin`             | optional | `boolean`                                                         | `false`     | When set to true, blocks the creation of the getter functions and mixins.                                                                                                                                                                                  |

## Types

ℹ️ **Please be aware that, depending on the order you use parsers, their input and output types have to match.**

### Input

Array of object with at least name, value and type:

```ts
Array<{name: string, value: any, type: string}>
```

### Output

```ts
Array<{name: string, value: {content: string}}>
```

## Basic Usage

### Config

```json
{
  "name": "to-scss-map",
  "options": {}
}
...
```

### Before/After

#### Input

```json
[
  {
    "name": "primary",
    "value": {
      "a": 1,
      "b": 255,
      "g": 189,
      "r": 198
    },
    "type": "color"
  },
  {
    "name": "baseSpace01",
    "value": {
      "unit": "px",
      "measure": 4
    },
    "type": "measurement"
  },
  {
    "name": "body",
    "value": {
      "font": {
        "id": "69d2d62e-4d62-45d7-b85f-5da2f9f0c0d4",
        "name": "Roboto-Regular",
        "value": {
          "fontFamily": "Roboto",
          "fontWeight": 400,
          "fontPostScriptName": "Roboto-Regular"
        },
        "type": "font"
      },
      "fontSize": {
        "value": {
          "unit": "px",
          "measure": 16
        }
      },
      "textAlign": {
        "vertical": "top",
        "horizontal": "left"
      },
      "lineHeight": {
        "value": {
          "unit": "px",
          "measure": 20
        }
      },
      "fontVariant": ["small-caps"]
    },
    "type": "textStyle"
  }
]
```

#### Output

File tree

```
folder
|- measurement.scss
|- color.scss
|- textStyle.scss
```

measurement.scss

```scss
@use "sass:map";

$measurement: (
  baseSpace01: 4px,
);
@function get-measurement($levels...) {
  $fetched: $measurement;
  @each $level in $levels {
    @if map-has-key($fetched, $level) {
      $fetched: map-get($fetched, $level);
    } @else {
      @error "There is no `#{$level}` in the `#{$measurement}` map";
    }
  }
  @if type-of($fetched) == map {
    @error "Non usable value. Got `#{$measurement}`";
  }

  @return $fetched;
}
```

color.scss

```scss
@use "sass:map";

$color: (
  primary: rgb(198, 189, 255),
);
@function get-color($levels...) {
  $fetched: $color;
  @each $level in $levels {
    @if map-has-key($fetched, $level) {
      $fetched: map-get($fetched, $level);
    } @else {
      @error "There is no `#{$level}` in the `#{$color}` map";
    }
  }
  @if type-of($fetched) == map {
    @error "Non usable value. Got `#{$color}`";
  }

  @return $fetched;
}
```

textStyle.scss

```scss
@use "sass:map";

$textStyle: (
  body: (
    font-family: 'Roboto-Regular',
    font-size: 16px,
    font-weight: 400,
    line-height: 20px,
  ),
);
@mixin text-style($levels...) {
  $fetched: $text-style;
  @each $level in $levels {
    @if map-has-key($fetched, $level) {
      $fetched: map-get($fetched, $level);
    } @else {
      @error "There is no `#{$level}` in the `#{$text-style}` map";
    }
  }
  @if type-of($fetched) != map {
    @error "Non usable value. Got `#{$text-style}`";
  }

  @each $prop, $value in $fetched {
    #{$prop}: $value;
  }
}
```

#### How to use the generated files

In another scss file you can use it like

```scss
@import 'color.scss';
@import 'measurement.scss';
@import 'textStyle.scss';

.my-example {
  background: get-color(primary);
  margin: get-measurement(baseSpace01);
  @include text-style(body);
}
```

## Advanced Usage

### Config

```json
{
  "name": "to-scss-map",
  "options": {
    "variableName": {
      "color": "custom-color",
      "measurement": "my-{{type}}",
      "textStyle": "typography"
    },
    "fileName": {
      "color": "_colors",
      "measurement": "_sizing",
      "textStyle": "_typography"
    },
    "formatTokens": {
      "fontSize": {
        "unit": "rem"
      }
    },
    "splitBy": "/"
  }
}
...
```

### Before/After

#### Input

```json
[
  {
    "name": "primary / default",
    "value": {
      "a": 1,
      "b": 255,
      "g": 189,
      "r": 198
    },
    "type": "color"
  },
  {
    "name": "baseSpace01",
    "value": {
      "unit": "px",
      "measure": 4
    },
    "type": "measurement"
  },
  {
    "name": "body",
    "value": {
      "font": {
        "id": "69d2d62e-4d62-45d7-b85f-5da2f9f0c0d4",
        "name": "Roboto-Regular",
        "value": {
          "fontFamily": "Roboto",
          "fontWeight": 400,
          "fontPostScriptName": "Roboto-Regular"
        },
        "type": "font"
      },
      "fontSize": {
        "value": {
          "unit": "px",
          "measure": 16
        }
      },
      "textAlign": {
        "vertical": "top",
        "horizontal": "left"
      },
      "lineHeight": {
        "value": {
          "unit": "px",
          "measure": 20
        }
      },
      "fontVariant": ["small-caps"]
    },
    "type": "textStyle"
  }
]
```

#### Output

File tree

```
folder
|- _sizing.scss
|- _colors.scss
|- _typography.scss
```

\_sizing.scss

```scss
@use "sass:map";

$my-measurement: (
  baseSpace01: 4px,
);

@function get-my-measurement($levels...) {
  $fetched: $my-measurement;
  @each $level in $levels {
    @if map-has-key($fetched, $level) {
      $fetched: map-get($fetched, $level);
    } @else {
      @error "There is no `#{$level}` in the `#{$my-measurement}` map";
    }
  }
  @if type-of($fetched) == map {
    @error "Non usable value. Got `#{$my-measurement}`";
  }

  @return $fetched;
}
```

\_color.scss

```scss
@use "sass:map";

$custom-color: (
  primary: (
    default: rgb(198, 189, 255),
  ),
);
@function get-color($levels...) {
  $fetched: $custom-color;
  @each $level in $levels {
    @if map-has-key($fetched, $level) {
      $fetched: map-get($fetched, $level);
    } @else {
      @error "There is no `#{$level}` in the `#{$custom-color}` map";
    }
  }
  @if type-of($fetched) == map {
    @error "Non usable value. Got `#{$custom-color}`";
  }

  @return $fetched;
}
```

\_typography.scss

```scss
@use "sass:map";

$typography: (
  body: (
    font-family: 'Roboto-Regular',
    font-size: 1rem,
    font-weight: 400,
    line-height: 20px,
  ),
);
@mixin typography($levels...) {
  $fetched: $typography;
  @each $level in $levels {
    @if map-has-key($fetched, $level) {
      $fetched: map-get($fetched, $level);
    } @else {
      @error "There is no `#{$level}` in the `#{$typography}` map";
    }
  }
  @if type-of($fetched) != map {
    @error "Non usable value. Got `#{$typography}`";
  }

  @each $prop, $value in $fetched {
    #{$prop}: $value;
  }
}
```

#### How to use the generated files

In another scss file you can use it like

```scss
@import 'color.scss';
@import 'measurement.scss';
@import 'textStyle.scss';

.my-example {
  background: get-custom-color(primary, default);
  margin: get-my-measurement(baseSpace01);
  @include typography(body);
}
```
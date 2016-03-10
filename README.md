![Megatype](http://studiothick.github.io/megatype/favicons/apple-touch-icon-144x144.png)

# Megatype
Execute typographic structure with ease    

##Install
Can be installed as a bower component:     
```bower install megatype --save-dev```    

Then add a load path to your build process of choice (gulp example shown):    
```js
// gulpfile.babel.js

import gulp-sass from "gulp-sass";

gulp.task('styles', () => {
    return gulp.src(“app/styles/screen.scss”)
        .pipe(gulp-sass({
            outputStyle: 'expanded',
            precision: 6,
            includePaths: [
                './node_modules/megatype'
            ]
        })
        .pipe(gulp.dest('dist'));
});
```

And import into your styles with:      
```scss
@import "megatype";
```    

##Using MegaType
MegaType provides typesetting tools, and some breakpoint mixins.    


###Config
It's recommended to copy `_config.scss` into your code base and override the `!default` settings where required, leaving unmodified values commented out.    
First, set up your breakpoint map (defaults shown) and your baseline snap & scaling preferences:    
```scss
// config.scss

// enable responsive baseline & type scaling.
// increases root font size from each breakpoint, starting from the size specified in the rootsizes below
$baseline-scaling: false;

// enable formal baseline grid
// snaps all type to the baseline grid
$baseline-snap: true;

// map for flexible retrieval of breakpoint info
$breakpoint-map: (
    0: (
        start:      0px,
        max:        420px,
        rootsize:   12px
    ),
    1: (
        start:      480px,
        max:        560px,
        rootsize:   14px
    ),
    2: (
        start:      768px,
        max:        840px,
        rootsize:   16px
    ),
    3: (
        start:      980px,
        max:        1080px,
        rootsize:   18px
    ),
    4: (
        start:      1280px,
        max:        1440px,
        rootsize:   20px
    )
);

```
Breaking down the map:    
- `start` is the start position of the breakpoint. Can be `px` or `em`.    
- `max` is the max width of the container. Can be `px`, `em`, or `%`.   
- `rootsize` is the base font size applied to the `html` element. Can be `px` or `rem`. This also controls our grid size at this breakpoint.         
These values can be retreived easily using the `break-get` function, eg:    
```scss
.my-component {
    width: break-get(3, max);
}
```

To intialise the baseline, call MegaType at the top of your code:   
```scss
@include megatype;
```

You may also wish to apply `max` widths from your config to any content you wish to be contained:     
```scss
.my-container {
    @include set-container;
}
```
    
Next we need to have a look at providing some typographic info about each font we want to typeset. Modify the ones provided, or add your own:       
```scss
// Set cap height to set to the baseline.
// Here are some cap-height sizes to get you started:
// Georgia: 0.66, Times / Times New Roman: 0.65, Palatino: 0.52
// Lucida Grande: 0.72, Helvetica: 0.65, Helvetica Neue: 0.71, Verdana: 0.76, Tahoma: 0.76
$sans: (
    font-family: '"Helvetica Neue", Arial, sans-serif',
    regular: normal,
    bold: bold,
    cap-height: 0.71
) !default;

$serif: (
    font-family: 'Georgia, serif',
    regular: normal,
    bold: bold,
    cap-height: 0.69
) !default;

$monospace: (
    font-family: 'Menlo, Courier, monospace',
    regular: 400,
    cap-height: 0.68
) !default;
```
To set `cap-height`; It's recommended just to tweak this by trial and error until a typefece sits nicely on the baseline.    
   
**Tip:** set `$debug-allow` and `$debug-baseline` to `true`, and you will be able to see a visual representation of this on your typeset elements.   


###Setting type

With our rootsize initialised and our typographic config all set up, we can start setting type. First, provide the type config, and then provide `$fontsize`, `$lineheight`, `$leader` and `$trailer` in `px`, `rem`, or baseline units. One baseline unit is equivalent to the configured `rootsize` for that media query.

```scss
p {
    // we can set our type using pixels, rems or baseline units
    @include typeset($font: $sans, $fontsize: 16px, $lineheight: 2rem, $leader: 0, $trailer: 2);
}
```
The `$fontsize`, `$leader` and `$trailer` are output in `rem`, whereas the lineheight is output as a unitless number. 
`$leader` is calculated alongside an offset to put our type on the baseline, and output as a `top` value. This is then added to the `$trailer`, which is output as `margin-bottom`. 


###Media queries

To set type at different breakpoints, our `typeset` mixin needs to know about the configured rootsize for that breakpoint. Use the `min-width` mixin with a configured breakpoint key:

```scss
p {
    @include typeset($sans, 16px, 24px, $leader: 0, $trailer: 2);
    
    // we can apply a single breakpoint, starting with breakpoint key: 1
    @include min-width(1) {
        @include typeset($sans, 16px, 24px, $leader: 0, $trailer: 2);
    }

    // or set several keys at once
    @include min-width(2 3 4) {
        @include typeset($sans, 18px, 28px, $leader: 1, $trailer: 3);

        // Feel free to set other styles for these breakpoints here as well.
        padding: 2rem;
    }
}

```

There is also a `max-width` mixin available, but this will always use the smallest breakpoint for it's type calculations, so exercise caution if using this in conjunction with the `typeset` mixin.

Both of these mixins can also accept `px` values for a custom media query shortcut, but these will use the settings from breakpoint `0` so should be avoided when setting type with `typeset`


###Debugging
MegaType contains extensive debugging tools to let you visualise your type and grid setup. This is turned on by default.

```scss
// debug baseline grid
$debug-grid: true;
// debug typeset elements, their cap height, and baseline
$debug-type: true;
// show some information about the current breakpoint and it's config
$debug-breakpoints: true;
```

And these can be toggled on and off all at once in `megatype.scss`:   
```
$debug-allow: true;
```

**Note:** Background gradients are used for some debugging elements. As background gradients suffer from pixel rounding issues these can get out of sync with some configs at long distances (on long typeset pages, for example). This is expected behaviour;


###Extras
A few extra goodies.
- See `_typography.scss` for some functions that can easily return information from your typeface configs.
- An optional color palette config is included, see `_config.scss` and `_map-helpers.scss` for 
- The `typeset` mixin sets some background position for easily replacing ugly default text decoration with background gradients (can be disabled with `$link-underline-support` in `_config.scss`). See `_text-link.scss` for a self-explanitory helper `text-link` mixin.
- See `_toolset_easing.scss` for some handy functions to use in animation easing
- See `_toolset_units.scss` for some handy unit conversion tools

## Roadmap
- Node packages
- Tests
- Default config templates?

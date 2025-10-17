
# Tailwind

+ we cannot use tailwind just using npm
+ we should use vite or postcss


## Install


```bash

```

## JIT compiler

+ JIT (Just-In-Time)
+ JIT is a compiler that works at runtime and compiling code during the execution of a program rather than ahead of time
+ Traditional Compiler ---> AOT (Ahead-of-Time)
+ without JIT compiler all possible utilities generated at build time
+ with JIT compiler only generates the classes you actually use
+ JIT compiler has been the default engine since v3.0.


### Breakpoints

```css
 * sm: min-width: 640px;
 * md: min-width: 768px;
 * lg: min-width: 1024px;
 * xl: min-width: 1280px;
```

### Custome values

```css
w-[123px]
bg-[#1da1f2]
top-[10%]
```

### Layout

| Purpose    | Class                                                                      |
| ---------- | -------------------------------------------------------------------------- |
| Container  | `container`                                                                |
| Display    | `block`, `inline-block`, `inline`, `flex`, `inline-flex`, `grid`, `hidden` |
| Position   | `static`, `relative`, `absolute`, `fixed`, `sticky`                        |
| Z-index    | `z-0`, `z-10`, `z-50`, `z-[999]`                                           |
| Overflow   | `overflow-auto`, `overflow-hidden`, `overflow-x-scroll`                    |
| Object fit | `object-contain`, `object-cover`                                           |

```css
.container           /* fixed-width content to the current breakpoint */

.hidden              /* display: none; */
.block               /* display: block; */
.inline-block        /* display: inline-block; */

.static    /* position: static; */
.fixed     /* position: fixed; */


.inset-0       /* top: 0; right: 0; bottom: 0; left: 0; */
.inset-y-0     /* top: 0; bottom: 0; */
.inset-x-auto  /* left: auto; right: auto; */
.top-0         /* top: 0; */
.top-auto      /* top: auto; */

.z-50    /* z-index: 50; */
.z-auto  /* z-index: auto; */

.visible    /* visibility: visible; */
.invisible  /* visibility: hidden; */

.object-contain     /* object-fit: contain; */
.object-cover       /* object-fit: cover; */
.object-fill        /* object-fit: fill; */

.object-bottom        /* object-position: bottom; */
.object-center        /* object-position: center; */

.overflow-auto       /* overflow: auto; */
.overflow-hidden     /* overflow: hidden; */
```



### Flexbox

| Purpose         | Class                                                  |
| --------------- | ------------------------------------------------------ |
| Flex direction  | `flex-row`, `flex-col`                                 |
| Justify content | `justify-start`, `justify-center`, `justify-between`   |
| Align items     | `items-start`, `items-center`, `items-end`             |
| Flex wrap       | `flex-wrap`, `flex-nowrap`                             |
| Space           | `space-x-4`, `space-y-2`                                |
| Gap             | `gap-2`, `gap-x-4`, `gap-y-4`                           |


```css
.flex                /* display: flex; */
.flex-row          /* flex-direction: row; */
.flex-wrap          /* flex-wrap: wrap; */
.items-center    /* align-items: center; */
.content-center   /* align-content: center; */
.justify-center   /* justify-content: center; */
.self-center   /* align-self: center; */
.flex-1        /* flex: 1 1 0%; */
```


```html
<div class="flex">                  <!-- display: flex -->
<div class="flex flex-row">          <!-- Default: left to right -->
<div class="flex flex-row-reverse">  <!-- Right to left -->
<div class="flex flex-col">          <!-- Top to bottom -->
<div class="flex flex-col-reverse">  <!-- Bottom to top -->
```



### Grid

| Purpose         | Class                                                  |
| --------------- | ------------------------------------------------------ |
| Grid cols       | `grid-cols-1`, `grid-cols-3`, `grid-cols-[200px_auto]` |
| Align grid      | `place-items-center`, `place-content-between`          |
| Gap             | `gap-2`, `gap-x-4`, `gap-y-4`                           |



### Spacing

| Purpose                     | Class                         |
| --------------------------- | ----------------------------- |
| Margin                      | `m-4`, `mx-2`, `mt-1`         |
| Padding                     | `p-4`, `px-2`, `pt-1`         |
| Space between flex children | `space-x-4`, `space-y-2`      |
| Gap (Grid/Flex)             | `gap-2`, `gap-x-4`, `gap-y-4` |


### Sizing
+ Box-sizing : Utilities for controlling how the browser should calculate an element's total size.
+ sizes are predefined: 0,1,2,3, ...,8,10,12,16,20,24,28,32,36,40,44,48,52,56,60,64,72,80,96
+ numbers are not in pixel or rem
+ 1 unit = 4px

| Purpose       | Class                                                     |
| -------       | --------------------------------------------------------- |
| Width         | `w-0`, `w-px`, `w-1/2`, `w-full`, `w-screen`, `w-[300px]` |
| Height        | `h-0`, `h-10`, `h-screen`, `h-[400px]`                    |
| Min/Max       | `min-w-0`, `max-w-xl`, `min-h-screen`                     |
| Box sizing    | `box-border`, `box-content`                               |

```css
.p-1    /* padding: 0.25rem; */         /* 4 pixel */
.m-2      /* margin: 0.5rem; */         /* 8 pixel */
.max-w-xs         /* max-width: 20rem; */
.max-w-sm         /* max-width: 24rem; */
.max-w-md         /* max-width: 28rem; */
.max-w-lg         /* max-width: 32rem; */
```


### Floating

```css
.float-right  /* float: right; */
.float-none   /* float: none; */
.clearfix     /* &::after { content: ""; display: table; clear: both; } */
```

### Colors

| Purpose            | Class                                            |
| ------------------ | ------------------------------------------------ |
| Text color         | `text-red-500`, `text-gray-700`                  |
| Background         | `bg-blue-500`, `bg-gray-100`, `bg-gradient-to-r` |
| Gradient direction | `bg-gradient-to-r`, `bg-gradient-to-b`           |
| From / via / to    | `from-purple-400 via-pink-500 to-red-500`        |
| Opacity            | `bg-opacity-50`, `text-opacity-75`               |


###### Color Families 
https://umeshmk.github.io/Tailwindcss-cheatsheet/

| Group                           | Colors                                        |
| ------------------------------- | --------------------------------------------- |
| ⚪ **Grayscale**                 | `slate`, `gray`, `zinc`, `neutral`, `stone`   |
| 🔴 **Reds / Oranges / Yellows** | `red`, `orange`, `amber`, `yellow`            |
| 🟢 **Greens**                   | `lime`, `green`, `emerald`, `teal`            |
| 🔵 **Blues**                    | `cyan`, `sky`, `blue`, `indigo`               |
| 🟣 **Purples / Pinks**          | `violet`, `purple`, `fuchsia`, `pink`, `rose` |


##### Dark mode

```css
dark:bg-gray-900
dark:text-white
```





### Typography

| Purpose        | Class                                         |
| -------------- | --------------------------------------------- |
| Font size      | `text-xs`, `text-sm`, `text-base`, `text-2xl` |
| Font weight    | `font-light`, `font-medium`, `font-bold`      |
| Text align     | `text-left`, `text-center`, `text-right`      |
| Line height    | `leading-none`, `leading-relaxed`             |
| Letter spacing | `tracking-tight`, `tracking-wide`             |
| Text transform | `uppercase`, `lowercase`, `capitalize`        |


### Borders & Radius

| Purpose                   | Class                                     |
| ------------------------- | ----------------------------------------- |
| Border width              | `border`, `border-2`, `border-t-4`        |
| Border color              | `border-gray-300`, `border-red-500`       |
| Border radius             | `rounded`, `rounded-lg`, `rounded-full`   |
| Divide (between children) | `divide-x`, `divide-y`, `divide-gray-200` |




### Effects

| Purpose    | Class                                             |
| ---------- | ------------------------------------------------- |
| Shadow     | `shadow`, `shadow-md`, `shadow-lg`, `shadow-none` |
| Opacity    | `opacity-0`, `opacity-50`, `opacity-100`          |
| Blur       | `blur-sm`, `blur-md`, `blur-lg`                   |
| Brightness | `brightness-75`, `brightness-125`                 |



### Animation

| Purpose    | Class                                             |
| ---------- | ------------------------------------------------- |
| Scale      | `scale-75`, `scale-100`, `scale-125`              |
| Rotate     | `rotate-45`, `-rotate-90`                         |
| Translate  | `translate-x-2`, `-translate-y-1`                 |
| Transition | `transition`, `duration-300`, `ease-in-out`       |
| Animation  | `animate-bounce`, `animate-spin`, `animate-pulse` |


### Utilities

| Purpose        | Class                                        |
| -------------- | -------------------------------------------- |
| Cursor         | `cursor-pointer`, `cursor-not-allowed`       |
| Select         | `select-none`, `select-text`                 |
| Visibility     | `visible`, `invisible`                       |
| Pointer events | `pointer-events-none`, `pointer-events-auto` |


### States

hover:, focus:, active:, disabled:
Group hover: group hover:group-hover:bg-red-500






























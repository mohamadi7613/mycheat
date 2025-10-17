

## Naming convention

### 1. BEM
+ BEM (Block Element Modifier) 

### 2. SMACSS
+ SMACSS (Scalable and Modular Architecture for CSS)

### 3. OOCSS

### 4. Atomic / Utility-First Naming

+ Each class does one small thing.


## center a text

```css
text-align: center;
align-content: center;
```


## Customizing REM Base

+ `REM`: Represents the font-size of the root element (typically <html>).
+ if you  change the font size of your html element, and the calculation base for rem will change.
+ usually the font-size of browsers are 16px
+ 1.6rem = 16px, 2rem = 20px

```css
html {                   /*  Then text-base (1rem) = 10px.   */
  font-size: 62.5%;     /* 10px base */
  font-size: 10px;     /* fixed size (ignores user/browser preferences) */
}
h1 {                       /*     we can change the size of elements         */
  font-size: 2.4rem;    /* 24px */
}
```

```html
<html style="font-size: 62.5%">
    <div></div>
</html>
```








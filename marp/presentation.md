---
marp: true
# size: 4:3
# theme: gaia
# paginate: true
# class: invert

---
<!-- _backgroundColor: #26d79d -->
<!-- _class: #26d79d -->
![bg](../images/horizontal-lines.svg)
# Marp
## A simple tool to build presentations in Markdown

# ![](images/marp.png)


##### 2019-07-30 by Alessandro Rodi

---

## Marp is a markdown presentation writer, powered by [Electron](http://electron.atom.io/)

# Features

- **Slides are written in Markdown.**
- Cross-platform. Supports Windows, Mac, and Linux
- Live Preview with 3 modes
- Slide themes (`default`, `gaia`) and custom background images
- Supports emoji :heart:
- Render `code` in your slides
- Export your slides to PDF

---

# How to write slides?

Split slides by horizontal ruler `---`. It's very simple.

```md
# Slide 1

foobar

---

# Slide 2

foobar
```

---

## Global Directives

### `theme`

Changes the theme of all the slides in the deck.
```
theme: gaia
```

|Theme name|Value|Directive|
|:-:|:-:|:-|
| ***Default*** | default | `theme: default`
| **Gaia** | gaia |`theme: gaia`

### `size`

Changes slide size by presets.

Presets: `4:3`, `16:9`, `A0`-`A8`, `B0`-`B8` and suffix of `-portrait`.

```html
size: 16:9
```

<!--
$size: a4
Example is here. Global Directive is enabled in anywhere.
It apply the latest value if you write multiple same Global Directives.
-->

---

## Page Directives

The page directive would apply to the  **current page and the following pages**.
You should insert it *at the top* to apply it to all slides.

### `page_number`

<!-- _paginate: true -->

Set `true` to show page number on slides. *See lower right!*


```html
<!-- paginate: true -->
```
---
### `class`

Set to use class of theme.

The `class` directive just enables that using theme supports templates.

```md
class: invert
```

---

### `footer`

Add a footer to the current slide and all of the following slides

```html
<!-- footer: This is a footer -->
```
<!-- _footer: This is a footer -->
Example: Adds "This is a footer" in the bottom of each slide

---

### ```

You can type code as you are used to in Github.
```md
   ```md
    this is this block itself   
```

---
### Emoji

You can type all the emojis you desire
😃🙂🤣👩🏼‍🏫👨🏽‍⚖️

---
<!-- _backgroundColor: #26d79d -->
![bg](../images/horizontal-lines.svg)
#### Slide background Images

You can set an image as a slide background.

```html
<!-- backgroundColor: megabyte-mint -->
![bg](horizontal-lines.png)
```

---

#### Maths Typesetting

Mathematics is typeset using the `KaTeX` package. Use `$` for inline maths, such as $ax^2+bc+c$, and `$$` for block maths:

$$I_{xx}=\int\int_Ry^2f(x,y)\cdot{}dydx$$

```html
This is inline: $ax^2+bx+c$, and this is block:

$$I_{xx}=\int\int_Ry^2f(x,y)\cdot{}dydx$$

```

---

# Why I think is great

* I learned it in zero-time
* Can be pushed to github and maintained as a source file
* I spent zero time in aligning elements on the slides
* The source code is very simple and has **only** relevant content
* I can put code on slides with syntax highlight
* In the end: writing a presentation is much faster, and fun again.


---

<style>
img[alt~="portrait"] {
  position: absolute;
  right: 1cm;
  top: 1cm;
}
</style>

<!-- _backgroundColor: #26d79d -->
![bg](../images/horizontal-lines.svg)
# ![height:5cm drop-shadow portrait](../images/alessandro.jpg)

## Enjoy writing slides! :+1:

### https://marpit.marp.app/
### https://github.com/renuo/lightning-talks
# ![](images/marp.png)

# Thanks!
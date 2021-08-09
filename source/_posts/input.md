---
title: 输入框自动伸长
date: 2021-08-09 11:58:18
categories: Javascript
tags:
    - input
    - textarea
---




CODE
---

html

```html
<h1>Auto-Growing Inputs</h1>

<h2>Inputs</h2>

<p><strong>Problem (doesn't resize horizontally):</strong> I would like <input class="input" value="99" type="number"> hugs.</p>

<p><strong>Solution with span:</strong> I would like <span class="input" role="textbox" contenteditable>99</span> hugs</p>

<p><strong>Solution with JS:</strong> I would like <span class="input-wrap"><span class="width-machine" aria-hidden="true">99</span><input class="input" value="99" type="number"></span> hugs.</p>

<h2>Textareas</h2>

<p><strong>Problem (doesn't resize vertically):</strong> <textarea class="textarea"></textarea></p>

<p><strong>Solution with span:</strong> <span class="textarea" role="textbox" contenteditable></span></p>

<p><strong>Solution with JS (only deals with line breaks): </strong> <textarea class="textarea resize-ta"></textarea></p>
```

css

```css
.input,
.textarea {
  border: 1px solid #ccc;
  font-family: inherit;
  font-size: inherit;
  padding: 1px 6px;
}

.input-wrap {
  position: relative;
}
.input-wrap .input {
  position: absolute;
  width: 100%;
  left: 0;
}
.width-machine {
  /*   Sort of a magic number to add extra space for number spinner */
  padding: 0 1rem;
}

.textarea {
  display: block;
  width: 100%;
  overflow: hidden;
  resize: both;
  min-height: 40px;
  line-height: 20px;
}

.textarea[contenteditable]:empty::before {
  content: "Placeholder still possible";
  color: gray;
}

/* Just for demo */
* {
  box-sizing: border-box;
}
body {
  font-family: "Heebo", sans-serif;
  max-width: 500px;
  margin: 0 auto;
  padding: 1rem;
}

p strong {
  display: block;
}
h1 {
  border-bottom: 5px solid #9ccc65;
}
h2 {
  border-bottom: 2px solid #c5e1a5;
}
```

javascript

```javascript
// Dealing with Input width
let el = document.querySelector(".input-wrap .input");
let widthMachine = document.querySelector(".input-wrap .width-machine");
el.addEventListener("keyup", () => {
  widthMachine.innerHTML = el.value;
});

// Dealing with Textarea Height
function calcHeight(value) {
  let numberOfLineBreaks = (value.match(/\n/g) || []).length;
  // min-height + lines x line-height + padding + border
  let newHeight = 20 + numberOfLineBreaks * 20 + 12 + 2;
  return newHeight;
}

let textarea = document.querySelector(".resize-ta");
textarea.addEventListener("keyup", () => {
  textarea.style.height = calcHeight(textarea.value) + "px";
});
```


# N<sub>E</sub>Obeam
## ✨️A modern take on LaTeX beamer 
### A Marp theme

Find the classic beamer outdated? Are your students losing focus due to it? 💤

I'm not sure how to solve the last one, but this theme is my attempt to address the first issue: too many a new presentation look decades old due to the classic look of beamer.

_[Marp]() is a presentation framework that allows users to create presentations with Markdown and CSS. While this theme relies on Marp to be used, it is not directly affiliated with or sponsored by it._

Inspired by, but not affiliated with or sourced from, the Marp [beam theme made by rnd195](https://github.com/rnd195/my-marp-themes). As mentioned, I have not used any of their code to create this theme.

#### Disclaimer

This theme on purpose uses no copyright-protected works, and should be able to be used for any purpose, but do conduct your own research before doing such. Anything personal-use should be okay.

Consider the [license](LICENSE) of this project as well.

All fonts used in the theme by default are imported from Google Fonts. Consider the privacy aspects of this yourself and choose wheter you want local installs of them or not. This was a choice made to enable direct linking of the theme to work.

---
## 📺 Previews
N<sub>E</sub>Obeam            |  beamer
:-------------------------:|:-------------------------:
![Math](previews/title-slide.png)  |  ![Code](https://www.latextemplates.com/actions/action_fetch_template_image?image=1&template=beamer-presentation)

Image on the right courtesy of [latextemplates.com](https://www.latextemplates.com). Will eventually be replaced by my own.

### Main title
![Main title preview](previews/title-slide.png)
### A slide with text
![Text title preview](previews/text.png)
### Science slides
Math            |  Code
:-------------------------:|:-------------------------:
![Math](previews/math.png)  |  ![Code](previews/code.png)
### Special elements
Images            |  Tables
:-------------------------:|:-------------------------:
![Images](previews/images.png)  |  ![Tables](previews/table.png)
### HTML styling
> This is optionally enabled!

![HTML styling](previews/html.png)

---

## ⌨️ Usage
> ! Until a future timepoint, I won't provide a guide on how to use it with Marp CLI, as I expect CLI users to be technical enough to know how to do this.
### 🛠 Prerequisites
- Git or zip
- Visual Studio Code
- Marp Extension for Visual Studio Code

This font by default uses the fonts Roboto, Roboto Mono (for code) and Noto Sans Math (for math). These are imported in the CSS file, but you can aquire them yourself too, or change them out.

#### 📥 In an existing presentation
1. Download or copy the neobeam.css file into your project OR use the direct link.
2. Add it to your VSCode settings by editing your ``.vscode/settings.json`` file and appending:
```json
  //...
  {
      "markdown.marp.themes": [
          //...
          "path/to/neobeam.css"
          //...
      ]
  }
  //...
```
where ``path/to/neobeam.css`` is either local, for example ``css/neobeam.css`` or the direct link to the repo's CSS file: ``https://raw.githubusercontent.com/mikael-ros/neobeam/main/css/neobeam.css``. Keep in mind that using the direct link might cause your presentation to change over time and it's best you have a local copy instead.

3. Add it to your Marp presentation by adding:
```markdown
marp: true
theme: neobeam
paginate: true
math: katex
footer: '**Author**
         **Title**
         **Conference/course/e.t.c.**'
```

#### ✨️ As a template
Simply clone or fork this repo or download the release. If you wish, you can delete the previews folder.

---

## 🪄 Features & quirks
Most things work like you'd expect, but there are some quirks to keep the cogs turning.
### Title slide
I opted not to always treat the first slide as a title slide, so to declare a slide (any slide actually) a title slide, add the following before the content: ``<!-- _class: title -->``. 

Heres an example of a title slide. As long as you follow as similar format it should work. Theres also an optional logo slot for a University logo or similar.
```html,markdown
<!-- _class: title -->
# Title

## Author

> ### Faculty
> University

## Conference/course/e.t.c.

![logo Logo](path/to/logo)

```
### Headings
Instead of defininig a static header, one gets created by the first H1 (single #) in the slide. 
### Footers
Every page has a footer. It's made from the footer content defined in the beginning of the presentation markdown, and the sections are split by using ** (to create children objects). The footer is a flex-box and I've made it split itself into three sections. You could likely with some modifications change this fairly easily.
### Math
I've opted to use KaTeX, and have not styled for other options yet.
### Definitions
To make LaTeX style bubble definitions I've had to do a slight work around to avoid using HTML in Marp. To define a definition, write the following:
```markdown
    > #### Definition title
    > Definition description
```
The first 3 of these will have unique colors, but after that all of them will have the same. If you wish to add more, change the CSS-file around the ``blockquote:nth-of-type`` part of the stylesheet.
### Image alignment
This stylesheet supports adding a tag in the alt text for ``left``, ``center`` and ``right`` alignment. To define this, you would write something like: ``![left (rest of the alt text)](path/to/image)``. Do note this might cause issues when using these words in your alt text in a normal sentence.
### HTML support
There is light HTML support, including the elements:
- ``<mark>`` (highlighting)
- ``<q>`` (inline quotes)
- ``<var>`` (variables)
- ``<samp>`` (code output)
- ``<audio>`` (audio)
- ``<abbr>`` (abbreviations).

To enable HTML support add or modify the line ``"markdown.marp.enableHtml": true`` in ``.vscode/settings.json``. 

Considering the [security issues mentioned in Marps documentation](https://github.com/marp-team/marp-vscode#enable-html-in-marp-markdown-%EF%B8%8F), this is disabled by default, even in the template. You may need to restart Visual Studio Code after changing the setting. You will need to export in HTML for interactive elements to function.

---

**Feel free to contribute** 💙

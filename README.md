# Robert Schmieder Website

Portfolio website.

## Setup & Workflow

Requires: [node](https://nodejs.org/en)

```
# one time, clone repo
git clone https://github.com/SchmiederDev/ShmeeDev_Homepage.git
cd ShmeeDev_Homepage

# get latest repo updates
git checkout main
git pull

# create branch
git checkout -b task/name-of-todo

# for frontend assets, if using nvm to manage node version (see .nvmrc for current node version)
nvm use

# install frontend packages
npm install

# run local server
npm start

# commit on feature branch
git push -u origin task/name-of-todo

# create Pull Request and merge in Github
```

## Find your way around the repo

The website uses Eleventy ([11ty.dev](https://www.11ty.dev/docs/)) as a static site generator.

### Files and directories

The file structure:
```
|-- src
  |-- _data
    |-- ballanImages.json
    |-- barqueImages.json
    |-- …
  |-- _templates
  |-- assets
  |-- pages
    |-- project-name
      |-- index.njk
      |-- images/
```


- The directories `pages` and `_data` contain all relevant content.
- The directories `assets` and `_templates` contain all layouts, fonts, etc. for building the website.

#### The pages

The folder structure reflects the page structure. Each folder has an `index.njk`, which is compiled to `index.html`.
Each project has an `index.njk` and an `images` folder. 

Each project's index.njk has all the text in HTML. 

The images are placed in the images folder. The paths and sections are defined in `src/_data/projectImages.json`. (For now, better would be if the .json would be co-located with the index.njk).


#### Nunjucks (.njk)

The HTML is written in the templating language Nunjucks ([Mozilla Nunjucks](https://mozilla.github.io/nunjucks/)).

Example file name: `index.njk`.

The pages' content is written in standard HTML. Additional 

### Frontmatter

Frontmatter is aditional data within an HTML/Nunjucks file. It is processed by Eleventy while writing the final HTML files.

It is written at the beginning of a .njk file, beginning and ending with three dashes, `---`.

It adds meta data information that is used for the navigation, the teaser list on the home page, etc.

Example:
```
---
layout: base
title: Shiny new page
homepageorder: 3
---
```

Used Frontmatter properties:
- layout: Uses the base layout, see `src/_templates/layout/`.
- title: Used in head's `<title>`, `<h1>` in overview list, etc.
- permalink: Sets a fixed link to the subpage
- heroImage: used a big banner image on project page and in teaser lists
- eleventyNavigation: relevant for the main navigation

### Layout components used in the project pages

#### Hero / Banner image

```html
<div class="hero full-width">
  {% heroimage heroImage.src, heroImage.alt, "full-width hero__img" %}
  <div class="hero__content">
    <h1>{{ title }}</h1>
  </div>
</div>
```

#### Youtube Video

Replace the attributes src and title:
- `src`: only add the youtube id
- `title`: Usually the title attribute holds the video title. If the videos themselves don't have subtitles, a very concise video description is helpful here. 

```html
<iframe width="900" height="506" src="https://www.youtube-nocookie.com/embed/<youtube-id>" title="ADD A SHORT VIDEO DESCRIPTION" frameborder="0" allow="encrypted-media;" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

```

#### Lightbox
```html
<div class="lightbox">
  <dialog class="lightbox-dialog">
    <button class="lightbox-button-close" autofocus aria-label="Close modal">
      {% include "icons/plain-cross.njk" %}
    </button>
    {% heroimage officerushImages.onepager.src, officerushImages.onepager.alt, "onepager__img-modal" %}
  </dialog>
  <button class="lightbox-button-show" aria-label="Show large image in modal">
  {% image officerushImages.onepager.src, officerushImages.onepager.alt, "onepager__img-button" %}
  </button>
</div>


<!-- at the end of the file -->
<script>
  document.addEventListener( 'DOMContentLoaded', function() {
  // init lightbox
  const lightbox = document.querySelectorAll('.lightbox');
  lightbox.forEach(box => {
    const dialog = box.querySelector(".lightbox-dialog");
    const showButton = box.querySelector(".lightbox-button-show");
    const closeButton = box.querySelector(".lightbox-button-close");
    showButton.addEventListener("click", () => {
      dialog.showModal();
    });
    closeButton.addEventListener("click", () => {
      dialog.close();
    });
  });
  });
</script>
```

#### Image gallery
Important: Each gallery needs a unique identifier. 

```html
<!-- adjust id, and aria-label -->
<section id="splide-<id>" class="splide breakout flow" aria-labelledby="carousel-heading">
  <h2 id="carousel-heading" class="visuallyhidden">Project Images</h2>

  <div class="splide__track">
    <ul class="splide__list">
      {% for item in officerushImages.gallery %}
      <li class="splide__slide">
        {% image item.src, item.alt, "gallery__img" %}
      </li>
      {% endfor %}
    </ul>
  </div>
</section>

<!-- at the end of the file -->
<script>
  // init gallery
  document.addEventListener( 'DOMContentLoaded', function() {
    const galleries = document.querySelectorAll('.splide');
    galleries.forEach(slide => {
      let splide = new Splide(`#${slide.id}`, {
        type  : 'fade',
        rewind: true,
        padding: '3rem',
        gap:'2rem',
        lazyLoad: 'nearby',
      });
      splide.mount();
    });
  });
</script>
```


#### Two-column elements
If the column contains text and/or lists, ass class `.prose` to the column container.

Important: Always put image into container `.two-column__two`. This assures the correct content order in mobile. If the image shall be on the left side on desktop, use  classes `.two-column.two-column--swapped`.

```html
<!-- Image on right side -->
<div class="two-column">
  <div class="two-column__one flow prose">
    <!-- Content: heading and text -->
  </div>
  <div class="two-column__two flow">
    <!-- Media: image or video -->
  </div>
</div>

<!-- Swapped: Image on left side -->
<div class="two-column two-column--swapped">
  <div class="two-column__one flow prose">
    <!-- Content: heading and text -->
  </div>
  <div class="two-column__two flow">
    <!-- Media: image or video -->
  </div>
</div>

```

#### Layout CSS classes

The website uses various css utility classes for layout and styling.
See in file `src/assets/partials/_layout.scss`.

##### Horizontal placement
Without any additional wrapper classes, all content is placed as a single column. The max-width is 80ch (80 characters). See in file `_layout.scss`, lines 62ff, the css selector `.content-grid`.

If content needs to be bigger, use classes `.breakout` or `.full-width` as a wrapper around that content.
- breakout: max-width of 160ch (used e.g. for two-column elements)
- fill-width: all full page width (used e.g. for hero images)

##### Vertical styling

Utility class: `.flow`.
The CSS class is set on the parent element. All direct children receive a vertical spacing. Standard is 1em.

If the spacing between elements should be a bit larger, add the class `.flow-80`.

If blocks contain heading followed by paragraph and/or lists, add special text content styling with the css class `.prose`.

Examples:
```html
<!-- Standard use of .flow -->
<div class="flow"></div>

<!-- Larger spacing -->
<div class="breakout flow flow-80"></div>

<!-- Special text spacing -->
<div class="flow prose"></div>
```


#### Image shortcodes in Nunjucks

The image shortcodes process the images and create the HTML, see `src/_11ty/imageShortcode.js`.

Arguments are given in quotes, comma-separated. Options are: 
- src: file path, either as string (see example of standard image) or as json reference (see example of cardthumbnail).
- alt: alternative text, short description of the image
- customClass: optional

Standard image
```
{% image "src/pages/student-projects/office-rush/images/01_IMG_GP_Office_Rush_95_Title_Screen.png", "Title screen of project shows an empty lila-blue cubicle office", "my-class" %}
```

Hero image, used on the project pages
```
{% heroimage raccoonImages.heroImage.src, "", "full-width hero__img" %}
```

Card thumbnail image, used in the teaser lists, see e.g. `homePageCards.njk`
```
{% cardthumbnail page.heroImage.src, page.heroImage.alt, "card__img" %}
```

### Create a new project page

1. Create page
2. Add to home page

#### Create project

1. Create folder in `src/pages`.
2. Add index.njk.
3. Create folder "images", place all original images here.
4. Create image json file in `src/_data`, add image references.

#### Add project to homepage

Add project to file `src/_data/homepageList.json`.

## Ressources

-   [11ty Eleventy Documentation](https://www.11ty.dev/docs/)
-   [Nunjucks templating](https://mozilla.github.io/nunjucks/templating.html)

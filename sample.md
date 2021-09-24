---
layout: page
title: Basics
description: >
  This chapter covers the basics of content creation with Hydejack.
hide_description: true
sitemap: false
---

This chapter covers the basics of content creation with Hydejack.

* this unordered seed list will be replaced by toc as unordered list
{:toc}


## Adding images
Adding good images is key to a engaging blog experience. You can provide an `image` attribute in in the front matter of posts, pages, and projects* that will be used by Hydejack in a variety of ways, 
such as header image in the `blog` and `post` layout, social media previews, cards in the `gird` and `projects` layout\*, thumbnails in the search dropdown\*, etc.

The `image` attribute will accept an URL to an image, but it is recommended that you provide a `path` / `srcset` hash instead, e.g. 

```yml
image:
  path:    /assets/img/projects/hyde-v2.jpg
  srcset:
    1920w: /assets/img/projects/hyde-v2.jpg
    960w:  /assets/img/projects/hyde-v2@0,5x.jpg
    480w:  /assets/img/projects/hyde-v2@0,25x.jpg
```

Hydejack will show the image in various sizes depending on available screen width so that no specific size will fit all. 
Instead, I recommend using a [mipmap]-like approach, providing the image in multiple sizes, each image half the width of the previous one.
Since Hydejack provides an appropriate [`sizes` attribute][mdn-sizes], the browser can chose the best image from the provided source set.

If you have [ImageMagick] installed, you can use the following commands to create images at 50%, 25%, and 12.5% of the original image. 
Other image tools will provide similar capabilities.

    convert your-image.jpg -resize 50% -sampling-factor 4:2:0 -strip -quality 85 -interlace JPEG -colorspace RGB your-image@0,5x.jpg
    convert your-image.jpg -resize 25% -sampling-factor 4:2:0 -strip -quality 85 -interlace JPEG -colorspace RGB your-image@0,25x.jpg
    convert your-image.jpg -resize 12.5% -sampling-factor 4:2:0 -strip -quality 85 -interlace JPEG -colorspace RGB your-image@0,125x.jpg

For more information on `srcset`, see the [documentation at MDN][mdn-srcset], or [this article from CSS-Tricks][csstricks].

[imagemagick]: https://imagemagick.org/index.php
[mipmap]: https://en.wikipedia.org/wiki/Mipmap
[mdn-srcset]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-srcset
[mdn-sizes]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-sizes
[csstricks]: https://css-tricks.com/responsive-images-youre-just-changing-resolutions-use-srcset/


```yml
# file: `_config.yml`
menu:
  - title: Blog
    url:   /blog/
  - title: Projects
    url:   /projects/
  - title: Resume
    url:   /resume/
  - title: About
    url:   /about/
```

This is now the preferred way of assigning categories in Hydejack, as it makes URLs correspond more naturally to the underlying folder structure.
{:.note}

Type       | URL
-----------|----
Categories | `/jekyll/update/2017-04-07-welcome-to-jekyll/`
Tags       | `/2017-04-07-welcome-to-jekyll/`
{:.scroll-table-small}

You can adjust these in [`_data/string.yml`][strings].

For each featured category or tag, a file called `<category-name>.md` or `<tag-name>.md` has to be created inside the `_featured_tags` and `_featured_categories` folders, respectively. Each file in these folders is part of a [Jekyll Collection](https://jekyllrb.com/docs/collections/).

The meta data of a category or tag is set in the files front matter, e.g.

`layout`
: Must either `list` or `grid`\*

`title`
: Used as title of the page, as well as name of the category or tag as part of the line below a blog post's title. Can be different from the name of the tag or category, as long as `slug` is identical to the name.

Without further configuration, the welcome page will just look like a regular page. However, it can be enhanced through the use of markers:
- To show the two most recent projects, add the `<!--projects-->` marker to the content
- To show the four most recent blog posts, add the `<!--posts-->` marker to the content
- (To show the five most recent blog posts in list form, add the `<!--posts_list-->` marker to the content)


+++
# Hero widget.
widget = "hero"  # See https://sourcethemes.com/academic/docs/page-builder/
headless = true  # This file represents a page section.
active = true  # Activate this widget? true/false
weight = 10  # Order that this section will appear.

title = "waSCC"

# Hero image (optional). Enter filename of an image in the `static/img/` folder.
hero_media = "icon-512.png"

[design.background]
  # Apply a background color, gradient, or image.
  #   Uncomment (by removing `#`) an option to apply it.
  #   Choose a light or dark text color by setting `text_color_light`.
  #   Any HTML color name or Hex value is valid.

  # Background color.
  #color = "white"
  
  # Background gradient.
  #gradient_start = "#4bb4e3"
  #gradient_end = "#2b94c3"
  
  # Background image.
  # image = ""  # Name of image in `static/img/`.
  # image_darken = 0.6  # Darken the image? Range 0-1 where 0 is transparent and 1 is opaque.

  # Text color (true=light or false=dark).
  #text_color_light = false

# Call to action links (optional).
#   Display link(s) by specifying a URL and label below. Icon is optional for `[cta]`.
#   Remove a link/note by deleting a cta/note block.
[cta]
  url = "/tutorials"
  label = "Get Started"
  icon_pack = "fas"
  icon = "download"
  
[cta_alt]
  url = "docs/"
  label = "View Documentation"

# Note. An optional note to show underneath the links.
[cta_note]
  label = '<a class="js-github-release" href="https://github.com/wascc/wascc-host/releases" data-repo="wascc/wascc-host">Latest release<!-- V --></a>'
+++

**A dynamic, elastically scalable WebAssembly host runtime for securely connecting actors and capability providers**

Build your _functions_ and _services_ in WebAssembly and deploy and run them _anywhere_.

<span style="text-shadow: none;"><a class="github-button" href="https://github.com/wascc/wascc-host" data-icon="octicon-star" data-size="large" data-show-count="true" aria-label="Star this on GitHub">Star</a><script async defer src="https://buttons.github.io/buttons.js"></script></span>

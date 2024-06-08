# Material for MkDocs

## Markdown frontmatter

```yaml
title: Used in navigation sidebar
description: Added to meta tag
icon: Rendered as part of sidebar
```

## Markdown features

### Admonitions

Use the following messages, and then indent the text by 4 spaces

-   abstract
-   bug
-   danger
-   example
-   failure
-   info
-   note
-   question
-   quote
-   success
-   tip
-   warning

Usage

-   `!!! note "Custom title"` - Shown by default.
-   `??? note` - Shows a button to show/collapse. Collapsed by default.
-   `???+ note` - Shown by default.

### Annotations

Add markers, that can expand a tooltip. Can be added anywhere in the document.

Usage

```
Some text, (1) some text.
{ .annotate } -> add it to the outermost block

1. Markdown content
```

Usage nested annotation

```
Some text, (1) some text.
{ .annotate }

1. Markdown content (1)
    { .annotate }

    1. Another markdown content
```

Usage with admonitions

```
!!! note annotate "Title (1)"

    Note content (1)

1. Markdown content
```

Usage with content tabs

```
=== "Tab 1"

    Content tab1 content (1)

    1. Markdown content

== "Tab 2"

    Content tab2 content (1)

    1. Another markdown content
```

For cases, where the above syntax is not supported, HTML can be used

```
<div class="annotate" markdown>
Normal markdown content (1)
</div>

1. Markdown content
```

### Buttons

Add these CSS classes

-   `.md-button` - to render link as a button.
-   `.md-button .md-button--primary` - to render a filled, primary button.

Usage

```
[Send :fontawesome-solid-paper-plane:](#){ .md-button }
```

### Code blocks

-   Copy button, added globally. To disable copy button use `{ .yaml .no-copy }`.
-   Add a button to select ranges from the code block using `content.code.select` in yaml.

Usage

-   `py` - view [list](https://pygments.org/docs/lexers/) of all available languages.
-   `py title="bubble_sort.py"` - add title.
-   `py linenums="1"` - to show line numbers, starting from 1.
-   `py hl_lines="2 3 3-5"` - to highlight the given lines. Line numbers start from 1, regardless of `linenums`.
-   `#!python range()` - Use `#!` for inline codeblocks.

To embed code from a file use `--8<--`

````
```title=".browserlistrc"
--8<-- ".browserlistrc"
```
````

### Content tabs

To show different markdown options.

````
=== "C"

    ``` c
    #include <stdio.h>

    int main(void) {
      printf("Hello world!\n");
      return 0;
    }
    ```

=== "C++"

    ``` c++
    #include <iostream>

    int main(void) {
      std::cout << "Hello world!" << std::endl;
      return 0;
    }
    ```
````

### Data tables

```
| Method | Description |
| ------ | ----------- |
| `GET`  | Fetch       |
| `PUT`  | Update      |
```

To read from CSV use

```
{{ read_csv('path_relative_to_mkdocs_yaml.csv') }}
```

Other functions for reading from files

-   read_csv
-   read_fwf
-   read_yaml
-   read_json
-   read_feather
-   read_excel
-   read_raw

### Diagrams

Uses [Mermaid.js](https://mermaid.js.org/).

Use `mermaid` as the language for the code block.

These diagrams are fully-supported

-   flowcharts
-   sequence diagrams
-   state diagrams
-   class diagrams
-   entity-relationship diagrams

Other diagrams provided by Mermaid.js, might not work on mobile.

### Footnotes

The footnote is shown at the end of the markdown file.

### Images

To align images left/right (not center)

-   `![Image title](link){ align=left }` - If there isn't sufficient space to render text next to the image, the image will stretch full width of the viewport.
-   `![Image title](link){ align=right }`

To add captions use this syntax

```
<figure markdown="span">
    ![Image title](link){ width="300" }
    <figcaption>Image caption</figcaption>
</figure>
```

To lazy load images add `{ loading=lazy }`.

To support light/dark mode images, append `#only-light`, `#only-dark` to the image URL.

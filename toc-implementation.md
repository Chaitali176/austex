# Table of Contents (TOC) Implementation Reference

## Overview
This document records the exact changes made to implement a dynamic, side-by-side Table of Contents in the `austex-child` theme. 

Because the theme's NPM/Gulp compilation pipeline is broken due to outdated Node dependencies (specifically `cwebp-bin` failing inside Node v22), modifying the core SCSS (`src/css/_blog.scss`) and JS (`src/js/main.js`) source files is useless as they never compile to `dist/`.

To guarantee that the TOC works immediately across all browsers without requiring build commands, the styling and logic were rewritten fundamentally and injected **inline** into `single.php` at the very bottom, right before `get_footer()`. 

All CSS classes use a specific prefix (`tocs-`) to avoid conflicting with older compiled stylesheets.

## Key Technical Decisions & Changes

### 1. HTML Container Modifications (`single.php`)
The `.content.container` wrapping the main post was transformed:
- Added a wrapper `.tocs-two-col-sep` using CSS Flexbox.
- **Left Column** (`.tocs-left-content`): Contains the mobile TOC unit and the main content (`.tocs-full-data`). Occupies **65%** of the space.
- **Right Column** (`.tocs-right-content`): Contains the desktop TOC unit. Occupies **32%** of the space.

### 2. Inline Style Fixes (`<style>`)
- **Underlines Removed:** Enforced `text-decoration: none !important; border-bottom: none !important; box-shadow: none !important;` on the TOC list items (`.tocs-heading-lists li a`) to brutally override the global site theme styles which were underlining the anchor tags.
- **TOC Scrollbar:** Added `max-height: calc(100vh - 140px); overflow-y: auto;` to the `.tocs-toc-desktop-position` wrapper, creating a styled custom scroll-bar that appears when the TOC links exceed the browser's vertical height.
- **Flex Child Sizing:** The containing wrapper is forced to `align-items: stretch` so the right column inherits the full height of the blog post.

### 3. JavaScript Sticky & Scroll Tracking (`<script>`)
- **Header Generation:** Dynamically loops through all `<h2-h6>` tags localized exclusively within `.tocs-full-data`. It intentionally ignores any manually built "Table of contents" headers so the sidebar doesn't duplicate itself. It assigns each tag a generated ID and populates the sidebar sidebar list.
- **Scroll Spy Highlighter:** An interval listens to `window.on('scroll')` to trace which `<h2>-<h6>` heading the user has scrolled to. The active link gets marked and highlighted in `#F0B334` (Theme Yellow).
- **Position Fixed Tracker:** Originally, CSS `position: sticky` was implemented, but it failed completely on the theme because WordPress themes often carry parent components with `overflow-x: hidden` preventing stickiness. Thus, a robust custom JavaScript event was written tracking the sidebar position. It mathematically identifies the top of the viewport (`120px` offset below the navigation header) to toggle CSS `position: fixed`. Crucially, when the user scrolls past the end of the article, the sidebar applies `position: absolute; bottom: 0;` so it does not awkwardly scroll through the footer.

## Future Maintenance
Do not touch `dist/css/style.css` or `gulpfile.js` when modifying this TOC. 
If modifications are needed to the layout widths, the scroll spy logic, or styling, simply edit the HTML directly beneath the `<script>` and `<style>` blocks in `single.php`!

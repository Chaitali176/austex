# Table of Contents (TOC) Implementation Guide

## Overview
This document outlines the final approach taken to implement a dynamic, scrolling "Table of Contents" sidebar on the `austex-child` theme's single blog posts. 

Because the theme's NPM/Gulp compilation pipeline is currently broken (due to outdated Node dependencies like `cwebp-bin`), any changes made exclusively to the SCSS (`src/css/_blog.scss`) or JS (`src/js/main.js`) source files were not propagating to the live frontend styles.

To ensure the Table of Contents feature works perfectly without requiring a build step, the entire implementation was rewritten natively inside `single.php`.

## Changes Made

### 1. Reverted Source Files
All experimental changes to the source files were completely reverted via `git restore`. No fragmented code was left in:
- `src/css/_blog.scss`
- `src/js/main.js`
- `dist/css/style.css`

### 2. Modified `single.php` HTML Structure
We modified the `.content .container` wrapper inside `single.php` to use a custom flexbox structure. We prefixed all classes with `tocs-` (Table of Contents Sidebar) to ensure no conflicts with any existing CSS.

The layout consists of:
- **`.tocs-two-col-sep`**: The main flex container.
- **`.tocs-left-content` (65% width)**: Contains the mobile TOC and the main article content (`.tocs-full-data`).
- **`.tocs-right-content` (32% width)**: Contains the desktop TOC sidebar.

### 3. Added Inline CSS (`<style>`)
Instead of compiling SCSS, the styling for the TOC was directly appended to the bottom of `single.php` just before `get_footer()`.
- **Responsive Layout:** Flexbox handles side-by-side layout on desktop (`min-width: 992px`) and stacks vertically on mobile.
- **Sticky Sidebar:** Uses `position: sticky; top: 120px;` on the `.tocs-toc-desktop-position` container to ensure the TOC tracks dynamically on the right side of the screen without covering the content.

### 4. Added Inline JavaScript (`<script>`)
The scroll-spy functionality and dynamic header parsing were also added to the bottom of `single.php`.
- Extracts all `<h2>` through `<h6>` tags specifically inside `.tocs-full-data`.
- Generates unique ID jump links.
- Uses viewport offset comparison to apply an `.active` class to the TOC item corresponding to the user's scroll position.
- Uses smooth scrolling for clicking links.

## How to Edit in the Future
If you need to change the style of the Table of Contents, simply edit the `<style>` block at the very bottom of `single.php`. If you need to change how headings are fetched or tracked, edit the `<script>` block below it.

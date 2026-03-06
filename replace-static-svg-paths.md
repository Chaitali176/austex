---
description: Replace static path on svg in template-instant-quote.php
---

# Replace Static SVG Paths in template-instant-quote.php

This agent workflow automates the process of finding and replacing hardcoded static image paths for SVGs in the file `wp-content/themes/austex-child/template-instant-quote.php`.

## Steps to Execute

1. **Locate the target file**  
   Read the file at `wp-content/themes/austex-child/template-instant-quote.php` to identify all image tags containing static `src` attributes pointing to `.svg` files from WP Engine staging paths. Example:  
   `src="https://austexdev.wpengine.com/wp-content/uploads/..."`

2. **Replace Static Paths with Dynamic WordPress Paths**  
   Replace all occurrences of `https://austexdev.wpengine.com/wp-content/uploads/` with a dynamic path prefix `<?php echo site_url('/wp-content/uploads/'); ?>`. Be extremely careful to keep the correct filename (e.g., `2026/03/search-icon.svg`).

   For example, replace:
   ```html
   <img id="search-icon" decoding="async" src="https://austexdev.wpengine.com/wp-content/uploads/2026/03/search-icon.svg" width="20" height="20" alt="search-icon">
   ```
   with:
   ```html
   <img id="search-icon" decoding="async" src="<?php echo site_url('/wp-content/uploads/2026/03/search-icon.svg'); ?>" width="20" height="20" alt="search-icon">
   ```
   *Note: If `template-instant-quote.php` explicitly expects them as part of the theme assets instead of `wp-content/uploads/`, use `<?php echo get_stylesheet_directory_uri(); ?>/images/...` instead and verify their existence with the user. However, since the current path is `uploads/`, `site_url()` is appropriate.*

3. **Verify the Replacements**  
   Review the modified file lines using a code viewer to ensure no PHP syntax errors were introduced and that all instances of the WP Engine URL have been removed from the `.svg` images.

// turbo-all

# Built-in SASS Compiler

## Table of Contents
1. [Introduction to Bootscore's SASS Compiler](#introduction-to-bootscores-sass-compiler)
2. [How It Works](#how-it-works)
3. [SCSS File Structure](#scss-file-structure)
4. [Compilation Process](#compilation-process)
5. [Development Workflow](#development-workflow)
6. [Customizing Bootstrap Variables](#customizing-bootstrap-variables)
7. [Adding Custom SCSS](#adding-custom-scss)
8. [Production vs Development](#production-vs-development)
9. [Troubleshooting](#troubleshooting)
10. [Advanced Techniques](#advanced-techniques)

## Introduction to Bootscore's SASS Compiler

One of Bootscore's most distinctive features is its built-in PHP-based SASS compiler. This eliminates the need for external build tools like Node.js, npm, Gulp, or Webpack, making Bootscore unique among modern WordPress themes.

### Why Built-in SASS Compilation Matters

**Traditional Workflow Problems:**
- Requires Node.js installation and maintenance
- Complex build processes with multiple dependencies
- Version conflicts between different tools
- Steep learning curve for designers
- Server deployment complications

**Bootscore's Solution:**
- Zero external dependencies beyond PHP
- Automatic compilation on file changes
- Simple file-based workflow
- Works on any hosting environment
- No command-line knowledge required

### Key Benefits

1. **Simplicity**: No build tools to install or maintain
2. **Portability**: Works on any PHP-enabled server
3. **Accessibility**: Designers can work without terminal knowledge
4. **Reliability**: No dependency version conflicts
5. **Performance**: Compilation happens only when needed

## How It Works

Bootscore uses the [scssphp](https://github.com/scssphp/scssphp) library, a pure PHP implementation of the SASS compiler.

### Core Components

```php
// Simplified compiler structure
class BootscoreSassCompiler {
    private $compiler;
    private $source_path;
    private $output_path;
    
    public function __construct() {
        $this->compiler = new ScssPhp\ScssPhp\Compiler();
        $this->source_path = get_template_directory() . '/scss/';
        $this->output_path = get_template_directory() . '/';
    }
    
    public function compile() {
        $scss_content = file_get_contents($this->source_path . 'style.scss');
        $compiled_css = $this->compiler->compile($scss_content);
        file_put_contents($this->output_path . 'style.css', $compiled_css);
    }
}
```

### Compilation Triggers

The SASS compiler runs automatically when:

1. **Theme Activation**: Initial compilation of all SCSS files
2. **File Changes**: When SCSS files are modified (in development mode)
3. **Manual Trigger**: Through WordPress admin or custom functions
4. **Version Updates**: When theme version changes

### Compilation Check

```php
function bootscore_check_scss_compilation() {
    $scss_file = get_template_directory() . '/scss/style.scss';
    $css_file = get_template_directory() . '/style.css';
    
    // Check if SCSS file is newer than CSS file
    if (filemtime($scss_file) > filemtime($css_file)) {
        bootscore_compile_scss();
    }
}
add_action('wp_enqueue_scripts', 'bootscore_check_scss_compilation');
```

## SCSS File Structure

Bootscore organizes SCSS files in a logical, maintainable structure:

```
scss/
├── style.scss                 # Main entry point
├── _variables.scss             # Custom variables
├── _custom.scss                # Custom styles
├── _mixins.scss                # Custom mixins
├── bootstrap/                  # Bootstrap source
│   ├── scss/
│   │   ├── _variables.scss     # Bootstrap variables
│   │   ├── _mixins.scss        # Bootstrap mixins
│   │   ├── _functions.scss     # Bootstrap functions
│   │   ├── _root.scss          # CSS custom properties
│   │   ├── _reboot.scss        # Normalize styles
│   │   ├── _grid.scss          # Grid system
│   │   ├── _utilities.scss     # Utility classes
│   │   └── bootstrap.scss      # Bootstrap main file
│   └── ...
├── components/                 # Custom components
│   ├── _buttons.scss           # Button customizations
│   ├── _navigation.scss        # Navigation styles
│   ├── _cards.scss             # Card customizations
│   └── _forms.scss             # Form styles
├── layout/                     # Layout-specific styles
│   ├── _header.scss            # Header styles
│   ├── _footer.scss            # Footer styles
│   ├── _sidebar.scss           # Sidebar styles
│   └── _content.scss           # Content area styles
├── pages/                      # Page-specific styles
│   ├── _home.scss              # Homepage styles
│   ├── _blog.scss              # Blog page styles
│   └── _contact.scss           # Contact page styles
└── vendor/                     # Third-party SCSS
    ├── _fontawesome.scss       # Font Awesome
    └── _plugins.scss           # Plugin-specific styles
```

### Main SCSS Entry Point

```scss
// style.scss - Main compilation entry point
/*
Theme Name: Bootscore
Description: Bootstrap WordPress Starter Theme
Version: 6.2.2
*/

// Import custom variables first (to override Bootstrap defaults)
@import "variables";

// Import Bootstrap
@import "bootstrap/scss/bootstrap";

// Import custom components and layouts
@import "mixins";
@import "custom";

// Import component-specific styles
@import "components/buttons";
@import "components/navigation";
@import "components/cards";
@import "components/forms";

// Import layout styles
@import "layout/header";
@import "layout/footer";
@import "layout/sidebar";
@import "layout/content";

// Import page-specific styles
@import "pages/home";
@import "pages/blog";
@import "pages/contact";

// Import vendor styles
@import "vendor/fontawesome";
@import "vendor/plugins";
```

## Compilation Process

### Step-by-Step Compilation

1. **File Detection**: Check for SCSS file modifications
2. **Variable Loading**: Load custom variables and Bootstrap variables
3. **Import Resolution**: Resolve all @import statements
4. **Compilation**: Convert SCSS to CSS
5. **Optimization**: Minify and optimize output (in production)
6. **Output**: Write compiled CSS to style.css

### Compilation Example

```php
function bootscore_compile_scss() {
    try {
        // Initialize compiler
        $compiler = new ScssPhp\ScssPhp\Compiler();
        
        // Set compilation mode
        if (WP_DEBUG) {
            $compiler->setOutputStyle(OutputStyle::EXPANDED);
        } else {
            $compiler->setOutputStyle(OutputStyle::COMPRESSED);
        }
        
        // Set import paths
        $compiler->setImportPaths([
            get_template_directory() . '/scss/',
            get_template_directory() . '/scss/bootstrap/',
        ]);
        
        // Read main SCSS file
        $scss_content = file_get_contents(get_template_directory() . '/scss/style.scss');
        
        // Compile SCSS to CSS
        $compiled_css = $compiler->compile($scss_content);
        
        // Add WordPress theme header
        $css_header = "/*\nTheme Name: Bootscore\nCompiled: " . date('Y-m-d H:i:s') . "\n*/\n\n";
        $final_css = $css_header . $compiled_css;
        
        // Write to style.css
        file_put_contents(get_template_directory() . '/style.css', $final_css);
        
        // Update compilation timestamp
        update_option('bootscore_scss_compiled', time());
        
    } catch (Exception $e) {
        // Log compilation error
        error_log('Bootscore SCSS Compilation Error: ' . $e->getMessage());
    }
}
```

## Development Workflow

### Basic Development Process

1. **Edit SCSS Files**: Modify files in the `/scss/` directory
2. **Automatic Compilation**: Changes trigger recompilation
3. **Browser Refresh**: See changes immediately
4. **No Build Commands**: No terminal or command-line tools needed

### Live Development

```php
// Enable live SCSS compilation in development
function bootscore_enable_live_compilation() {
    if (WP_DEBUG) {
        add_action('wp_head', function() {
            echo '<script>
                // Auto-refresh when CSS changes
                setInterval(function() {
                    var links = document.getElementsByTagName("link");
                    for (var i = 0; i < links.length; i++) {
                        if (links[i].rel === "stylesheet") {
                            links[i].href = links[i].href.split("?")[0] + "?v=" + new Date().getTime();
                        }
                    }
                }, 1000);
            </script>';
        });
    }
}
add_action('init', 'bootscore_enable_live_compilation');
```

### File Watching

```php
// Watch for SCSS file changes
function bootscore_watch_scss_files() {
    if (!WP_DEBUG) return;
    
    $scss_files = glob(get_template_directory() . '/scss/**/*.scss');
    $last_compile = get_option('bootscore_scss_compiled', 0);
    
    foreach ($scss_files as $file) {
        if (filemtime($file) > $last_compile) {
            bootscore_compile_scss();
            break;
        }
    }
}
add_action('wp_loaded', 'bootscore_watch_scss_files');
```

## Customizing Bootstrap Variables

### Variable Override System

Bootstrap variables can be overridden before importing Bootstrap:

```scss
// _variables.scss - Custom Bootstrap variable overrides

// Color system
$primary: #007bff !default;
$secondary: #6c757d !default;
$success: #28a745 !default;
$info: #17a2b8 !default;
$warning: #ffc107 !default;
$danger: #dc3545 !default;
$light: #f8f9fa !default;
$dark: #343a40 !default;

// Brand colors
$brand-primary: #your-brand-color;
$brand-secondary: #your-secondary-color;

// Typography
$font-family-sans-serif: "Helvetica Neue", Arial, sans-serif !default;
$font-family-monospace: "Monaco", "Consolas", monospace !default;
$font-size-base: 1rem !default;
$font-weight-base: 400 !default;
$line-height-base: 1.5 !default;

// Grid breakpoints
$grid-breakpoints: (
  xs: 0,
  sm: 576px,
  md: 768px,
  lg: 992px,
  xl: 1200px,
  xxl: 1400px
) !default;

// Container max widths
$container-max-widths: (
  sm: 540px,
  md: 720px,
  lg: 960px,
  xl: 1140px,
  xxl: 1320px
) !default;

// Component sizes
$border-radius: 0.375rem !default;
$border-radius-sm: 0.25rem !default;
$border-radius-lg: 0.5rem !default;

// Spacing
$spacer: 1rem !default;
$spacers: (
  0: 0,
  1: $spacer * 0.25,
  2: $spacer * 0.5,
  3: $spacer,
  4: $spacer * 1.5,
  5: $spacer * 3,
) !default;
```

### Advanced Variable Usage

```scss
// Create custom color schemes
$custom-colors: (
  "brand": $brand-primary,
  "accent": #ff6b35,
  "neutral": #f5f5f5
);

// Merge with Bootstrap colors
$theme-colors: map-merge($theme-colors, $custom-colors);

// Custom button variants
$btn-border-radius: $border-radius-lg;
$btn-font-weight: 600;

// Custom navbar settings
$navbar-brand-font-size: 1.5rem;
$navbar-brand-font-weight: 700;
$navbar-nav-link-padding-x: 1rem;
```

## Adding Custom SCSS

### Component-Based Organization

```scss
// components/_buttons.scss - Custom button styles

.btn-custom {
  background: linear-gradient(45deg, $primary, $secondary);
  border: none;
  color: white;
  transition: all 0.3s ease;
  
  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 8px rgba(0,0,0,0.2);
  }
  
  &.btn-lg {
    padding: 1rem 2rem;
    font-size: 1.2rem;
  }
}

// WordPress-specific button styles
.wp-block-button {
  .wp-block-button__link {
    @extend .btn;
    @extend .btn-primary;
  }
  
  &.is-style-outline {
    .wp-block-button__link {
      @extend .btn-outline-primary;
    }
  }
}
```

### Layout-Specific Styles

```scss
// layout/_header.scss - Header customizations

.site-header {
  background: linear-gradient(135deg, $primary 0%, $secondary 100%);
  position: sticky;
  top: 0;
  z-index: 1030;
  
  .navbar-brand {
    font-weight: 700;
    font-size: 1.5rem;
    
    img {
      max-height: 40px;
      width: auto;
    }
  }
  
  .navbar-nav {
    .nav-link {
      color: rgba(255, 255, 255, 0.9);
      font-weight: 500;
      
      &:hover {
        color: white;
      }
    }
  }
}

// Responsive header adjustments
@include media-breakpoint-down(lg) {
  .site-header {
    .navbar-brand {
      font-size: 1.25rem;
    }
    
    .navbar-collapse {
      background: rgba(0, 0, 0, 0.1);
      margin-top: 1rem;
      border-radius: $border-radius;
      padding: 1rem;
    }
  }
}
```

### Utility Classes

```scss
// _custom.scss - Custom utility classes

// Spacing utilities
.py-6 { padding-top: 4rem; padding-bottom: 4rem; }
.py-7 { padding-top: 5rem; padding-bottom: 5rem; }
.py-8 { padding-top: 6rem; padding-bottom: 6rem; }

// Background utilities
.bg-gradient-primary {
  background: linear-gradient(45deg, $primary, lighten($primary, 10%));
}

.bg-pattern {
  background-image: url('data:image/svg+xml,<svg...>');
  background-repeat: repeat;
}

// Animation utilities
.fade-in {
  animation: fadeIn 0.5s ease-in;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}

// WordPress-specific utilities
.wp-block-image {
  img {
    @extend .img-fluid;
  }
  
  &.is-style-rounded {
    img {
      border-radius: $border-radius-lg;
    }
  }
}
```

## Production vs Development

### Development Mode

```scss
// Development settings in style.scss
@if $development-mode {
  // Expanded output for debugging
  $enable-rounded: true;
  $enable-shadows: true;
  $enable-gradients: true;
  
  // Debug information
  body::after {
    content: "Development Mode - SCSS Compiled: #{$compile-time}";
    position: fixed;
    bottom: 0;
    right: 0;
    background: rgba(0,0,0,0.8);
    color: white;
    padding: 0.5rem;
    font-size: 0.75rem;
    z-index: 9999;
  }
}
```

### Production Optimization

```php
// Production compilation settings
function bootscore_production_compile() {
    $compiler = new ScssPhp\ScssPhp\Compiler();
    
    // Set production mode
    $compiler->setOutputStyle(OutputStyle::COMPRESSED);
    
    // Enable optimizations
    $compiler->setSourceMap(false);
    $compiler->setLineNumberStyle(false);
    
    // Add production variables
    $compiler->setVariables([
        'production-mode' => true,
        'debug-mode' => false
    ]);
    
    return $compiler;
}
```

### Conditional Compilation

```scss
// Conditional styles based on environment
@if $production-mode {
  // Remove debug styles
  .debug-info { display: none; }
  
  // Optimize animations
  * {
    animation-duration: 0.2s !important;
    transition-duration: 0.2s !important;
  }
} @else {
  // Development styles
  .debug-info {
    position: fixed;
    top: 0;
    left: 0;
    background: red;
    color: white;
    padding: 0.5rem;
    z-index: 10000;
  }
}
```

## Troubleshooting

### Common Issues and Solutions

#### 1. Compilation Errors

**Problem**: SCSS syntax errors prevent compilation

```php
// Error handling and reporting
function bootscore_handle_scss_error($error) {
    if (WP_DEBUG) {
        echo '<div style="background: red; color: white; padding: 1rem; margin: 1rem;">';
        echo '<strong>SCSS Compilation Error:</strong><br>';
        echo esc_html($error->getMessage());
        echo '</div>';
    }
    
    // Log error for debugging
    error_log('Bootscore SCSS Error: ' . $error->getMessage());
}
```

**Solution**: Validate SCSS syntax and check import paths

#### 2. Performance Issues

**Problem**: Compilation takes too long or happens too frequently

```php
// Implement compilation caching
function bootscore_cached_compile() {
    $cache_key = 'bootscore_scss_' . md5_file(get_template_directory() . '/scss/style.scss');
    $cached_css = get_transient($cache_key);
    
    if ($cached_css === false) {
        $cached_css = bootscore_compile_scss();
        set_transient($cache_key, $cached_css, HOUR_IN_SECONDS);
    }
    
    return $cached_css;
}
```

#### 3. Import Path Issues

**Problem**: SCSS files not found during import

```php
// Set correct import paths
function bootscore_set_import_paths($compiler) {
    $paths = [
        get_template_directory() . '/scss/',
        get_template_directory() . '/scss/bootstrap/',
        get_template_directory() . '/scss/components/',
        get_template_directory() . '/scss/layout/',
    ];
    
    $compiler->setImportPaths($paths);
}
```

### Debug Tools

```php
// SCSS compilation debug tool
function bootscore_scss_debug() {
    if (!current_user_can('manage_options')) return;
    
    echo '<div class="wrap">';
    echo '<h1>Bootscore SCSS Debug</h1>';
    
    // Show last compilation time
    $last_compile = get_option('bootscore_scss_compiled');
    echo '<p>Last compiled: ' . date('Y-m-d H:i:s', $last_compile) . '</p>';
    
    // Show SCSS file status
    $scss_files = glob(get_template_directory() . '/scss/**/*.scss');
    echo '<h2>SCSS Files</h2><ul>';
    foreach ($scss_files as $file) {
        $relative_path = str_replace(get_template_directory(), '', $file);
        $modified = date('Y-m-d H:i:s', filemtime($file));
        echo '<li>' . $relative_path . ' (modified: ' . $modified . ')</li>';
    }
    echo '</ul>';
    
    // Manual compilation button
    if (isset($_POST['compile_scss'])) {
        bootscore_compile_scss();
        echo '<div class="notice notice-success"><p>SCSS compiled successfully!</p></div>';
    }
    
    echo '<form method="post"><input type="submit" name="compile_scss" value="Compile SCSS" class="button button-primary"></form>';
    echo '</div>';
}

// Add to WordPress admin
function bootscore_add_scss_debug_page() {
    add_theme_page(
        'SCSS Debug',
        'SCSS Debug',
        'manage_options',
        'bootscore-scss-debug',
        'bootscore_scss_debug'
    );
}
add_action('admin_menu', 'bootscore_add_scss_debug_page');
```

## Advanced Techniques

### Custom Functions and Mixins

```scss
// _mixins.scss - Custom SCSS functions and mixins

// Color manipulation functions
@function lighten-color($color, $amount: 10%) {
  @return lighten($color, $amount);
}

@function darken-color($color, $amount: 10%) {
  @return darken($color, $amount);
}

// Responsive breakpoint mixins
@mixin respond-above($breakpoint) {
  @media (min-width: map-get($grid-breakpoints, $breakpoint)) {
    @content;
  }
}

@mixin respond-below($breakpoint) {
  @media (max-width: map-get($grid-breakpoints, $breakpoint) - 1px) {
    @content;
  }
}

// Animation mixins
@mixin smooth-transition($properties: all, $duration: 0.3s, $easing: ease) {
  transition: $properties $duration $easing;
}

@mixin hover-lift($lift: 5px, $shadow: true) {
  @include smooth-transition(transform, 0.2s);
  
  &:hover {
    transform: translateY(-$lift);
    
    @if $shadow {
      box-shadow: 0 $lift * 2 $lift * 4 rgba(0, 0, 0, 0.1);
    }
  }
}

// WordPress-specific mixins
@mixin wp-block($block-name) {
  .wp-block-#{$block-name} {
    @content;
  }
}

// Usage examples
.custom-card {
  @include hover-lift(3px, true);
  @include smooth-transition();
}

@include wp-block('paragraph') {
  font-size: 1.1rem;
  line-height: 1.6;
}
```

### Dynamic SCSS Generation

```php
// Generate SCSS variables from WordPress customizer
function bootscore_generate_dynamic_scss() {
    $primary_color = get_theme_mod('primary_color', '#007bff');
    $secondary_color = get_theme_mod('secondary_color', '#6c757d');
    $font_family = get_theme_mod('font_family', 'Arial, sans-serif');
    
    $dynamic_scss = "
        // Generated from WordPress Customizer
        \$primary: {$primary_color};
        \$secondary: {$secondary_color};
        \$font-family-sans-serif: '{$font_family}', Arial, sans-serif;
        
        // Generated " . date('Y-m-d H:i:s') . "
    ";
    
    file_put_contents(
        get_template_directory() . '/scss/_dynamic.scss',
        $dynamic_scss
    );
}
add_action('customize_save_after', 'bootscore_generate_dynamic_scss');
```

### SCSS Hot Reloading

```php
// Implement hot reloading for development
function bootscore_hot_reload() {
    if (!WP_DEBUG) return;
    
    wp_enqueue_script('bootscore-hot-reload', '', [], null, true);
    wp_add_inline_script('bootscore-hot-reload', '
        // Check for SCSS changes every 2 seconds
        setInterval(function() {
            fetch("' . admin_url('admin-ajax.php') . '?action=check_scss_changes")
                .then(response => response.json())
                .then(data => {
                    if (data.changed) {
                        // Reload stylesheets
                        document.querySelectorAll("link[rel=stylesheet]").forEach(link => {
                            link.href = link.href.split("?")[0] + "?v=" + Date.now();
                        });
                    }
                });
        }, 2000);
    ');
}
add_action('wp_enqueue_scripts', 'bootscore_hot_reload');
```

---

*This document provides comprehensive coverage of Bootscore's built-in SASS compiler. The next document will explore WooCommerce integration and e-commerce features.*
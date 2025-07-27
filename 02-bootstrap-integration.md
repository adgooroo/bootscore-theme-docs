# Bootstrap Integration with WordPress

## Table of Contents
1. [Introduction to Bootstrap](#introduction-to-bootstrap)
2. [Bootstrap Architecture](#bootstrap-architecture)
3. [Bootstrap Grid System](#bootstrap-grid-system)
4. [Bootstrap Components](#bootstrap-components)
5. [Bootstrap Utilities](#bootstrap-utilities)
6. [Integrating Bootstrap into WordPress](#integrating-bootstrap-into-wordpress)
7. [Bootstrap-Enhanced WordPress Templates](#bootstrap-enhanced-wordpress-templates)
8. [Responsive Design with Bootstrap](#responsive-design-with-bootstrap)
9. [Customizing Bootstrap](#customizing-bootstrap)
10. [Best Practices](#best-practices)

## Introduction to Bootstrap

Bootstrap is the world's most popular CSS framework for building responsive, mobile-first websites. Originally developed by Twitter, Bootstrap provides a comprehensive collection of pre-built components, utilities, and a flexible grid system that dramatically speeds up frontend development.

### Key Benefits for WordPress

- **Rapid Development**: Pre-built components reduce development time
- **Responsive Design**: Mobile-first approach ensures cross-device compatibility
- **Consistency**: Standardized design patterns and components
- **Customizable**: Extensive customization options through SASS variables
- **Community Support**: Large ecosystem and extensive documentation
- **Accessibility**: Built-in accessibility features and ARIA support

### Bootstrap Repository Reference

The official Bootstrap repository ([twbs/bootstrap](https://github.com/twbs/bootstrap)) contains the complete source code, documentation, and examples for implementing Bootstrap in your projects.

## Bootstrap Architecture

Bootstrap follows a modular architecture that makes it easy to include only the components you need:

### Core Structure

```
bootstrap/
├── scss/
│   ├── _variables.scss     # Configuration variables
│   ├── _mixins.scss        # Reusable mixins
│   ├── _root.scss          # CSS custom properties
│   ├── _reboot.scss        # Cross-browser normalization
│   ├── _grid.scss          # Grid system
│   ├── _utilities.scss     # Utility classes
│   └── _components/        # Individual components
├── js/
│   ├── dist/               # Compiled JavaScript
│   └── src/                # Source JavaScript modules
└── dist/
    ├── css/                # Compiled CSS files
    └── js/                 # Compiled JavaScript files
```

### Bootstrap Components

Bootstrap includes dozens of reusable components:

- **Layout**: Grid, containers, breakpoints
- **Content**: Typography, images, tables, figures
- **Forms**: Input groups, validation, layouts
- **Components**: Alerts, badges, buttons, cards, modals, navigation
- **Utilities**: Spacing, colors, display, position, sizing

## Bootstrap Grid System

Bootstrap's grid system is built with flexbox and allows up to 12 columns across the page.

### Container Types

```html
<!-- Fixed-width container -->
<div class="container">
  <!-- Content -->
</div>

<!-- Full-width container -->
<div class="container-fluid">
  <!-- Content -->
</div>

<!-- Responsive container -->
<div class="container-sm">
  <!-- 100% wide until small breakpoint -->
</div>
```

### Grid Basics

```html
<div class="container">
  <div class="row">
    <div class="col-md-8">
      <h1>Main Content</h1>
      <p>This column takes 8/12 of the width on medium screens and up.</p>
    </div>
    <div class="col-md-4">
      <h2>Sidebar</h2>
      <p>This column takes 4/12 of the width on medium screens and up.</p>
    </div>
  </div>
</div>
```

### Responsive Breakpoints

Bootstrap includes six default breakpoints:

```scss
// Extra small devices (portrait phones, less than 576px)
// No media query for `xs` since this is the default

// Small devices (landscape phones, 576px and up)
@media (min-width: 576px) { ... }

// Medium devices (tablets, 768px and up)
@media (min-width: 768px) { ... }

// Large devices (desktops, 992px and up)
@media (min-width: 992px) { ... }

// X-Large devices (large desktops, 1200px and up)
@media (min-width: 1200px) { ... }

// XX-Large devices (larger desktops, 1400px and up)
@media (min-width: 1400px) { ... }
```

## Bootstrap Components

### Cards

Cards are flexible content containers:

```html
<div class="card">
  <img src="..." class="card-img-top" alt="...">
  <div class="card-body">
    <h5 class="card-title">Card title</h5>
    <p class="card-text">Some quick example text to build on the card title.</p>
    <a href="#" class="btn btn-primary">Go somewhere</a>
  </div>
</div>
```

### Navigation

Bootstrap provides several navigation components:

```html
<!-- Basic navbar -->
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">Navbar</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav">
        <li class="nav-item">
          <a class="nav-link active" href="#">Home</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#">About</a>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

### Buttons

Bootstrap includes several predefined button styles:

```html
<button type="button" class="btn btn-primary">Primary</button>
<button type="button" class="btn btn-secondary">Secondary</button>
<button type="button" class="btn btn-success">Success</button>
<button type="button" class="btn btn-danger">Danger</button>
<button type="button" class="btn btn-warning">Warning</button>
<button type="button" class="btn btn-info">Info</button>
<button type="button" class="btn btn-light">Light</button>
<button type="button" class="btn btn-dark">Dark</button>
```

## Bootstrap Utilities

Bootstrap includes hundreds of utility classes for common styling needs:

### Spacing

```html
<!-- Margin -->
<div class="m-4">Margin on all sides</div>
<div class="mt-3">Margin top</div>
<div class="mx-auto">Horizontal margin auto</div>

<!-- Padding -->
<div class="p-3">Padding on all sides</div>
<div class="py-4">Padding top and bottom</div>
```

### Display

```html
<div class="d-none">Hidden element</div>
<div class="d-block">Block element</div>
<div class="d-flex">Flex container</div>
<div class="d-grid">Grid container</div>
```

### Text Utilities

```html
<p class="text-start">Left aligned text</p>
<p class="text-center">Center aligned text</p>
<p class="text-end">Right aligned text</p>
<p class="text-primary">Primary color text</p>
<p class="fw-bold">Bold text</p>
```

## Integrating Bootstrap into WordPress

### Method 1: CDN Integration

The quickest way to add Bootstrap to a WordPress theme:

```php
function enqueue_bootstrap_cdn() {
    // Bootstrap CSS
    wp_enqueue_style(
        'bootstrap-css',
        'https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css',
        array(),
        '5.3.0'
    );
    
    // Bootstrap JavaScript
    wp_enqueue_script(
        'bootstrap-js',
        'https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js',
        array(),
        '5.3.0',
        true
    );
}
add_action('wp_enqueue_scripts', 'enqueue_bootstrap_cdn');
```

### Method 2: Local Bootstrap Files

Download and include Bootstrap files locally:

```php
function enqueue_bootstrap_local() {
    // Bootstrap CSS
    wp_enqueue_style(
        'bootstrap-css',
        get_template_directory_uri() . '/assets/css/bootstrap.min.css',
        array(),
        '5.3.0'
    );
    
    // Bootstrap JavaScript
    wp_enqueue_script(
        'bootstrap-js',
        get_template_directory_uri() . '/assets/js/bootstrap.bundle.min.js',
        array(),
        '5.3.0',
        true
    );
    
    // Theme styles (should come after Bootstrap)
    wp_enqueue_style(
        'theme-style',
        get_stylesheet_uri(),
        array('bootstrap-css'),
        '1.0.0'
    );
}
add_action('wp_enqueue_scripts', 'enqueue_bootstrap_local');
```

### Method 3: NPM and Build Process

For advanced customization, use NPM to install Bootstrap:

```bash
npm install bootstrap
```

```scss
// Import Bootstrap
@import "../../node_modules/bootstrap/scss/bootstrap";

// Custom styles
.my-custom-class {
  // Your styles here
}
```

## Bootstrap-Enhanced WordPress Templates

### Header Template with Bootstrap Navbar

```php
<!DOCTYPE html>
<html <?php language_attributes(); ?>>
<head>
    <meta charset="<?php bloginfo('charset'); ?>">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <?php wp_head(); ?>
</head>
<body <?php body_class(); ?>>

<nav class="navbar navbar-expand-lg navbar-dark bg-primary">
    <div class="container">
        <?php if (has_custom_logo()) : ?>
            <div class="navbar-brand">
                <?php the_custom_logo(); ?>
            </div>
        <?php else : ?>
            <a class="navbar-brand" href="<?php echo esc_url(home_url('/')); ?>">
                <?php bloginfo('name'); ?>
            </a>
        <?php endif; ?>
        
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        
        <div class="collapse navbar-collapse" id="navbarNav">
            <?php
            wp_nav_menu(array(
                'theme_location' => 'primary',
                'menu_class'     => 'navbar-nav ms-auto',
                'container'      => false,
                'walker'         => new Bootstrap_Nav_Walker(),
            ));
            ?>
        </div>
    </div>
</nav>
```

### Bootstrap WordPress Navigation Walker

```php
class Bootstrap_Nav_Walker extends Walker_Nav_Menu {
    
    function start_lvl(&$output, $depth = 0, $args = null) {
        $indent = str_repeat("\t", $depth);
        $output .= "\n$indent<ul class=\"dropdown-menu\">\n";
    }
    
    function start_el(&$output, $item, $depth = 0, $args = null, $id = 0) {
        $classes = empty($item->classes) ? array() : (array) $item->classes;
        $classes[] = 'nav-item';
        
        if (in_array('menu-item-has-children', $classes)) {
            $classes[] = 'dropdown';
        }
        
        $class_names = join(' ', apply_filters('nav_menu_css_class', array_filter($classes), $item, $args));
        $class_names = $class_names ? ' class="' . esc_attr($class_names) . '"' : '';
        
        $output .= '<li' . $class_names .'>';
        
        $link_class = ($depth > 0) ? 'dropdown-item' : 'nav-link';
        if (in_array('menu-item-has-children', $classes)) {
            $link_class .= ' dropdown-toggle';
        }
        
        $attributes = !empty($item->attr_title) ? ' title="'  . esc_attr($item->attr_title) .'"' : '';
        $attributes .= !empty($item->target) ? ' target="' . esc_attr($item->target) .'"' : '';
        $attributes .= !empty($item->xfn) ? ' rel="' . esc_attr($item->xfn) .'"' : '';
        $attributes .= !empty($item->url) ? ' href="' . esc_attr($item->url) .'"' : '';
        $attributes .= ' class="' . $link_class . '"';
        
        if (in_array('menu-item-has-children', $classes)) {
            $attributes .= ' data-bs-toggle="dropdown"';
        }
        
        $item_output = isset($args->before) ? $args->before : '';
        $item_output .= '<a' . $attributes .'>';
        $item_output .= (isset($args->link_before) ? $args->link_before : '') . apply_filters('the_title', $item->title, $item->ID) . (isset($args->link_after) ? $args->link_after : '');
        $item_output .= '</a>';
        $item_output .= isset($args->after) ? $args->after : '';
        
        $output .= apply_filters('walker_nav_menu_start_el', $item_output, $item, $depth, $args);
    }
}
```

### Bootstrap Card Layout for Posts

```php
<div class="container my-5">
    <div class="row">
        <?php if (have_posts()) : ?>
            <?php while (have_posts()) : the_post(); ?>
                <div class="col-md-6 col-lg-4 mb-4">
                    <div class="card h-100">
                        <?php if (has_post_thumbnail()) : ?>
                            <a href="<?php the_permalink(); ?>">
                                <?php the_post_thumbnail('medium', array('class' => 'card-img-top')); ?>
                            </a>
                        <?php endif; ?>
                        
                        <div class="card-body d-flex flex-column">
                            <h5 class="card-title">
                                <a href="<?php the_permalink(); ?>" class="text-decoration-none">
                                    <?php the_title(); ?>
                                </a>
                            </h5>
                            
                            <p class="card-text"><?php the_excerpt(); ?></p>
                            
                            <div class="mt-auto">
                                <a href="<?php the_permalink(); ?>" class="btn btn-primary">
                                    <?php _e('Read More', 'textdomain'); ?>
                                </a>
                            </div>
                        </div>
                        
                        <div class="card-footer bg-transparent">
                            <small class="text-muted">
                                <?php echo get_the_date(); ?> | 
                                <?php _e('By', 'textdomain'); ?> <?php the_author(); ?>
                            </small>
                        </div>
                    </div>
                </div>
            <?php endwhile; ?>
        <?php endif; ?>
    </div>
</div>
```

## Responsive Design with Bootstrap

### Mobile-First Approach

Bootstrap uses a mobile-first approach, meaning styles are first applied to mobile devices and then scaled up:

```html
<!-- Stack vertically on mobile, side by side on larger screens -->
<div class="row">
    <div class="col-12 col-md-8">
        <h1>Main Content</h1>
        <p>This content will be full width on mobile and 8/12 width on medium screens and up.</p>
    </div>
    <div class="col-12 col-md-4">
        <h2>Sidebar</h2>
        <p>This sidebar will be full width on mobile and 4/12 width on medium screens and up.</p>
    </div>
</div>
```

### Responsive Images

```html
<!-- Responsive image -->
<img src="image.jpg" class="img-fluid" alt="Responsive image">

<!-- Image with specific aspect ratio -->
<div class="ratio ratio-16x9">
    <img src="image.jpg" alt="16:9 aspect ratio image">
</div>
```

### Responsive Typography

```html
<h1 class="display-1">Large heading</h1>
<p class="lead">Lead paragraph that stands out.</p>
<p class="fs-1">Font size 1</p>
<p class="fs-6">Font size 6 (smallest)</p>
```

## Customizing Bootstrap

### Using SASS Variables

Create a custom SCSS file to override Bootstrap variables:

```scss
// _custom-variables.scss

// Color system
$primary: #007bff;
$secondary: #6c757d;
$success: #28a745;
$info: #17a2b8;
$warning: #ffc107;
$danger: #dc3545;
$light: #f8f9fa;
$dark: #343a40;

// Typography
$font-family-sans-serif: "Helvetica Neue", Arial, sans-serif;
$font-size-base: 1rem;
$line-height-base: 1.5;

// Grid breakpoints
$grid-breakpoints: (
  xs: 0,
  sm: 576px,
  md: 768px,
  lg: 992px,
  xl: 1200px,
  xxl: 1400px
);

// Container max widths
$container-max-widths: (
  sm: 540px,
  md: 720px,
  lg: 960px,
  xl: 1140px,
  xxl: 1320px
);

// Import Bootstrap
@import "bootstrap";
```

### Creating Custom Components

```scss
// Custom WordPress-specific components

.wp-post-card {
  @extend .card;
  
  .wp-post-thumbnail {
    @extend .card-img-top;
  }
  
  .wp-post-content {
    @extend .card-body;
  }
  
  .wp-post-title {
    @extend .card-title;
    @extend .h5;
  }
  
  .wp-post-excerpt {
    @extend .card-text;
  }
  
  .wp-post-meta {
    @extend .card-footer;
    @extend .bg-transparent;
    font-size: 0.875rem;
    color: $text-muted;
  }
}

.wp-sidebar-widget {
  @extend .card;
  @extend .mb-4;
  
  .widget-title {
    @extend .card-header;
    @extend .h6;
    @extend .mb-0;
  }
  
  .widget-content {
    @extend .card-body;
  }
}
```

## Best Practices

### 1. Performance Optimization

```php
// Only load Bootstrap where needed
function conditional_bootstrap() {
    if (!is_admin()) {
        wp_enqueue_style('bootstrap-css');
        wp_enqueue_script('bootstrap-js');
    }
}
add_action('wp_enqueue_scripts', 'conditional_bootstrap');

// Load only required Bootstrap components
function custom_bootstrap() {
    wp_enqueue_style(
        'custom-bootstrap',
        get_template_directory_uri() . '/assets/css/bootstrap-custom.css'
    );
}
```

### 2. Accessibility

```html
<!-- Use semantic HTML with Bootstrap classes -->
<nav class="navbar" role="navigation" aria-label="Main navigation">
  <!-- Navigation content -->
</nav>

<main role="main" class="container">
  <!-- Main content -->
</main>

<aside role="complementary" class="sidebar">
  <!-- Sidebar content -->
</aside>
```

### 3. WordPress Integration

```php
// Add Bootstrap classes to WordPress elements
function add_bootstrap_classes($content) {
    // Add Bootstrap table classes
    $content = str_replace('<table>', '<table class="table table-striped">', $content);
    
    // Add Bootstrap image classes
    $content = str_replace('<img', '<img class="img-fluid"', $content);
    
    return $content;
}
add_filter('the_content', 'add_bootstrap_classes');

// Bootstrap pagination
function bootstrap_pagination() {
    global $wp_query;
    
    $big = 999999999;
    
    $pages = paginate_links(array(
        'base' => str_replace($big, '%#%', esc_url(get_pagenum_link($big))),
        'format' => '?paged=%#%',
        'current' => max(1, get_query_var('paged')),
        'total' => $wp_query->max_num_pages,
        'type' => 'array',
        'prev_text' => '&laquo;',
        'next_text' => '&raquo;',
    ));
    
    if (is_array($pages)) {
        echo '<nav aria-label="Page navigation"><ul class="pagination justify-content-center">';
        foreach ($pages as $page) {
            $class = strpos($page, 'current') !== false ? 'page-item active' : 'page-item';
            $page = str_replace('page-numbers', 'page-link', $page);
            echo '<li class="' . $class . '">' . $page . '</li>';
        }
        echo '</ul></nav>';
    }
}
```

### 4. Theme Compatibility

```php
// Ensure Bootstrap doesn't conflict with WordPress styles
function bootstrap_theme_setup() {
    // Remove default WordPress styles that conflict with Bootstrap
    add_theme_support('html5', array(
        'search-form',
        'comment-form',
        'comment-list',
        'gallery',
        'caption',
    ));
    
    // Add theme support for responsive embeds
    add_theme_support('responsive-embeds');
    
    // Add theme support for wide alignment
    add_theme_support('align-wide');
}
add_action('after_setup_theme', 'bootstrap_theme_setup');
```

## Resources

- [Bootstrap Official Documentation](https://getbootstrap.com/)
- [Bootstrap GitHub Repository](https://github.com/twbs/bootstrap)
- [Bootstrap Examples](https://getbootstrap.com/docs/5.3/examples/)
- [Bootstrap Cheat Sheet](https://bootstrap-cheatsheet.themeselection.com/)
- [WordPress Bootstrap Integration Guide](https://developer.wordpress.org/themes/advanced-topics/bootstrap-integration/)

---

*This document covers Bootstrap fundamentals and WordPress integration. The next document will introduce Bootscore and show how it leverages Bootstrap to create a powerful WordPress starter theme.*
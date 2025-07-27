# WordPress Theme Development Fundamentals

## Table of Contents
1. [Introduction to WordPress Themes](#introduction-to-wordpress-themes)
2. [WordPress Theme Structure](#wordpress-theme-structure)
3. [Core Theme Files](#core-theme-files)
4. [Template Hierarchy](#template-hierarchy)
5. [WordPress APIs for Themes](#wordpress-apis-for-themes)
6. [Theme Support Features](#theme-support-features)
7. [Best Practices](#best-practices)

## Introduction to WordPress Themes

WordPress themes control the visual presentation and layout of a WordPress website. A theme consists of template files, stylesheets, and JavaScript files that work together to render the frontend of your site.

### Key Concepts

- **Template Files**: PHP files that define how content is displayed
- **Template Hierarchy**: WordPress's system for determining which template file to use
- **Theme Functions**: PHP functions that add features and modify theme behavior
- **Hooks and Filters**: WordPress's system for modifying behavior without editing core files

## WordPress Theme Structure

A typical WordPress theme directory contains several essential files and folders:

```
theme-name/
├── style.css          # Main stylesheet (required)
├── index.php          # Main template file (required)
├── functions.php      # Theme functions and features
├── header.php         # Header template
├── footer.php         # Footer template
├── sidebar.php        # Sidebar template
├── single.php         # Single post template
├── page.php           # Page template
├── archive.php        # Archive template
├── search.php         # Search results template
├── 404.php            # 404 error template
├── comments.php       # Comments template
├── screenshot.png     # Theme preview image
├── assets/            # CSS, JS, images
│   ├── css/
│   ├── js/
│   └── img/
├── template-parts/    # Reusable template components
├── inc/               # Theme includes and functionality
└── languages/         # Translation files
```

## Core Theme Files

### 1. style.css (Required)

The main stylesheet must contain theme information in a header comment:

```css
/*
Theme Name: Your Theme Name
Description: Theme description
Version: 1.0.0
Author: Your Name
Text Domain: your-theme-textdomain
*/
```

### 2. index.php (Required)

The fallback template for all content types:

```php
<?php get_header(); ?>

<main id="main" class="site-main">
    <?php if (have_posts()) : ?>
        <?php while (have_posts()) : the_post(); ?>
            <article id="post-<?php the_ID(); ?>" <?php post_class(); ?>>
                <h2><a href="<?php the_permalink(); ?>"><?php the_title(); ?></a></h2>
                <div class="entry-content">
                    <?php the_excerpt(); ?>
                </div>
            </article>
        <?php endwhile; ?>
        
        <?php the_posts_navigation(); ?>
    <?php else : ?>
        <p><?php esc_html_e('No posts found.', 'textdomain'); ?></p>
    <?php endif; ?>
</main>

<?php get_sidebar(); ?>
<?php get_footer(); ?>
```

### 3. functions.php

The central file for theme functionality:

```php
<?php
/**
 * Theme setup and initialization
 */
function your_theme_setup() {
    // Add theme support for various features
    add_theme_support('post-thumbnails');
    add_theme_support('automatic-feed-links');
    add_theme_support('title-tag');
    add_theme_support('html5', array(
        'search-form',
        'comment-form',
        'comment-list',
        'gallery',
        'caption',
    ));
    
    // Register navigation menus
    register_nav_menus(array(
        'primary' => __('Primary Menu', 'textdomain'),
        'footer'  => __('Footer Menu', 'textdomain'),
    ));
}
add_action('after_setup_theme', 'your_theme_setup');

/**
 * Enqueue scripts and styles
 */
function your_theme_scripts() {
    wp_enqueue_style('theme-style', get_stylesheet_uri());
    wp_enqueue_script('theme-script', get_template_directory_uri() . '/assets/js/main.js', array('jquery'), '1.0.0', true);
}
add_action('wp_enqueue_scripts', 'your_theme_scripts');

/**
 * Register widget areas
 */
function your_theme_widgets_init() {
    register_sidebar(array(
        'name'          => __('Primary Sidebar', 'textdomain'),
        'id'            => 'sidebar-1',
        'description'   => __('Add widgets here.', 'textdomain'),
        'before_widget' => '<section id="%1$s" class="widget %2$s">',
        'after_widget'  => '</section>',
        'before_title'  => '<h2 class="widget-title">',
        'after_title'   => '</h2>',
    ));
}
add_action('widgets_init', 'your_theme_widgets_init');
```

## Template Hierarchy

WordPress follows a specific hierarchy to determine which template file to use for different content types:

### Page Templates
1. `page-{slug}.php`
2. `page-{id}.php` 
3. `page-{template}.php`
4. `page.php`
5. `singular.php`
6. `index.php`

### Single Post Templates
1. `single-{post-type}-{slug}.php`
2. `single-{post-type}.php`
3. `single.php`
4. `singular.php`
5. `index.php`

### Archive Templates
1. `archive-{post-type}.php`
2. `archive.php`
3. `index.php`

### Example: Custom Page Template

```php
<?php
/*
Template Name: Custom Landing Page
*/

get_header(); ?>

<div class="custom-landing-page">
    <?php while (have_posts()) : the_post(); ?>
        <div class="hero-section">
            <h1><?php the_title(); ?></h1>
            <?php if (has_post_thumbnail()) : ?>
                <div class="hero-image">
                    <?php the_post_thumbnail('large'); ?>
                </div>
            <?php endif; ?>
        </div>
        
        <div class="content-section">
            <?php the_content(); ?>
        </div>
    <?php endwhile; ?>
</div>

<?php get_footer(); ?>
```

## WordPress APIs for Themes

### 1. Theme Customizer API

Add customization options to the WordPress Customizer:

```php
function your_theme_customize_register($wp_customize) {
    // Add a section
    $wp_customize->add_section('your_theme_colors', array(
        'title'    => __('Theme Colors', 'textdomain'),
        'priority' => 30,
    ));
    
    // Add a setting
    $wp_customize->add_setting('primary_color', array(
        'default'           => '#0073aa',
        'sanitize_callback' => 'sanitize_hex_color',
    ));
    
    // Add a control
    $wp_customize->add_control(new WP_Customize_Color_Control($wp_customize, 'primary_color', array(
        'label'    => __('Primary Color', 'textdomain'),
        'section'  => 'your_theme_colors',
        'settings' => 'primary_color',
    )));
}
add_action('customize_register', 'your_theme_customize_register');
```

### 2. Custom Post Types

Register custom post types for specialized content:

```php
function create_portfolio_post_type() {
    register_post_type('portfolio',
        array(
            'labels' => array(
                'name' => __('Portfolio'),
                'singular_name' => __('Portfolio Item')
            ),
            'public' => true,
            'has_archive' => true,
            'supports' => array('title', 'editor', 'thumbnail'),
            'menu_icon' => 'dashicons-portfolio'
        )
    );
}
add_action('init', 'create_portfolio_post_type');
```

### 3. Custom Taxonomies

Create custom taxonomies for organizing content:

```php
function create_portfolio_taxonomy() {
    register_taxonomy(
        'portfolio_category',
        'portfolio',
        array(
            'label' => __('Portfolio Categories'),
            'hierarchical' => true,
            'public' => true,
        )
    );
}
add_action('init', 'create_portfolio_taxonomy');
```

## Theme Support Features

WordPress themes can declare support for various features:

```php
function your_theme_setup() {
    // Featured images
    add_theme_support('post-thumbnails');
    
    // Custom image sizes
    add_image_size('hero-image', 1200, 600, true);
    
    // Automatic feed links
    add_theme_support('automatic-feed-links');
    
    // HTML5 markup
    add_theme_support('html5', array(
        'search-form',
        'comment-form',
        'comment-list',
        'gallery',
        'caption'
    ));
    
    // Custom logo
    add_theme_support('custom-logo', array(
        'height'      => 100,
        'width'       => 400,
        'flex-height' => true,
        'flex-width'  => true,
    ));
    
    // Custom header
    add_theme_support('custom-header', array(
        'default-image' => get_template_directory_uri() . '/assets/img/header.jpg',
        'width'         => 1200,
        'height'        => 400,
        'flex-width'    => true,
        'flex-height'   => true,
    ));
    
    // Custom background
    add_theme_support('custom-background', array(
        'default-color' => 'ffffff',
    ));
    
    // Editor styles
    add_theme_support('editor-styles');
    add_editor_style('assets/css/editor-style.css');
    
    // Wide alignment (Gutenberg)
    add_theme_support('align-wide');
    
    // Responsive embeds
    add_theme_support('responsive-embeds');
}
add_action('after_setup_theme', 'your_theme_setup');
```

## Best Practices

### 1. Security

- Always sanitize and validate user input
- Use nonces for form submissions
- Escape output data

```php
// Sanitize input
$user_input = sanitize_text_field($_POST['user_input']);

// Escape output
echo esc_html($user_input);
echo esc_url($url);
echo esc_attr($attribute);
```

### 2. Performance

- Minimize HTTP requests
- Optimize images
- Use conditional loading

```php
// Conditional script loading
function load_scripts_conditionally() {
    if (is_page('contact')) {
        wp_enqueue_script('contact-form');
    }
}
add_action('wp_enqueue_scripts', 'load_scripts_conditionally');
```

### 3. Accessibility

- Use semantic HTML
- Provide proper ARIA labels
- Ensure keyboard navigation

```php
// Skip link for accessibility
function add_skip_link() {
    echo '<a class="skip-link screen-reader-text" href="#main">' . __('Skip to content', 'textdomain') . '</a>';
}
add_action('wp_body_open', 'add_skip_link');
```

### 4. Internationalization

- Use translation functions
- Load text domain
- Provide translation files

```php
// Load text domain
function your_theme_load_textdomain() {
    load_theme_textdomain('textdomain', get_template_directory() . '/languages');
}
add_action('after_setup_theme', 'your_theme_load_textdomain');

// Translatable strings
_e('Hello World', 'textdomain');
echo __('Translated text', 'textdomain');
printf(__('Hello %s', 'textdomain'), $name);
```

## Resources

- [WordPress Theme Developer Handbook](https://developer.wordpress.org/themes/)
- [WordPress Codex](https://codex.wordpress.org/)
- [WordPress/WordPress Repository](https://github.com/WordPress/WordPress)
- [WordPress/wordpress-develop Repository](https://github.com/WordPress/wordpress-develop)

---

*This document provides the foundation for understanding WordPress theme development. The next document will cover Bootstrap integration and how it enhances WordPress theme development.*
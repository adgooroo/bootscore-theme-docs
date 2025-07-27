# Bootscore Introduction and Architecture

## Table of Contents
1. [What is Bootscore](#what-is-bootscore)
2. [Bootscore vs Traditional WordPress Themes](#bootscore-vs-traditional-wordpress-themes)
3. [Core Architecture](#core-architecture)
4. [File Structure Overview](#file-structure-overview)
5. [Key Features](#key-features)
6. [Getting Started](#getting-started)
7. [Installation Methods](#installation-methods)
8. [First Steps After Installation](#first-steps-after-installation)
9. [Development Philosophy](#development-philosophy)
10. [Community and Support](#community-and-support)

## What is Bootscore

Bootscore is a flexible Bootstrap WordPress starter theme that combines the power of Bootstrap 5 with the solid foundation of Underscores (_s). It's designed for developers who want to build custom WordPress themes quickly while maintaining full control over the codebase.

### Repository Reference

The main Bootscore repository ([bootscore/bootscore](https://github.com/bootscore/bootscore)) serves as the primary source for the theme, containing all core files, documentation, and community contributions.

### Key Distinguishing Features

- **Built-in SASS Compiler**: No need for Node.js, Gulp, or external build tools
- **Bootstrap 5 Integration**: Latest Bootstrap version with full customization
- **WooCommerce Ready**: Complete e-commerce support out of the box
- **Underscores Foundation**: Solid WordPress theme development practices
- **Code-First Approach**: No Customizer settings or drag-and-drop interface
- **Developer-Focused**: Built for developers who write code

## Bootscore vs Traditional WordPress Themes

### Traditional Themes
- Rely on WordPress Customizer for configuration
- Often include page builders or visual editors
- Limited flexibility without code modification
- Heavy with features most users don't need
- Difficult to maintain and update

### Bootscore Approach
- **Code-Based Customization**: Edit PHP, SCSS, and JS files directly
- **Minimal Core**: Only essential features included
- **Full Control**: Complete access to all theme aspects
- **Performance Focused**: No unnecessary bloat or features
- **Developer Workflow**: Git-friendly, version controllable

### Comparison Table

| Feature | Traditional Themes | Bootscore |
|---------|-------------------|-----------|
| Customization | Customizer/Admin Panel | Code Files |
| Performance | Often Heavy | Lightweight |
| Flexibility | Limited | Complete |
| Updates | May Break Customizations | Code-Safe |
| Learning Curve | User-Friendly | Developer-Focused |
| Build Tools | Usually Required | Built-in SASS Compiler |

## Core Architecture

Bootscore is built on three foundational technologies:

### 1. WordPress Underscores (_s)
- Provides solid WordPress theme structure
- Follows WordPress coding standards
- Includes essential theme functions
- Ensures compatibility with WordPress features

### 2. Bootstrap 5
- Modern CSS framework for responsive design
- Component-based architecture
- Utility-first approach
- Mobile-first responsive design

### 3. Built-in SASS Compiler
- PHP-based SCSS compilation
- No Node.js or build tools required
- Automatic compilation on file changes
- Development and production modes

### Architecture Diagram

```
Bootscore Theme
├── WordPress Core Integration
│   ├── Template Hierarchy
│   ├── Hooks & Filters
│   ├── WordPress APIs
│   └── Theme Support Features
├── Bootstrap 5 Framework
│   ├── Grid System
│   ├── Components
│   ├── Utilities
│   └── JavaScript Modules
└── Built-in SASS Compiler
    ├── SCSS Processing
    ├── Bootstrap Customization
    ├── Auto-compilation
    └── Production Optimization
```

## File Structure Overview

Understanding Bootscore's file organization is crucial for effective development:

```
bootscore/
├── assets/                    # Compiled assets
│   ├── css/
│   │   ├── lib/              # Bootstrap & vendor CSS
│   │   └── main.css          # Compiled theme styles
│   ├── js/
│   │   ├── lib/              # Bootstrap & vendor JS
│   │   └── main.js           # Theme JavaScript
│   └── img/                  # Theme images
├── inc/                      # Theme functionality
│   ├── customizer.php        # Minimal customizer settings
│   ├── template-functions.php # Helper functions
│   ├── template-tags.php     # Template helper functions
│   └── class-wp-bootstrap-navwalker.php # Bootstrap navigation
├── languages/                # Translation files
│   └── bootscore.pot         # Translation template
├── page-templates/           # Custom page templates
│   ├── page-blank.php        # Blank page template
│   ├── page-full-width.php   # Full width template
│   └── ...                   # Additional templates
├── patterns/                 # Block patterns
│   └── *.php                 # Pattern definitions
├── single-templates/         # Single post templates
│   ├── single-full-width.php # Full width single
│   └── ...                   # Additional single templates
├── template-parts/           # Reusable template parts
│   ├── header/               # Header components
│   ├── footer/               # Footer components
│   ├── content/              # Content components
│   └── navigation/           # Navigation components
├── woocommerce/              # WooCommerce templates
│   ├── archive-product.php   # Product archive
│   ├── single-product.php    # Single product
│   └── ...                   # All WooCommerce templates
├── scss/                     # SASS source files
│   ├── bootstrap/            # Bootstrap SCSS files
│   ├── _variables.scss       # Theme variables
│   ├── _custom.scss          # Custom styles
│   └── style.scss            # Main SCSS file
├── js/                       # JavaScript source files
│   ├── bootstrap/            # Bootstrap JS modules
│   └── theme.js              # Theme JavaScript
├── functions.php             # Main theme functions
├── style.css                 # Theme information & compiled CSS
├── index.php                 # Main template file
├── header.php                # Header template
├── footer.php                # Footer template
├── sidebar.php               # Sidebar template
└── ...                       # Standard WordPress templates
```

## Key Features

### 1. No Build Tools Required

Unlike most modern WordPress themes, Bootscore includes a built-in PHP SASS compiler:

```php
// Automatic SCSS compilation
function bootscore_compile_scss() {
    $scss_file = get_template_directory() . '/scss/style.scss';
    $css_file = get_template_directory() . '/style.css';
    
    // Compile SCSS to CSS automatically
    if (file_exists($scss_file)) {
        $compiler = new ScssPhp\ScssPhp\Compiler();
        $compiled_css = $compiler->compile(file_get_contents($scss_file));
        file_put_contents($css_file, $compiled_css);
    }
}
```

### 2. Bootstrap 5 Source Integration

Full Bootstrap source code is included, allowing complete customization:

```scss
// Override Bootstrap variables
$primary: #your-color;
$secondary: #your-secondary-color;

// Import Bootstrap
@import "bootstrap/bootstrap";

// Add custom styles
.your-custom-class {
    // Your styles here
}
```

### 3. WooCommerce Integration

Complete WooCommerce support with Bootstrap-styled templates:

- Custom product pages
- Shopping cart integration
- Checkout process
- Account pages
- AJAX cart functionality

### 4. Multiple Template Options

Bootscore includes various page and post templates:

- **Page Templates**: Default, sidebar left, sidebar none, full width, blank
- **Single Templates**: Default, sidebar left, sidebar none, full width
- **Archive Templates**: Standard, masonry, equal height
- **Custom Templates**: Landing pages, contact pages, etc.

### 5. Responsive Design

Built-in responsive features:

- Mobile-first approach
- Bootstrap grid system
- Responsive images
- Mobile navigation
- Touch-friendly interfaces

## Getting Started

### Prerequisites

Before installing Bootscore, ensure you have:

- WordPress 5.0 or higher
- PHP 7.4 or higher
- Basic knowledge of HTML, CSS, and PHP
- Understanding of WordPress theme structure
- Familiarity with Bootstrap (recommended)

### System Requirements

- **WordPress**: 5.0+
- **PHP**: 7.4+
- **Memory**: 64MB minimum (128MB recommended)
- **MySQL**: 5.6+ or MariaDB 10.1+

## Installation Methods

### Method 1: Download from GitHub

1. Go to [bootscore/bootscore](https://github.com/bootscore/bootscore)
2. Click "Code" → "Download ZIP"
3. Extract the ZIP file
4. Upload to `/wp-content/themes/bootscore/`
5. Activate in WordPress admin

```bash
# Via command line
cd /path/to/wp-content/themes/
wget https://github.com/bootscore/bootscore/archive/main.zip
unzip main.zip
mv bootscore-main bootscore
```

### Method 2: Git Clone

```bash
cd /path/to/wp-content/themes/
git clone https://github.com/bootscore/bootscore.git
```

### Method 3: WordPress Admin Upload

1. Download ZIP from GitHub
2. Go to WordPress Admin → Appearance → Themes
3. Click "Add New" → "Upload Theme"
4. Choose the ZIP file and install
5. Activate the theme

### Method 4: Composer Installation

For development environments:

```bash
composer require bootscore/bootscore
```

## First Steps After Installation

### 1. Activate the Theme

```php
// WordPress admin or programmatically
switch_theme('bootscore');
```

### 2. Set Up Menus

Navigate to Appearance → Menus and create:
- Primary menu (header navigation)
- Footer menu (footer links)

### 3. Configure Widgets

Set up widget areas:
- Primary sidebar
- Footer widgets (3 columns)
- Top footer widget

### 4. Customize Colors and Fonts

Edit `/scss/_variables.scss`:

```scss
// Color customization
$primary: #007bff;
$secondary: #6c757d;
$success: #28a745;

// Typography
$font-family-sans-serif: "Your Font", sans-serif;
$font-size-base: 1rem;
```

### 5. Upload Logo

```php
// Add theme support for custom logo
add_theme_support('custom-logo', array(
    'height'      => 100,
    'width'       => 400,
    'flex-height' => true,
    'flex-width'  => true,
));
```

### 6. Create Child Theme (Recommended)

For upgrade-safe customizations:

```php
<?php
// child-theme/style.css
/*
Template: bootscore
Theme Name: Bootscore Child
Version: 1.0
*/

// child-theme/functions.php
<?php
function bootscore_child_enqueue_styles() {
    wp_enqueue_style('parent-style', get_template_directory_uri() . '/style.css');
}
add_action('wp_enqueue_scripts', 'bootscore_child_enqueue_styles');
```

## Development Philosophy

### Code Over Configuration

Bootscore prioritizes direct code modification over admin interfaces:

```php
// Instead of customizer options
function my_custom_header() {
    // Add custom header functionality
}

// Instead of theme options
$custom_color = '#your-color'; // Define in code
```

### Performance First

- Minimal JavaScript and CSS
- Conditional loading of resources
- Optimized images and assets
- Clean, semantic HTML

### Developer Experience

- Git-friendly file structure
- Version control compatible
- Clear file organization
- Comprehensive documentation

### Standards Compliance

- WordPress coding standards
- Bootstrap best practices
- Accessibility guidelines
- SEO optimization

## Community and Support

### Official Resources

- **Website**: [bootscore.me](https://bootscore.me)
- **GitHub**: [bootscore/bootscore](https://github.com/bootscore/bootscore)
- **Documentation**: [bootscore.me/docs](https://bootscore.me/docs)
- **Blog**: [bootscore.me/blog](https://bootscore.me/blog)

### Getting Help

1. **GitHub Issues**: Report bugs and request features
2. **Discussions**: Community Q&A and discussions
3. **Documentation**: Comprehensive guides and tutorials
4. **Blog**: Latest updates and tutorials

### Contributing

Bootscore welcomes contributions:

```bash
# Fork the repository
git clone https://github.com/your-username/bootscore.git
cd bootscore

# Create feature branch
git checkout -b feature/new-feature

# Make changes and commit
git commit -m "Add new feature"

# Push and create pull request
git push origin feature/new-feature
```

### Community Guidelines

- Follow WordPress and Bootstrap conventions
- Write clear, documented code
- Test thoroughly before submitting
- Be respectful and helpful

## Next Steps

Now that you understand Bootscore's foundation, the next documents will cover:

1. **Built-in SASS Compiler**: Deep dive into the compilation system
2. **WooCommerce Integration**: E-commerce features and customization
3. **Child Theme Development**: Safe customization practices
4. **Hooks and Filters**: Bootscore-specific extension points
5. **Template Customization**: Advanced template modifications

---

*This document introduces Bootscore's core concepts and architecture. The next document will explore the built-in SASS compiler that sets Bootscore apart from other WordPress themes.*
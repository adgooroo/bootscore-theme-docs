# WooCommerce Integration

## Table of Contents
1. [Introduction to Bootscore WooCommerce](#introduction-to-bootscore-woocommerce)
2. [WooCommerce Support Features](#woocommerce-support-features)
3. [Template Structure](#template-structure)
4. [AJAX Cart System](#ajax-cart-system)
5. [Product Pages](#product-pages)
6. [Shop and Archive Pages](#shop-and-archive-pages)
7. [Checkout and Account Pages](#checkout-and-account-pages)
8. [Bootstrap-Styled WooCommerce Components](#bootstrap-styled-woocommerce-components)
9. [Customization and Hooks](#customization-and-hooks)
10. [Best Practices](#best-practices)

## Introduction to Bootscore WooCommerce

Bootscore provides comprehensive WooCommerce support out of the box, featuring Bootstrap-styled templates, AJAX cart functionality, and full e-commerce integration. Unlike many WordPress themes that treat WooCommerce as an afterthought, Bootscore is built with e-commerce as a core feature.

### Key WooCommerce Features

- **Complete Template Coverage**: All WooCommerce templates styled with Bootstrap
- **AJAX Cart System**: Off-canvas cart with real-time updates
- **Responsive Design**: Mobile-optimized shopping experience
- **Bootstrap Integration**: All components use Bootstrap classes
- **Performance Optimized**: Minimal extra code for maximum speed
- **Accessibility Ready**: WCAG compliant e-commerce templates

### WooCommerce Compatibility

Bootscore maintains compatibility with:
- WooCommerce 8.0+
- WordPress 6.0+
- All major WooCommerce extensions
- Payment gateways and shipping methods
- Multi-language plugins (WPML, Polylang)

## WooCommerce Support Features

### Theme Support Declaration

```php
// functions.php - WooCommerce theme support
function bootscore_woocommerce_support() {
    add_theme_support('woocommerce');
    add_theme_support('wc-product-gallery-zoom');
    add_theme_support('wc-product-gallery-lightbox');
    add_theme_support('wc-product-gallery-slider');
    
    // Custom image sizes for WooCommerce
    add_image_size('woocommerce_thumbnail_custom', 300, 300, true);
    add_image_size('woocommerce_single_custom', 600, 600, true);
}
add_action('after_setup_theme', 'bootscore_woocommerce_support');
```

### WooCommerce-Specific Enqueues

```php
// Conditional WooCommerce assets
function bootscore_woocommerce_scripts() {
    if (is_woocommerce() || is_cart() || is_checkout()) {
        wp_enqueue_script(
            'bootscore-woocommerce',
            get_template_directory_uri() . '/assets/js/woocommerce.js',
            array('jquery', 'wc-add-to-cart'),
            '1.0.0',
            true
        );
        
        wp_enqueue_style(
            'bootscore-woocommerce',
            get_template_directory_uri() . '/assets/css/woocommerce.css',
            array(),
            '1.0.0'
        );
        
        wp_localize_script('bootscore-woocommerce', 'bootscore_wc', array(
            'ajax_url' => admin_url('admin-ajax.php'),
            'nonce' => wp_create_nonce('bootscore_wc_nonce'),
            'cart_url' => wc_get_cart_url(),
            'checkout_url' => wc_get_checkout_url(),
        ));
    }
}
add_action('wp_enqueue_scripts', 'bootscore_woocommerce_scripts');
```

## Template Structure

Bootscore includes a complete set of WooCommerce templates in the `/woocommerce/` directory:

```
woocommerce/
├── archive-product.php          # Shop page
├── single-product.php           # Product page
├── taxonomy-product-cat.php     # Category archive
├── taxonomy-product-tag.php     # Tag archive
├── cart/
│   ├── cart.php                 # Shopping cart
│   ├── cart-empty.php           # Empty cart
│   ├── cart-totals.php          # Cart totals
│   ├── mini-cart.php            # AJAX mini cart
│   └── proceed-to-checkout-button.php
├── checkout/
│   ├── form-checkout.php        # Checkout form
│   ├── form-billing.php         # Billing fields
│   ├── form-shipping.php        # Shipping fields
│   ├── payment.php              # Payment methods
│   ├── review-order.php         # Order review
│   └── thankyou.php             # Thank you page
├── myaccount/
│   ├── dashboard.php            # Account dashboard
│   ├── form-login.php           # Login form
│   ├── form-edit-account.php    # Edit account
│   ├── orders.php               # Order history
│   ├── downloads.php            # Digital downloads
│   └── addresses.php            # Address book
├── single-product/
│   ├── add-to-cart/             # Add to cart buttons
│   ├── product-image.php        # Product gallery
│   ├── product-thumbnails.php   # Thumbnail gallery
│   ├── price.php                # Price display
│   ├── rating.php               # Star rating
│   ├── short-description.php    # Product excerpt
│   ├── tabs/                    # Product tabs
│   └── related.php              # Related products
├── content-product.php          # Product loop item
├── content-single-product.php   # Single product content
└── global/
    ├── breadcrumb.php           # WooCommerce breadcrumb
    ├── form-login.php           # Global login form
    ├── quantity-input.php       # Quantity selector
    └── wrapper-start.php        # Content wrapper
```

### Template Hierarchy

WooCommerce follows a specific template hierarchy that Bootscore leverages:

1. **Shop Page**: `archive-product.php` → `archive.php` → `index.php`
2. **Product Page**: `single-product.php` → `single.php` → `singular.php` → `index.php`
3. **Category**: `taxonomy-product-cat-{slug}.php` → `taxonomy-product-cat.php` → `archive-product.php`
4. **Cart**: `cart/cart.php` → WooCommerce default
5. **Checkout**: `checkout/form-checkout.php` → WooCommerce default

## AJAX Cart System

One of Bootscore's standout features is its AJAX-powered off-canvas cart system.

### Off-Canvas Cart HTML

```html
<!-- Off-canvas cart in header.php -->
<div class="offcanvas offcanvas-end" tabindex="-1" id="offcanvas-cart" aria-labelledby="offcanvas-cart-label">
    <div class="offcanvas-header">
        <h5 class="offcanvas-title" id="offcanvas-cart-label">
            <?php _e('Shopping Cart', 'bootscore'); ?>
        </h5>
        <button type="button" class="btn-close" data-bs-dismiss="offcanvas" aria-label="Close"></button>
    </div>
    <div class="offcanvas-body p-0">
        <div id="cart-content-wrapper">
            <?php woocommerce_mini_cart(); ?>
        </div>
    </div>
</div>
```

### AJAX Cart JavaScript

```javascript
// assets/js/woocommerce.js
(function($) {
    'use strict';
    
    // Update cart on add to cart
    $(document).on('added_to_cart', function(event, fragments, cart_hash, $button) {
        updateCartCount();
        updateMiniCart();
        showCartOffcanvas();
    });
    
    // Update cart count in header
    function updateCartCount() {
        $.ajax({
            url: bootscore_wc.ajax_url,
            type: 'POST',
            data: {
                action: 'get_cart_count',
                nonce: bootscore_wc.nonce
            },
            success: function(response) {
                if (response.success) {
                    $('.cart-count').text(response.data.count);
                    $('.cart-total').text(response.data.total);
                }
            }
        });
    }
    
    // Update mini cart content
    function updateMiniCart() {
        $.ajax({
            url: bootscore_wc.ajax_url,
            type: 'POST',
            data: {
                action: 'get_mini_cart',
                nonce: bootscore_wc.nonce
            },
            success: function(response) {
                if (response.success) {
                    $('#cart-content-wrapper').html(response.data.mini_cart);
                }
            }
        });
    }
    
    // Show off-canvas cart
    function showCartOffcanvas() {
        var cartOffcanvas = new bootstrap.Offcanvas(document.getElementById('offcanvas-cart'));
        cartOffcanvas.show();
    }
    
    // Remove item from cart
    $(document).on('click', '.remove-cart-item', function(e) {
        e.preventDefault();
        
        var $button = $(this);
        var productKey = $button.data('product-key');
        
        $button.addClass('loading');
        
        $.ajax({
            url: bootscore_wc.ajax_url,
            type: 'POST',
            data: {
                action: 'remove_cart_item',
                product_key: productKey,
                nonce: bootscore_wc.nonce
            },
            success: function(response) {
                if (response.success) {
                    updateCartCount();
                    updateMiniCart();
                    
                    // Show success message
                    showCartMessage(response.data.message, 'success');
                } else {
                    showCartMessage(response.data.message, 'error');
                }
            },
            complete: function() {
                $button.removeClass('loading');
            }
        });
    });
    
    // Show cart messages
    function showCartMessage(message, type) {
        var alertClass = type === 'success' ? 'alert-success' : 'alert-danger';
        var messageHtml = '<div class="alert ' + alertClass + ' alert-dismissible fade show" role="alert">' +
                         message +
                         '<button type="button" class="btn-close" data-bs-dismiss="alert"></button>' +
                         '</div>';
        
        $('#cart-messages').html(messageHtml);
        
        // Auto-hide after 3 seconds
        setTimeout(function() {
            $('.alert').alert('close');
        }, 3000);
    }
    
})(jQuery);
```

### AJAX Cart PHP Functions

```php
// AJAX cart count
function bootscore_get_cart_count() {
    check_ajax_referer('bootscore_wc_nonce', 'nonce');
    
    $count = WC()->cart->get_cart_contents_count();
    $total = WC()->cart->get_cart_total();
    
    wp_send_json_success(array(
        'count' => $count,
        'total' => $total
    ));
}
add_action('wp_ajax_get_cart_count', 'bootscore_get_cart_count');
add_action('wp_ajax_nopriv_get_cart_count', 'bootscore_get_cart_count');

// AJAX mini cart
function bootscore_get_mini_cart() {
    check_ajax_referer('bootscore_wc_nonce', 'nonce');
    
    ob_start();
    woocommerce_mini_cart();
    $mini_cart = ob_get_clean();
    
    wp_send_json_success(array(
        'mini_cart' => $mini_cart
    ));
}
add_action('wp_ajax_get_mini_cart', 'bootscore_get_mini_cart');
add_action('wp_ajax_nopriv_get_mini_cart', 'bootscore_get_mini_cart');

// AJAX remove cart item
function bootscore_remove_cart_item() {
    check_ajax_referer('bootscore_wc_nonce', 'nonce');
    
    $product_key = sanitize_text_field($_POST['product_key']);
    
    if (WC()->cart->remove_cart_item($product_key)) {
        wp_send_json_success(array(
            'message' => __('Item removed from cart.', 'bootscore')
        ));
    } else {
        wp_send_json_error(array(
            'message' => __('Failed to remove item from cart.', 'bootscore')
        ));
    }
}
add_action('wp_ajax_remove_cart_item', 'bootscore_remove_cart_item');
add_action('wp_ajax_nopriv_remove_cart_item', 'bootscore_remove_cart_item');
```

## Product Pages

### Single Product Template

```php
// woocommerce/single-product.php
get_header(); ?>

<div class="container my-5">
    <div class="row">
        <div class="col-12">
            <?php woocommerce_breadcrumb(); ?>
        </div>
    </div>
    
    <?php while (have_posts()) : the_post(); ?>
        <div class="row">
            <div class="col-lg-6">
                <?php woocommerce_show_product_images(); ?>
            </div>
            
            <div class="col-lg-6">
                <div class="product-summary">
                    <?php
                    woocommerce_template_single_title();
                    woocommerce_template_single_rating();
                    woocommerce_template_single_price();
                    woocommerce_template_single_excerpt();
                    woocommerce_template_single_add_to_cart();
                    woocommerce_template_single_meta();
                    ?>
                </div>
            </div>
        </div>
        
        <div class="row mt-5">
            <div class="col-12">
                <?php woocommerce_output_product_data_tabs(); ?>
            </div>
        </div>
        
        <div class="row mt-5">
            <div class="col-12">
                <?php woocommerce_output_related_products(); ?>
            </div>
        </div>
    <?php endwhile; ?>
</div>

<?php get_footer(); ?>
```

### Product Gallery Customization

```php
// Custom product gallery with Bootstrap
function bootscore_product_gallery() {
    global $product;
    
    $attachment_ids = $product->get_gallery_image_ids();
    
    if ($attachment_ids && $product->get_image_id()) {
        ?>
        <div class="product-gallery">
            <div id="productCarousel" class="carousel slide" data-bs-ride="carousel">
                <div class="carousel-inner">
                    <?php
                    $gallery_images = array_merge(array($product->get_image_id()), $attachment_ids);
                    foreach ($gallery_images as $index => $attachment_id) :
                        $image_url = wp_get_attachment_image_url($attachment_id, 'woocommerce_single');
                        $active_class = $index === 0 ? 'active' : '';
                    ?>
                        <div class="carousel-item <?php echo $active_class; ?>">
                            <img src="<?php echo esc_url($image_url); ?>" 
                                 class="d-block w-100" 
                                 alt="<?php echo esc_attr(get_post_meta($attachment_id, '_wp_attachment_image_alt', true)); ?>">
                        </div>
                    <?php endforeach; ?>
                </div>
                
                <?php if (count($gallery_images) > 1) : ?>
                    <button class="carousel-control-prev" type="button" data-bs-target="#productCarousel" data-bs-slide="prev">
                        <span class="carousel-control-prev-icon"></span>
                    </button>
                    <button class="carousel-control-next" type="button" data-bs-target="#productCarousel" data-bs-slide="next">
                        <span class="carousel-control-next-icon"></span>
                    </button>
                <?php endif; ?>
            </div>
            
            <!-- Thumbnail navigation -->
            <div class="product-thumbnails mt-3">
                <div class="row g-2">
                    <?php foreach ($gallery_images as $index => $attachment_id) : 
                        $thumb_url = wp_get_attachment_image_url($attachment_id, 'woocommerce_gallery_thumbnail');
                    ?>
                        <div class="col-3">
                            <img src="<?php echo esc_url($thumb_url); ?>" 
                                 class="img-fluid thumbnail-nav" 
                                 data-bs-target="#productCarousel" 
                                 data-bs-slide-to="<?php echo $index; ?>"
                                 alt="">
                        </div>
                    <?php endforeach; ?>
                </div>
            </div>
        </div>
        <?php
    }
}
```

### Add to Cart Variations

```php
// Custom variable product add to cart
function bootscore_variable_add_to_cart() {
    global $product;
    
    $available_variations = $product->get_available_variations();
    $attributes = $product->get_variation_attributes();
    ?>
    
    <form class="variations_form cart" method="post" enctype="multipart/form-data" data-product_id="<?php echo absint($product->get_id()); ?>" data-product_variations="<?php echo htmlspecialchars(wp_json_encode($available_variations)); ?>">
        <?php do_action('woocommerce_before_variations_form'); ?>
        
        <?php if (empty($available_variations) && false !== $available_variations) : ?>
            <p class="stock out-of-stock alert alert-warning">
                <?php echo esc_html(apply_filters('woocommerce_out_of_stock_message', __('This product is currently out of stock and unavailable.', 'woocommerce'))); ?>
            </p>
        <?php else : ?>
            <div class="variations mb-3">
                <?php foreach ($attributes as $attribute_name => $options) : ?>
                    <div class="mb-3">
                        <label class="form-label" for="<?php echo esc_attr(sanitize_title($attribute_name)); ?>">
                            <?php echo wc_attribute_label($attribute_name); ?>
                        </label>
                        <?php
                        wc_dropdown_variation_attribute_options(array(
                            'options'   => $options,
                            'attribute' => $attribute_name,
                            'product'   => $product,
                            'class'     => 'form-select',
                        ));
                        ?>
                    </div>
                <?php endforeach; ?>
            </div>
            
            <div class="single_variation_wrap">
                <div class="woocommerce-variation single_variation"></div>
                
                <div class="woocommerce-variation-add-to-cart variations_button">
                    <?php
                    woocommerce_quantity_input(array(
                        'min_value'   => apply_filters('woocommerce_quantity_input_min', $product->get_min_purchase_quantity(), $product),
                        'max_value'   => apply_filters('woocommerce_quantity_input_max', $product->get_max_purchase_quantity(), $product),
                        'input_value' => (isset($_POST['quantity'])) ? wc_stock_amount(wp_unslash($_POST['quantity'])) : $product->get_min_purchase_quantity(),
                        'classes'     => array('form-control', 'd-inline-block', 'me-2'),
                        'style'       => 'width: 80px;'
                    ));
                    ?>
                    
                    <button type="submit" class="single_add_to_cart_button btn btn-primary disabled wc-variation-selection-needed">
                        <?php echo esc_html($product->single_add_to_cart_text()); ?>
                    </button>
                </div>
            </div>
        <?php endif; ?>
        
        <?php do_action('woocommerce_after_variations_form'); ?>
    </form>
    
    <?php
}
```

## Shop and Archive Pages

### Product Archive Template

```php
// woocommerce/archive-product.php
get_header(); ?>

<div class="container my-5">
    <div class="row">
        <div class="col-lg-3">
            <!-- Sidebar with filters -->
            <div class="shop-sidebar">
                <?php if (is_active_sidebar('shop-sidebar')) : ?>
                    <?php dynamic_sidebar('shop-sidebar'); ?>
                <?php endif; ?>
                
                <!-- Product categories -->
                <div class="widget mb-4">
                    <h5 class="widget-title"><?php _e('Categories', 'bootscore'); ?></h5>
                    <?php
                    wp_list_categories(array(
                        'taxonomy' => 'product_cat',
                        'hide_empty' => true,
                        'hierarchical' => true,
                        'title_li' => '',
                        'walker' => new Bootscore_Category_Walker()
                    ));
                    ?>
                </div>
                
                <!-- Price filter -->
                <?php the_widget('WC_Widget_Price_Filter'); ?>
            </div>
        </div>
        
        <div class="col-lg-9">
            <?php if (woocommerce_product_loop()) : ?>
                <div class="d-flex justify-content-between align-items-center mb-4">
                    <div class="woocommerce-result-count">
                        <?php woocommerce_result_count(); ?>
                    </div>
                    
                    <div class="woocommerce-ordering">
                        <?php woocommerce_catalog_ordering(); ?>
                    </div>
                </div>
                
                <?php woocommerce_product_loop_start(); ?>
                
                <div class="row">
                    <?php if (wc_get_loop_prop('is_shortcode')) : ?>
                        <?php
                        while (have_posts()) :
                            the_post();
                            ?>
                            <div class="col-md-6 col-lg-4 mb-4">
                                <?php wc_get_template_part('content', 'product'); ?>
                            </div>
                            <?php
                        endwhile;
                        ?>
                    <?php endif; ?>
                </div>
                
                <?php woocommerce_product_loop_end(); ?>
                
                <?php
                // Pagination
                woocommerce_pagination();
                ?>
            <?php else : ?>
                <?php
                // No products found
                do_action('woocommerce_no_products_found');
                ?>
            <?php endif; ?>
        </div>
    </div>
</div>

<?php get_footer(); ?>
```

### Product Loop Item

```php
// woocommerce/content-product.php
global $product;

if (empty($product) || !$product->is_visible()) {
    return;
}
?>

<div class="product-card card h-100" <?php wc_product_class('', $product); ?>>
    <div class="card-img-top-wrapper position-relative">
        <?php
        // Product image
        echo woocommerce_get_product_thumbnail('woocommerce_thumbnail', array('class' => 'card-img-top'));
        
        // Sale badge
        if ($product->is_on_sale()) :
        ?>
            <span class="badge bg-danger position-absolute top-0 end-0 m-2">
                <?php _e('Sale!', 'bootscore'); ?>
            </span>
        <?php endif; ?>
        
        <!-- Quick view button -->
        <div class="product-actions position-absolute bottom-0 start-0 end-0 p-2 d-flex gap-2">
            <?php
            // Quick view
            echo '<button class="btn btn-sm btn-outline-light quick-view" data-product-id="' . $product->get_id() . '">';
            echo '<i class="fas fa-eye"></i>';
            echo '</button>';
            
            // Wishlist (if plugin is active)
            if (class_exists('YITH_WCWL')) {
                echo do_shortcode('[yith_wcwl_add_to_wishlist]');
            }
            ?>
        </div>
    </div>
    
    <div class="card-body d-flex flex-column">
        <h5 class="card-title product-title">
            <a href="<?php the_permalink(); ?>" class="text-decoration-none">
                <?php the_title(); ?>
            </a>
        </h5>
        
        <div class="product-rating mb-2">
            <?php woocommerce_template_loop_rating(); ?>
        </div>
        
        <div class="product-price mb-auto">
            <?php woocommerce_template_loop_price(); ?>
        </div>
        
        <div class="product-actions mt-3">
            <?php woocommerce_template_loop_add_to_cart(); ?>
        </div>
    </div>
</div>
```

## Checkout and Account Pages

### Checkout Form Bootstrap Styling

```php
// woocommerce/checkout/form-checkout.php
do_action('woocommerce_before_checkout_form', $checkout);

if (!$checkout->is_registration_enabled() && $checkout->is_registration_required() && !is_user_logged_in()) {
    echo esc_html(apply_filters('woocommerce_checkout_must_be_logged_in_message', __('You must be logged in to checkout.', 'woocommerce')));
    return;
}
?>

<form name="checkout" method="post" class="checkout woocommerce-checkout row" action="<?php echo esc_url(wc_get_checkout_url()); ?>" enctype="multipart/form-data">
    
    <div class="col-lg-8">
        <?php if ($checkout->get_checkout_fields()) : ?>
            <?php do_action('woocommerce_checkout_before_customer_details'); ?>
            
            <div class="checkout-details">
                <div class="billing-fields">
                    <h4><?php _e('Billing Details', 'bootscore'); ?></h4>
                    <?php do_action('woocommerce_checkout_billing'); ?>
                </div>
                
                <?php if (WC()->cart->needs_shipping_address()) : ?>
                    <div class="shipping-fields mt-4">
                        <h4><?php _e('Shipping Details', 'bootscore'); ?></h4>
                        <?php do_action('woocommerce_checkout_shipping'); ?>
                    </div>
                <?php endif; ?>
            </div>
            
            <?php do_action('woocommerce_checkout_after_customer_details'); ?>
        <?php endif; ?>
        
        <div class="additional-fields mt-4">
            <?php do_action('woocommerce_checkout_before_order_review_heading'); ?>
            
            <h4><?php _e('Additional Information', 'bootscore'); ?></h4>
            
            <?php do_action('woocommerce_checkout_before_order_review'); ?>
        </div>
    </div>
    
    <div class="col-lg-4">
        <div class="order-review-wrapper sticky-top">
            <div class="card">
                <div class="card-header">
                    <h5 class="mb-0"><?php _e('Your Order', 'bootscore'); ?></h5>
                </div>
                <div class="card-body">
                    <div id="order_review" class="woocommerce-checkout-review-order">
                        <?php do_action('woocommerce_checkout_order_review'); ?>
                    </div>
                </div>
            </div>
        </div>
    </div>
</form>

<?php do_action('woocommerce_after_checkout_form', $checkout); ?>
```

### Account Dashboard

```php
// woocommerce/myaccount/dashboard.php
?>

<div class="woocommerce-account-dashboard">
    <div class="row">
        <div class="col-lg-8">
            <div class="card">
                <div class="card-body">
                    <h5 class="card-title"><?php _e('Account Dashboard', 'bootscore'); ?></h5>
                    
                    <p class="card-text">
                        <?php
                        printf(
                            __('Hello %1$s (not %1$s? <a href="%2$s">Log out</a>)', 'woocommerce'),
                            '<strong>' . esc_html($current_user->display_name) . '</strong>',
                            esc_url(wc_logout_url())
                        );
                        ?>
                    </p>
                    
                    <p>
                        <?php
                        printf(
                            __('From your account dashboard you can view your <a href="%1$s">recent orders</a>, manage your <a href="%2$s">shipping and billing addresses</a>, and <a href="%3$s">edit your password and account details</a>.', 'woocommerce'),
                            esc_url(wc_get_endpoint_url('orders')),
                            esc_url(wc_get_endpoint_url('edit-address')),
                            esc_url(wc_get_endpoint_url('edit-account'))
                        );
                        ?>
                    </p>
                </div>
            </div>
        </div>
        
        <div class="col-lg-4">
            <div class="card">
                <div class="card-body">
                    <h6 class="card-title"><?php _e('Quick Actions', 'bootscore'); ?></h6>
                    <div class="d-grid gap-2">
                        <a href="<?php echo esc_url(wc_get_endpoint_url('orders')); ?>" class="btn btn-outline-primary">
                            <?php _e('View Orders', 'bootscore'); ?>
                        </a>
                        <a href="<?php echo esc_url(wc_get_endpoint_url('downloads')); ?>" class="btn btn-outline-primary">
                            <?php _e('Downloads', 'bootscore'); ?>
                        </a>
                        <a href="<?php echo esc_url(wc_get_endpoint_url('edit-address')); ?>" class="btn btn-outline-primary">
                            <?php _e('Addresses', 'bootscore'); ?>
                        </a>
                        <a href="<?php echo esc_url(wc_get_endpoint_url('edit-account')); ?>" class="btn btn-outline-primary">
                            <?php _e('Account Details', 'bootscore'); ?>
                        </a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<?php
do_action('woocommerce_account_dashboard');

do_action('woocommerce_after_account_dashboard', $current_user);
?>
```

## Bootstrap-Styled WooCommerce Components

### Custom Form Fields

```php
// Bootstrap form field styling
function bootscore_woocommerce_form_field($field, $key, $args, $value) {
    $defaults = array(
        'type'              => 'text',
        'label'             => '',
        'description'       => '',
        'placeholder'       => '',
        'maxlength'         => false,
        'required'          => false,
        'autocomplete'      => false,
        'id'                => $key,
        'class'             => array('form-control'),
        'label_class'       => array('form-label'),
        'input_class'       => array(),
        'return'            => false,
        'options'           => array(),
        'custom_attributes' => array(),
        'validate'          => array(),
        'default'           => '',
        'autofocus'         => '',
        'priority'          => '',
    );

    $args = wp_parse_args($args, $defaults);
    $args = apply_filters('woocommerce_form_field_args', $args, $key, $value);

    if ($args['required']) {
        $args['class'][] = 'validate-required';
        $required = '&nbsp;<abbr class="required" title="' . esc_attr__('required', 'woocommerce') . '">*</abbr>';
    } else {
        $required = '&nbsp;<span class="optional">(' . esc_html__('optional', 'woocommerce') . ')</span>';
    }

    if (is_string($args['label_class'])) {
        $args['label_class'] = array($args['label_class']);
    }

    if (is_null($value)) {
        $value = $args['default'];
    }

    // Custom attributes
    $custom_attributes         = array();
    $args['custom_attributes'] = array_filter((array) $args['custom_attributes'], 'strlen');

    if ($args['maxlength']) {
        $args['custom_attributes']['maxlength'] = absint($args['maxlength']);
    }

    if (!empty($args['autocomplete'])) {
        $args['custom_attributes']['autocomplete'] = $args['autocomplete'];
    }

    if (true === $args['autofocus']) {
        $args['custom_attributes']['autofocus'] = 'autofocus';
    }

    if ($args['description']) {
        $args['custom_attributes']['aria-describedby'] = $args['id'] . '-description';
    }

    if (!empty($args['custom_attributes']) && is_array($args['custom_attributes'])) {
        foreach ($args['custom_attributes'] as $attribute => $attribute_value) {
            $custom_attributes[] = esc_attr($attribute) . '="' . esc_attr($attribute_value) . '"';
        }
    }

    if (!empty($args['validate'])) {
        foreach ($args['validate'] as $validate) {
            $args['class'][] = 'validate-' . $validate;
        }
    }

    $field           = '';
    $label_id        = $args['id'];
    $sort            = $args['priority'] ? $args['priority'] : '';
    $field_container = '<div class="mb-3 form-field woocommerce-form-field woocommerce-form-field-' . esc_attr($args['type']) . ' ' . esc_attr(implode(' ', $args['class'])) . '" id="' . esc_attr($args['id']) . '_field" data-priority="' . esc_attr($sort) . '">%s</div>';

    switch ($args['type']) {
        case 'country':
            $countries = $key === 'shipping_country' ? WC()->countries->get_shipping_countries() : WC()->countries->get_allowed_countries();

            if (1 === count($countries)) {
                $field .= '<strong>' . current(array_values($countries)) . '</strong>';
                $field .= '<input type="hidden" name="' . esc_attr($key) . '" id="' . esc_attr($args['id']) . '" value="' . current(array_keys($countries)) . '" ' . implode(' ', $custom_attributes) . ' class="country_to_state" readonly="readonly" />';
            } else {
                $field .= '<select name="' . esc_attr($key) . '" id="' . esc_attr($args['id']) . '" class="form-select country_to_state country_select ' . esc_attr(implode(' ', $args['input_class'])) . '" autocomplete="country" ' . implode(' ', $custom_attributes) . ' data-placeholder="' . esc_attr($args['placeholder']) . '">' . '<option value="">' . esc_html__('Select a country / region&hellip;', 'woocommerce') . '</option>';

                foreach ($countries as $ckey => $cvalue) {
                    $field .= '<option value="' . esc_attr($ckey) . '" ' . selected($value, $ckey, false) . '>' . $cvalue . '</option>';
                }

                $field .= '</select>';

                $field .= '<noscript><button type="submit" name="woocommerce_checkout_update_totals" value="' . esc_attr__('Update country / region', 'woocommerce') . '">' . esc_html__('Update country / region', 'woocommerce') . '</button></noscript>';
            }

            break;
        case 'state':
            /* Get country this state field is representing */
            $for_country = isset($args['country']) ? $args['country'] : WC()->checkout->get_value('billing_state' === $key ? 'billing_country' : 'shipping_country');
            $states      = WC()->countries->get_states($for_country);

            if (is_array($states) && empty($states)) {
                $field_container = '<div class="mb-3 form-field woocommerce-form-field woocommerce-form-field-' . esc_attr($args['type']) . ' ' . esc_attr(implode(' ', $args['class'])) . '" id="' . esc_attr($args['id']) . '_field" style="display: none">%s</div>';
                $field .= '<input type="hidden" class="hidden" name="' . esc_attr($key) . '" id="' . esc_attr($args['id']) . '" value="" ' . implode(' ', $custom_attributes) . ' placeholder="' . esc_attr($args['placeholder']) . '" readonly="readonly" data-input-classes="' . esc_attr(implode(' ', $args['input_class'])) . '"/>';
            } elseif (!is_null($for_country) && is_array($states)) {
                $field .= '<select name="' . esc_attr($key) . '" id="' . esc_attr($args['id']) . '" class="form-select state_select ' . esc_attr(implode(' ', $args['input_class'])) . '" ' . implode(' ', $custom_attributes) . ' data-placeholder="' . esc_attr($args['placeholder']) . '" data-input-classes="' . esc_attr(implode(' ', $args['input_class'])) . '">';
                $field .= '<option value="">' . esc_html__('Select an option&hellip;', 'woocommerce') . '</option>';

                foreach ($states as $ckey => $cvalue) {
                    $field .= '<option value="' . esc_attr($ckey) . '" ' . selected($value, $ckey, false) . '>' . $cvalue . '</option>';
                }

                $field .= '</select>';
            } else {
                $field .= '<input type="text" class="form-control input-text ' . esc_attr(implode(' ', $args['input_class'])) . '" value="' . esc_attr($value) . '"  placeholder="' . esc_attr($args['placeholder']) . '" name="' . esc_attr($key) . '" id="' . esc_attr($args['id']) . '" ' . implode(' ', $custom_attributes) . ' data-input-classes="' . esc_attr(implode(' ', $args['input_class'])) . '"/>';
            }

            break;
        case 'textarea':
            $field .= '<textarea name="' . esc_attr($key) . '" class="form-control input-text ' . esc_attr(implode(' ', $args['input_class'])) . '" id="' . esc_attr($args['id']) . '" placeholder="' . esc_attr($args['placeholder']) . '" ' . (empty($args['custom_attributes']['rows']) ? ' rows="2"' : '') . (empty($args['custom_attributes']['cols']) ? ' cols="5"' : '') . implode(' ', $custom_attributes) . '>' . esc_textarea($value) . '</textarea>';

            break;
        case 'checkbox':
            $field = '<label class="form-check-label" for="' . esc_attr($args['id']) . '">';
            $field .= '<input type="' . esc_attr($args['type']) . '" class="form-check-input input-checkbox ' . esc_attr(implode(' ', $args['input_class'])) . '" name="' . esc_attr($key) . '" id="' . esc_attr($args['id']) . '" value="1" ' . checked($value, 1, false) . ' ' . implode(' ', $custom_attributes) . ' /> ';
            $field .= $args['label'] . $required . '</label>';

            break;
        case 'text':
        case 'password':
        case 'datetime':
        case 'datetime-local':
        case 'date':
        case 'month':
        case 'time':
        case 'week':
        case 'number':
        case 'email':
        case 'url':
        case 'tel':
            $field .= '<input type="' . esc_attr($args['type']) . '" class="form-control input-text ' . esc_attr(implode(' ', $args['input_class'])) . '" name="' . esc_attr($key) . '" id="' . esc_attr($args['id']) . '" placeholder="' . esc_attr($args['placeholder']) . '"  value="' . esc_attr($value) . '" ' . implode(' ', $custom_attributes) . ' />';

            break;
        case 'hidden':
            $field .= '<input type="' . esc_attr($args['type']) . '" class="form-control input-hidden ' . esc_attr(implode(' ', $args['input_class'])) . '" name="' . esc_attr($key) . '" id="' . esc_attr($args['id']) . '" value="' . esc_attr($value) . '" ' . implode(' ', $custom_attributes) . ' />';

            break;
        case 'select':
            $field   = '';
            $options = '';

            if (!empty($args['options'])) {
                foreach ($args['options'] as $option_key => $option_text) {
                    if ('' === $option_key) {
                        // If we have a blank option, select2 needs a placeholder.
                        if (empty($args['placeholder'])) {
                            $args['placeholder'] = $option_text ? $option_text : __('Choose an option', 'woocommerce');
                        }
                        $custom_attributes[] = 'data-allow_clear="true"';
                    }
                    $options .= '<option value="' . esc_attr($option_key) . '" ' . selected($value, $option_key, false) . '>' . esc_attr($option_text) . '</option>';
                }

                $field .= '<select name="' . esc_attr($key) . '" id="' . esc_attr($args['id']) . '" class="form-select select ' . esc_attr(implode(' ', $args['input_class'])) . '" ' . implode(' ', $custom_attributes) . ' data-placeholder="' . esc_attr($args['placeholder']) . '">';
                $field .= $options;
                $field .= '</select>';
            }

            break;
        case 'radio':
            $label_id .= '_' . current(array_keys($args['options']));

            if (!empty($args['options'])) {
                foreach ($args['options'] as $option_key => $option_text) {
                    $field .= '<div class="form-check">';
                    $field .= '<input type="radio" class="form-check-input input-radio ' . esc_attr(implode(' ', $args['input_class'])) . '" value="' . esc_attr($option_key) . '" name="' . esc_attr($key) . '" ' . implode(' ', $custom_attributes) . ' id="' . esc_attr($args['id']) . '_' . esc_attr($option_key) . '"' . checked($value, $option_key, false) . ' />';
                    $field .= '<label for="' . esc_attr($args['id']) . '_' . esc_attr($option_key) . '" class="form-check-label radio ' . implode(' ', $args['label_class']) . '">' . $option_text . '</label>';
                    $field .= '</div>';
                }
            }

            break;
    }

    if (!empty($field)) {
        $field_html = '';

        if ($args['label'] && 'checkbox' !== $args['type']) {
            $field_html .= '<label for="' . esc_attr($label_id) . '" class="' . esc_attr(implode(' ', $args['label_class'])) . '">' . $args['label'] . $required . '</label>';
        }

        $field_html .= '<span class="woocommerce-input-wrapper">' . $field;

        if ($args['description']) {
            $field_html .= '<span class="form-text" id="' . esc_attr($args['id']) . '-description" aria-hidden="true">' . wp_kses_post($args['description']) . '</span>';
        }

        $field_html .= '</span>';

        $container_class = esc_attr(implode(' ', $args['class']));
        $container_id    = esc_attr($args['id']) . '_field';
        $field           = sprintf($field_container, $field_html);
    }

    $field = apply_filters('woocommerce_form_field_' . $args['type'], $field, $key, $args, $value);

    if ($args['return']) {
        return $field;
    } else {
        echo $field; // WPCS: XSS ok.
    }
}
```

## Customization and Hooks

### WooCommerce-Specific Hooks

```php
// Customize WooCommerce with Bootscore-specific hooks
function bootscore_woocommerce_customizations() {
    // Remove default WooCommerce styles
    add_filter('woocommerce_enqueue_styles', '__return_empty_array');
    
    // Custom breadcrumb
    remove_action('woocommerce_before_main_content', 'woocommerce_breadcrumb', 20);
    add_action('woocommerce_before_main_content', 'bootscore_custom_breadcrumb', 20);
    
    // Remove sidebar from shop pages
    remove_action('woocommerce_sidebar', 'woocommerce_get_sidebar', 10);
    
    // Custom product gallery
    remove_action('woocommerce_before_single_product_summary', 'woocommerce_show_product_images', 20);
    add_action('woocommerce_before_single_product_summary', 'bootscore_product_gallery', 20);
    
    // Custom add to cart button
    remove_action('woocommerce_single_product_summary', 'woocommerce_template_single_add_to_cart', 30);
    add_action('woocommerce_single_product_summary', 'bootscore_custom_add_to_cart', 30);
    
    // Custom checkout fields
    add_filter('woocommerce_checkout_fields', 'bootscore_custom_checkout_fields');
    
    // Custom cart item removal
    add_action('wp_ajax_woocommerce_remove_cart_item', 'bootscore_ajax_remove_cart_item');
    add_action('wp_ajax_nopriv_woocommerce_remove_cart_item', 'bootscore_ajax_remove_cart_item');
}
add_action('init', 'bootscore_woocommerce_customizations');

// Custom breadcrumb
function bootscore_custom_breadcrumb() {
    if (is_woocommerce()) {
        echo '<nav aria-label="breadcrumb" class="woocommerce-breadcrumb-wrapper mb-4">';
        echo '<ol class="breadcrumb">';
        
        $breadcrumbs = wc_get_breadcrumb();
        $last_key = array_key_last($breadcrumbs);
        
        foreach ($breadcrumbs as $key => $breadcrumb) {
            if ($key === $last_key) {
                echo '<li class="breadcrumb-item active" aria-current="page">' . esc_html($breadcrumb[0]) . '</li>';
            } else {
                echo '<li class="breadcrumb-item"><a href="' . esc_url($breadcrumb[1]) . '">' . esc_html($breadcrumb[0]) . '</a></li>';
            }
        }
        
        echo '</ol>';
        echo '</nav>';
    }
}

// Custom checkout fields
function bootscore_custom_checkout_fields($fields) {
    // Add Bootstrap classes to all fields
    foreach ($fields as $fieldset_key => $fieldset) {
        foreach ($fieldset as $field_key => $field) {
            $fields[$fieldset_key][$field_key]['class'][] = 'form-group';
            $fields[$fieldset_key][$field_key]['input_class'][] = 'form-control';
        }
    }
    
    // Custom field modifications
    $fields['billing']['billing_first_name']['class'] = array('col-md-6');
    $fields['billing']['billing_last_name']['class'] = array('col-md-6');
    $fields['billing']['billing_email']['class'] = array('col-md-6');
    $fields['billing']['billing_phone']['class'] = array('col-md-6');
    
    return $fields;
}
```

## Best Practices

### Performance Optimization

```php
// Optimize WooCommerce performance
function bootscore_optimize_woocommerce() {
    // Disable WooCommerce scripts on non-shop pages
    if (!is_woocommerce() && !is_cart() && !is_checkout()) {
        wp_dequeue_style('woocommerce-general');
        wp_dequeue_style('woocommerce-layout');
        wp_dequeue_style('woocommerce-smallscreen');
        wp_dequeue_script('wc-cart-fragments');
        wp_dequeue_script('woocommerce');
        wp_dequeue_script('wc-add-to-cart');
    }
    
    // Optimize cart fragments
    if (is_admin()) {
        wp_dequeue_script('wc-cart-fragments');
    }
}
add_action('wp_enqueue_scripts', 'bootscore_optimize_woocommerce', 99);

// Disable cart fragments on non-shop pages
function bootscore_disable_cart_fragments() {
    if (!is_woocommerce() && !is_cart() && !is_checkout()) {
        wp_dequeue_script('wc-cart-fragments');
    }
}
add_action('wp_enqueue_scripts', 'bootscore_disable_cart_fragments', 100);

// Optimize WooCommerce database queries
function bootscore_optimize_wc_queries() {
    // Remove unnecessary meta queries
    remove_action('woocommerce_product_query', 'wc_product_meta_lookup_update_queue');
    
    // Optimize product query
    add_filter('woocommerce_product_query_meta_query', function($meta_query, $query) {
        if (!is_admin() && $query->is_main_query()) {
            // Add only necessary meta queries
            $meta_query = array_filter($meta_query, function($query) {
                return isset($query['key']) && in_array($query['key'], ['_visibility', '_stock_status']);
            });
        }
        return $meta_query;
    }, 10, 2);
}
add_action('init', 'bootscore_optimize_wc_queries');
```

### Security Best Practices

```php
// WooCommerce security enhancements
function bootscore_woocommerce_security() {
    // Sanitize all WooCommerce inputs
    add_filter('woocommerce_process_checkout_field_', 'sanitize_text_field', 10, 1);
    
    // Add nonce verification to AJAX calls
    add_action('wp_ajax_bootscore_add_to_cart', function() {
        if (!wp_verify_nonce($_POST['nonce'], 'bootscore_wc_nonce')) {
            wp_die(__('Security check failed', 'bootscore'));
        }
        // Process add to cart
    });
    
    // Limit checkout attempts
    add_action('woocommerce_checkout_process', function() {
        $attempts = get_transient('checkout_attempts_' . WC()->session->get_customer_id());
        if ($attempts && $attempts > 5) {
            wc_add_notice(__('Too many checkout attempts. Please try again later.', 'bootscore'), 'error');
        }
    });
}
add_action('init', 'bootscore_woocommerce_security');
```

### Accessibility Improvements

```php
// Improve WooCommerce accessibility
function bootscore_woocommerce_accessibility() {
    // Add ARIA labels to product loops
    add_filter('woocommerce_loop_add_to_cart_link', function($link, $product) {
        $link = str_replace(
            'class="',
            'aria-label="' . sprintf(__('Add %s to cart', 'bootscore'), $product->get_name()) . '" class="',
            $link
        );
        return $link;
    }, 10, 2);
    
    // Improve form accessibility
    add_filter('woocommerce_form_field_args', function($args, $key, $value) {
        if ($args['required']) {
            $args['custom_attributes']['aria-required'] = 'true';
        }
        return $args;
    }, 10, 3);
    
    // Add skip links for screen readers
    add_action('woocommerce_before_main_content', function() {
        echo '<a class="sr-only sr-only-focusable" href="#main-content">' . __('Skip to main content', 'bootscore') . '</a>';
    }, 5);
}
add_action('init', 'bootscore_woocommerce_accessibility');
```

---

*This document provides comprehensive coverage of Bootscore's WooCommerce integration. The next document will cover child theme development and safe customization practices.*
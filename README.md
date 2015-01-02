Introduction
=================
In Morpha, we use wordpress in client works heavily. Mainly because urgent client needs and short project timeline. We believe, delivering working and good products on time is better than perfect product but delivered late.

This document is ever growing places where we log our snippets and tips when working on client projects.

# Table of Contents
* [The Basic](#the-basic)
* [Look and Feel](#look-and-feel)
* [Performance Tips](#performance-tips)
* [Must Install Plugins](#must-install-plugins)
* [Recomended Plugins](#recomended-plugins)
* [Snippet](#snippet)

# The Basic
If not using self-made theme, we use premium theme. To make working with this premium theme easier, we create custom child theme. There, we put all our snippets and all custom CSS related. We also put CSS overide there.

# Look and Feel
## Login Logo
We change wordpress login logo to clients one. We put client logo on `images/site-login-logo.png` and use this snippet:
```
function morpha_login_logo() { ?>
    <style type="text/css">
        body.login div#login h1 a {
            background-image: url(<?php echo get_stylesheet_directory_uri(); ?>/images/site-login-logo.png);
            padding-bottom: 30px;
        }
    </style>
<?php }
add_action( 'login_enqueue_scripts', 'morpha_login_logo' );
```

## Customizing Admin Menu
We don't want client have total control over their site. To accomplish this, we hide some menu. This hiding will active when we don't set our custom $_GET variable.
```
function morpha_remove_menus(){
  // remove_menu_page( 'index.php' );                   // Dashboard
  // remove_menu_page( 'upload.php' );                  // Media
  // remove_menu_page( 'edit.php?post_type=page' );     // Pages
  // remove_menu_page( 'edit-comments.php' );           // Comments
  remove_menu_page( 'themes.php' );                     // Appearance
  remove_menu_page( 'plugins.php' );                    // Plugins
  // remove_menu_page( 'users.php' );                   // Users
  remove_menu_page( 'tools.php' );                      // Tools
  remove_menu_page( 'options-general.php' );            // Settings

  // custom post, menu with address: edit.php?post_type=slug
  remove_menu_page( 'edit.php?post_type=slug' );
 
  // for menu with address: /wp-admin/admin.php?page=slug
  remove_menu_page( 'slug' );
}
// hide when our custom $_GET variable not set
if (!isset($_GET['morpha_debug'])) {
  add_action( 'admin_menu', 'morpha_remove_menus', 999 );
}
```

## Admin Footer Text
We change default wordress left and right footer to our custom text:
```
// left footer
add_filter('admin_footer_text', function() {
  return '&copy; My Favorite Client.';
}, 1);
// right footer
add_filter('update_footer', function() {
  return "For help, please contact Admin.";
},999);
```

# Performance Tips
## Hearbeat
In order to minimize load in client's server, we slow down wordpress heartbeat rate. Here is sample snippet to do that:
```
function morphaworks_heartbeat_settings( $settings ) {
    $settings['interval'] = 60; //Anything between 15-60
    return $settings;
}
add_filter( 'heartbeat_settings', 'morphaworks_heartbeat_settings' );
```

## JS and CSS Concantenation
Reducing number of request quickly improve our site speed. We use this plugin: [JS & CSS Script Optimizer](https://wordpress.org/plugins/js-css-script-optimizer). Usually we only use CSS concantenate as with the JS sometimes produces unexpected behaviour.

## Image optimization
Image is always the blocking factor in website loading. Before uploading image, we tell clients what is the best resolution for the image. We also provide client with image optimization app such as [ImageOptim](https://imageoptim.com) (on Mac) or [RIOT](http://luci.criosweb.ro/riot) for windows. Before uploading image, we suggest them to optimizes it first with those tools.

# Must Install Plugins
* [Google Analytics Dashboard for WP](https://wordpress.org/plugins/google-analytics-dashboard-for-wp) : Used to display google analytics data on admin dashboard
* [Duplicate Post](https://wordpress.org/plugins/duplicate-post) : We make easier for client to create custom content. With this plugin, client only need to duplicate our template post/custom post.

# Recomended Plugins
* [TablePress](https://wordpress.org/plugins/tablepress) : Used when we need to display table in wordpress site.
* [TablePress Extension: Responsive Tables](http://tablepress.org/extensions/responsive-tables) : To make TablePress responsive
* [WordPress Notification Bar](https://wordpress.org/plugins/wordpress-notification-bar) : Used when client want some alert notification to their visitor.

# Snippet
## Creating Dashboard Widget
We use custom dashboard widget to display message or link to hidden plugin settings page. If we just want to display it to admin user, we use `current_user_can( 'manage_options' )` on `wp_dashboard_setup` action.
```
function morpha_admin_configuration_widget() {

  wp_add_dashboard_widget(
                 'morpha_admin_configuration_widget',           // Widget slug.
                 'Konfigurasi',                                 // Title.
                 'morpha_admin_configuration_widget_function'   // Display function.
        );
}

function morpha_admin_configuration_widget_function() {
  echo 'hello';
}

add_action( 'wp_dashboard_setup', 'morpha_admin_configuration_widget' );
```

# WPSites

WPSites is a CLI that lets you quickly spin up WordPress sites based on templates that you've defined.

While it was designed for myself, a full-time plugin developer, it can be used to create WordPress sites for whatever your needs happen to be.

While building Independent Analytics, I'd find myself creating a new WordPress site only to configure it the same way I've configured hundreds of my disposable development sites before.

It was getting old.

Now I can run `wpsites create`, select the template I want to use, and be (almost) instantly redirected to the admin panel for the new site.

https://github.com/user-attachments/assets/b04fc4d8-7603-4729-b399-db08e4b1da6a

## Getting started

Install WPSites as a global composer package.

```
composer global require andrewmead/wpsites
```

This will place a `wpsites` PHAR in `~/.composer/vendor/bin/`. Make sure that this directory is in your path, otherwise commands such as `wpsites create` won't work.

From there, run `wpsites create` to generate a configuration file.

```
wpsites create
```

This will save the default configuration file to `~/.wpsites.php`.

**Note:** On some setups, WPSites won't be able to automatically create the configuration file. If this happens, it'll spit out a command you can run to fix that.

There are two things you'll need to configure in `~/.wpsites.php` before you can start using WPSites.

1. First, you'll need to provide a value for `sites_directory`. This value should be the path to the folder where you want your sites created. The default path of `$HOME/Herd` will work if you're using Herd, otherwise change this path to whatever directory your PHP server is serving up.
2. Second, you'll need to configure your database connection. Under the defaults associative array, set values for `database_host`, `database_username`, `database_password`, and `database_name`. Checkout out the options documentation below for details on the individual options.

You can test your configuration file by running `wpsites create` and selecting "Basic WordPress". This will attempt to create a new WordPress site in `sites_directory` and will connect it to the database you configured.

If everything's configured correctly, you should be redirected to the admin panel for your newly created WordPress site!

## Building your own templates

There are three top-level properties in `.wpsites.php`. There's `sites_directory`, `defaults`, and `templates`. This section covers `defaults` and `templates`.

Let's start with `templates`.

The `templates` property defines an array of WordPress site templates. The default configuration file comes with a few predefined templates, but you'll definitely want to create and customize your own.

You can create a new template by adding an associative array to the end of `templates`. The only thing you have to define is a template name using `name`. All other properties are optional.

Your configuration file as contains `defaults`. `defaults` is an associative array where you can define all of the same properties for a template (except for `name`).

So if you wanted ever template to use a specific version of WordPress, such as `5.9.10`, you could define `'wordpress_version' => '5.9.10'` under `defaults` instead of needing to define it for every template you create. If there a specific site where you want to use the latest version of WordPress instead, you could set `'wordpress_version' => 'latest'` on the one site template to override the default value.

Let's see this in action. Below is the default `.wpsite.php` configuration file. Take a quick look and continue on.

```php
<?php  
  
return [  
    'sites_directory' => '$HOME/Herd',  
  
    'defaults' => [  
        'wordpress_version'      => 'latest',  
        'database_host'          => '127.0.0.1:3306',  
        'database_username'      => 'root',  
        'database_password'      => null,  
        'database_name'          => null,  
        'admin_username'         => 'admin',  
        'admin_email'            => 'admin@example.com',  
        'admin_password'         => 'password',  
        'enable_multisite'       => false,  
        'enable_error_logging'   => true,  
        'enable_automatic_login' => true,  
        'theme'                  => 'twentytwentyfour',  
        'plugins'                => []  
    ],  

    'templates' => [  
        [
            'name' => 'Basic WordPress',  
        ],
        [
            'name'             => 'Basic Multisite WordPress',  
            'enable_multisite' => true,  
        ],
        [            
	        'name'    => 'Symlink plugin example',  
            'plugins' => [  
                '/path/to/symlink/folder',  
            ],
        ],
        [            
			'name'              => 'Bug recreation example',  
			'wordpress_version' => '5.9.10',
            'plugins'           => [  
                'independent-analytics',  
            ],
        ],
    ],
];
```

Under `defaults` is every option that you can define. All of these options can be defined on `defaults` and also defined on a site template.

You'll notice that the default value for `wordpress_version` is `latest`. The first 3 site templates don't override this value, so sites created with those templates will use the latest WordPress version.

The last site template overrides the default value for `wordpress_version` by settings it own version of `wordpress_version` to `5.9.10`.

So you can define a set of reasonable defaults and then override those values on a per template basis.

It's important to note that every default value in `defaults` above is the default value for the property. So in this case, the values in `defaults` are only there to show you all the options you can set. Since the default value for `wordpress_version` is already latest, it's a bit redundant. That means the config file above and the one below are actually the same.

```php
<?php  
  
return [  
    'sites_directory' => '$HOME/Herd',  
  
    'defaults' => [],  

    'templates' => [  
        [
            'name' => 'Basic WordPress',  
        ],
        [
            'name'             => 'Basic Multisite WordPress',  
            'enable_multisite' => true,  
        ],
        [            
	        'name'    => 'Symlink plugin example',  
            'plugins' => [  
                '/path/to/symlink/folder',  
            ],
        ],
        [            
			'name'              => 'Bug recreation example',  
			'wordpress_version' => '5.9.10',
            'plugins'           => [  
                'independent-analytics',  
            ],
        ],
    ],
];
```

While every option (except `name`) can be set as a default or on a site template, it doesn't always make sense to do so. A good example of this are the database connection options. It's likely that all the sites you create are going to use the same values. You might need to customize it from the defaults, but there's no need to do it on a per site basis copying the same values over and over.

## Supported template options

Every option can be set in one of two places. You can set it inside of `defaults` to serve as a default for all site templates, or you can set it inside of a site template in `templates` to have it be specific to just one template.
### WordPress version

Option: `wordpress_version`
Default: `'latest'`

The version of WordPress your site will use. Valid values include 'latest', 'nightly', or a WordPress version such as '6.0.0'.

### Database host

Option: `database_host`
Default: `'127.0.0.1:3306'`

The host (and port!) for your sites database connection.

### Database username

Option: `database_username`
Default: `'root'`

The username for your sites database connection.

### Database password

Option: `database_password`
Default: `null`

The password to for your sites database connection. A value of null should be used if there is no password.

### Database name

There is no option for defining the database name to create. Instead, the database name comes from the unique slug you pick when creating a WordPress site with `wpsites create`.

### Admin username

Option: `admin_username`
Default: `'admin'`

The username for the admin user.

### Admin email

Option: `admin_email`
Default: `'admin@example.com'`

The email for the admin user.

### Admin password

Option: `admin_password`
Default: `'password'`

The password for the admin user.

### Enable multisite

Option: `enable_multisite`
Default: `false`

Enable to create a multisite. This will also create a second site as part of the multisite network.

### Enable error logging

Option: `enable_error_logging`
Default: `true`

Enable to setup error logging. This sets WP_DEBUG to true, WP_DEBUG_LOG to true, and WP_DEBUG_DISPLAY to false.

### Enable automatic login

Option: `enable_automatic_login`
Default: `true`

Enable automatically logging in to the admin panel. This is by installing and configuring the automatic-login plugin.

### Theme

Option: `theme`
Default: `'twentytwentyfour'`

The slug of the WordPress theme to use. This must be a theme available on the WordPress theme repository. Symlinked themes are currently not supported.

### Plugins

Option: `plugins`
Default: `[]`

An array of WordPress plugins to install. The plug must either be a plugin available on the WordPress plugin repository, or an absolute path to a local plugin folder to symlink.  

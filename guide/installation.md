# Installation

## System Requirements

The Alphazento framework has a few system requirements:

- PHP >= 7.2
- Mcrypt PHP Extension
- OpenSSL PHP Extension
- Mbstring PHP Extension
- Tokenizer PHP Extension

**Please Install via composer:**

```bash
composer create-project alphazento/alphazento alphazento
```

## Configuration

Please refer to [Laravel Configuration](https://laravel.com/docs/5.0#configuration) and [Laravel Enviroment Configuration](https://laravel.com/docs/5.0/configuration#environment-configuration) to complete Alphazento initial configuration.

## Initialization

After DB connection is configurated in .env, you need to run command to initialize the system.

```bash
php artizan alphazento:init
```

The command will try to enable some necessary modules to the system.

And for more modules if you want to discover or enable them, these commands below will help:

```bash
php artizan package:discover
php artizan package:enable packagename
php artizan package:disable package_name
```

## Permissions

Alphazento may require some permissions to be configured: folders within storage and vendor require write access by the web server.

## Pretty URLs

### Apache

The framework ships with a public/.htaccess file that is used to allow URLs without index.php. If you use Apache to serve your Laravel application, be sure to enable the mod_rewrite module.

If the .htaccess file that ships with Laravel does not work with your Apache installation, try this one:

```js
Options +FollowSymLinks
RewriteEngine On

RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [L]
```

### Nginx

On Nginx, the following directive in your site configuration will allow "pretty" URLs:

```js
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```

## Vue Theme Env

### Install node, npm

### Install npm packages

Go to the application root folder, and run 'npm install'

```bash
cd /var/www/html
npm install

php artisan vueproject:prepare
npm run build "'--theme=Zento_VueTheme'"
```

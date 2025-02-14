# OS2Forms Drupal 8/9 project

[![Build Status](https://travis-ci.org/OS2Forms/os2forms8.svg?branch=master)](https://travis-ci.org/OS2Forms/os2forms8)

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Drupal 9 readiness.

At the current moment OS2Forms is ready to install or upgrade to Drupal 9 version.

PLAN THE UPGRADE TO DRUPAL 9 VERSION OF OS2FORMS **AS SOON AS POSSIBLE**

See detailed [steps to update your project to Drupal 9](https://github.com/OS2Forms/os2forms8/tree/develop#upgrade-from-drupal-8-to-drupal-9) below in this page.

Drupal 8 version is still maintained to get bugfixes.
However, due to EOL of Drupal 8 in November 2021 Drupal core will not get its security update.

If you still use Drupal 8 version of OS2Forms you can use following branches to get updates on bugfixes or other issues:
- `master-d8`
- `staging-d8`
- `develop-d8`

### Traditional

#### Installing

1. Clone the git repository
   ```sh
   git clone git@github.com:OS2Forms/os2forms8.git
   ```

2. Enter the newly created project directory
   ```sh
   cd os2forms8
   ```

3. Install dependencies
   ```sh
   composer install
   ```

4. Install drupal based on OS2Forms profile. Make sure you substitute the following variables:
   * db_pass
   * db_user
   * db_host
   * db_name
   * account_password
   * site_name
   ```sh
   drush si os2forms8 --db-url=mysql://db_user:db_pass@db_host/db_name --account-pass=account_password --site-name="site_name"
   ```

### With Docksal

#### Prerequisites

* [Docksal](https://docksal.io/)

#### Installing

1. Clone the git repository
   ```sh
   git clone git@github.com:OS2Forms/os2forms8.git
   ```

2. Enter the newly created project directory
   ```sh
   cd os2forms8
   ```

3. Start docksal environment
   ```sh
   fin start
   ```

4. Create local settings
   ```sh
   cp web/sites/example.settings.local.php web/sites/default/settings.local.php
   ```

5. Add file permission fix to settings.local.php. See https://docs.docksal.io/apps/drupal/#file-permissions
   ```php
   // web/sites/default/settings.local.php

   $settings['file_chmod_directory'] = 0777;
   $settings['file_chmod_file'] = 0666;
   ```

6. Here you can choose to install a standard OS2forms or include the OS2forms Forløb module with necessary configurations.
   ```sh
   # Option 1: Standard OS2forms test or development install
   fin rebuild-test
   ```
   
   ```sh
   # Option 2: OS2forms 2.1 med Forløb test or development install
   fin build-forloeb
   ```

7. Configure trusted hosts in settings.local.php (add the following if not present)
   ```php
   // web/sites/default/settings.local.php

   $settings['trusted_host_patterns'] = ['^os2forms8.docksal$', '^localhost$'];
   ```

You should now be able to browse to the application at `http://os2forms8.docksal`

## Deployment

These instructions will get you a copy of the project up and running on a live system.
For a more detailed description, you could look at the `web/core/INSTALL.txt` [here](./web/core/INSTALL.txt).

### Prerequisites

* A HTTP server such as [Apache](https://httpd.apache.org/) that supports PHP
  * Make sure that Apache vhost configured properly. [See requirements](https://www.drupal.org/docs/system-requirements/web-server-requirements#s-apache)
* A database service such as [MySQL](https://www.mysql.com/)
* PHP 7.4 with the following extensions enabled:
  * gd
  * curl
  * simplexml
  * xml
  * dom
  * soap
  * mbstring
  * zip
  * database specific extension such as the mysql extension
* [Composer](https://getcomposer.org/)
* [Drush launcher](https://github.com/drush-ops/drush-launcher)

### Installing

1. Clone the git repository
```sh
git clone git@github.com:OS2Forms/os2forms8.git
```
It assumes that you have configured your web-server to use path `[/vhost/path]/os2web8/web` as document root folder.

2. Enter the newly created project directory
```sh
cd os2forms8
```

3. Install dependencies without development dependencies
```sh
composer install --no-dev
```

4. Create local settings
   ```sh
   cp web/sites/default.settings.local.php web/sites/default/settings.local.php
   ```
5. Add databases settings to web/sites/default/settings.local.php

```
   $databases['default']['default'] = array (
     'database' => '[dbname]',
     'username' => '[dbuser]',
     'password' => '[dbpass]',
     'prefix' => '',
     'host' => '[dbhost]',
     'port' => '',
     'namespace' => 'Drupal\\Core\\Database\\Driver\\mysql',
     'driver' => 'mysql',
   );
```

6. Generate a salt string and insert it in web/sites/default/settings.local.php
   ```sh
   # Generate salt string - this will output a new salt string
   ./vendor/bin/drush php-eval 'echo \Drupal\Component\Utility\Crypt::randomBytesBase64(55) . "\n";'
   ```

   ```php
   // web/sites/default/settings.php
   $settings['hash_salt'] = ''; // Insert the generated salt string here
   ```

7. Configure trusted hosts in web/sites/default/settings.local.php.
   For more information on how to write this, see the section for [Trusted Host settings](https://www.drupal.org/docs/installing-drupal/trusted-host-settings)
   in the official Drupal installation guide.
   ```php
   // web/sites/default/settings.local.php

   $settings['trusted_host_patterns'] = [''];
   ```
8. Install Drupal

   Using drush command:
   ```
   # To install default OS2Forms
   drush si os2forms8 --account-pass=account_password --site-name="OS2Forms"
   
   # To install OS2Forms 2
   drush si os2forms_forloeb_profile --account-pass=account_password --site-name="OS2Forms med forløb"
   ```

   Or visit the url for the os2forms application and follow the instructions
   * Select the os2forms install profile for a default os2forms installation

9. Enable OS2Forms modules
   ```sh
   ./vendor/bin/drush en os2forms, os2forms_nemid, os2forms_dawa, os2forms_sbsys
   ```

## Development

During development display of all PHP warnings and errors should be enabled by
adding

```php
<?php
// settings.local.php.
error_reporting(E_ALL);
ini_set('display_errors', TRUE);
ini_set('display_startup_errors', TRUE);
$config['system.logging']['error_level'] = 'verbose';
```

to `setttings.local.php`. See [Enable Drupal 8 backend errorlog/debugging
mode](https://www.drupal.org/forum/support/post-installation/2018-07-18/enable-drupal-8-backend-errorlogdebugging-mode)
for further details.

## Upgrade from Drupal 8 to Drupal 9

Upgrading process from 8 to 9 version is not very different from update Drupal from minor versions in Drupal 8.
However, there is some important steps you should be aware of.

It's recommended to do upgrade first on **TEST** environment before upgrading **PROD** environment.

See following steps to get your project upgraded to Drupal 9.

### 1. System requirements
Before upgrade to Drupal 9 be sure that your server cover [system requirements for Drupal 9](https://www.drupal.org/docs/understanding-drupal/how-drupal-9-was-made-and-what-is-included/environment-requirements-of-drupal-9)
The most important of them are:
- [PHP version](https://www.drupal.org/docs/understanding-drupal/how-drupal-9-was-made-and-what-is-included/environment-requirements-of-drupal-9#s-php-version-requirement)
- [Mysql db version](https://www.drupal.org/docs/understanding-drupal/how-drupal-9-was-made-and-what-is-included/environment-requirements-of-drupal-9#s-database-backend-and-other-hosting-requirements)

### 2. Making dumps

Before upgrade make sure you get DB dump to be able to roll back your project to stable state in case of unsuccessful upgrade

### 3. Update code

In order to upgrade your project to Drupal 9 get the latest version of `develop`, `staging` or  `master` branch.

Download 3rd party dependencies via composer:

```shell
composer install
```

### 4. Rebuild cache and run database update.

Run following commands:

```shell
drush cr
drush updb
```

### 5. Testing
Test essential functionality in your OS2Forms project.
OS2Forms has general test cases you can go through to be sure that your project works as it should.
It includes general Drupal test cases and additional test cases that cover basic OS2Forms functionality.
Test cases:
- Frontpage work
- User can loging
- User can create webform
- Webform can be submitted
- Nemid field can be added
- User can login via nemid and see prefilled data in nemid fields

NOTE: This list might not include some cases that is crucial for you installation.
If you think that this list has missing test cases that are crucial for OS2Forms in general, feel free to [submit an issue](https://github.com/OS2Forms/os2forms8/issues) and describe the issue you are missing there.

## Contributing

OS2Forms projects is open for new features and os course bugfixes. If you have any suggestion, or you found a bug in project, you are very welcome to create an issue in github repository issue tracker. For issue description there is expected that you will provide clear and sufficient information about your feature request or bug report.

### Development environment

For development purposes there has been included development environment driven
by [Docksal](https://docksal.io/). You can find all settings related to in
`/.docksal` folder.

See official manual on [how to install docksal](https://docksal.io/installation) on your local
development machine.

Since you have installed docksal it's easy to get installed default installation.
Use following commands:
```
# Clone project if you didn't do it yet
git clone git@github.com:OS2Forms/os2forms8.git os2forms8-dev
cd os2forms8-dev
# Start docksal environment
fin start
```
As result, you will get URL like `http://os2forms8-dev.docksal` that is going to
 be used for access os2forms application.

Run `fin help` to see commands you may need. You see [more information about docksal `fin` command](https://docs.docksal.io/fin/fin/)

Most useful commands:
- `fin start/stop/restart` - start/stop/restart environment
- `fin bash` - get ssh access in CLI container
- `fin drush [comnnand]` - run drush command from host mashine in CLI container
- `fin composer [comnnand]` - run composer command from host mashine in CLI container
- `fin exec 'command'` - run any command from host machine in CLI container

### Install default installation

To get default installation just run `fin rebuild-test` command. Docksal will
create default os2forms installation for you.

Before start using it you need to add trusted hosts settings. See next section.

### Upload existing db
If you have existing database you want to upload and use, then you have to
configure Drupal settings (see section above). NOTE: It's recommended to add
settings `settings.local.php` file.

Default db service credentials:

```
$databases['default']['default'] = array (
  'database' => 'default',
  'username' => 'root',
  'password' => 'root',
  'prefix' => '',
  'host' => 'db',
  'port' => '',
  'namespace' => 'Drupal\\Core\\Database\\Driver\\mysql',
  'driver' => 'mysql',
);
```

Use `fin bash` or `fin drush [command]` to upload your database

You also need to add the rest drupal settings such as salt, sync/tmp folders,
trusted hosts
```
$settings['trusted_host_patterns'] = ['^os2forms8-dev.docksal$', '^localhost$'];
```

### Code review policy
See [OS2Forms code review policy](https://github.com/OS2Forms/docs#code-review)

### Git name convention
See [OS2Forms git name convention](https://github.com/OS2Forms/docs#git-guideline)

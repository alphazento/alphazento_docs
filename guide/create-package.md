## Package

[edit on github](https://github.com/alphazento/alphazento-docs/blob/master/create_package.md)

A package is an module added to your application for enhancement which includes routes, controllers, views, vue components and configuration. Packages are created to manage your large applications into smaller modules. In the Alphazento, we have created plenty of packages at path `packages/alphazento/`. You can find a basic tree-structure of package below:

```php
- ACME/
  - PackageName/
    - Config/
      - Admin.php
    - Console/
    - Events/
    - Listeners/
    - Http/
      - Controllers/
        - Api/
        - Web/
      - Middleware/
      - Requests/
    - routes/
        - api.php
        - web.php
    - Model/
    - Providers/
      - Facades/
      - Plugin.php
    - Services/
    - resources/
      - assets/
      - i18n/
      - views/
      - vue/
        - components/
        - pages/
        - _backend.asm.js
        - _frontend.asm.js
    - setup
      - database/
        - 0.0.1/
    - _zento_assembly.php
```

## Using Alphazento Package Generator To Create Package <a id="to-create-package"></a>

You need to install Alphazento Package Generator with the help of composer. If you have not installed then you can check [here](https://github.com/alphazento/alphazento-package-generator#3-installation).

So, we are assuming that you have installed Alphazento Package Generator.

Now, use this command.

```php
php artisan make:package ACME_SamplePackage
```

- If somehow package directory already present then you need to check and remove the directory first.

Now check your package directory, everything is setup for you.

### Start Your First Package

After running the command, your package folder is ready for you.
Now let's step by step to finish your first package.

#### Step-1 The Plugin.php file

- Inside **Providers** folder there's a file named **Plugin.php**

  **Example** – Plugin.php

  - The Service Provider consists of two methods.

    - [Boot Method](https://laravel.com/docs/7.x/providers#the-boot-method){: target="\_blank"}

    - [Register Method](https://laravel.com/docs/7.x/providers#the-register-method){: target="\_blank"}

  - By default, after running **make:package** command, we present the sample **Plugin.php** file already.

    ```php
    <?php
    namespace ACME\SamplePackage\Providers;

    use Illuminate\Support\ServiceProvider;

    /**
    * SamplePackage service provider
    *
    * @author    Yongcheng Chen <yongcheng.chen@live.com>
    * @copyright 2020 Alphazento (https://www.alphazento.com)
    */
    class Plugin extends ServiceProvider
    {

        /**
        * Register services.
        *
        * @return void
        */
        public function register()
        {

        }

        /**
        * Bootstrap services.
        *
        * @return void
        */
        public function boot()
        {

        }
    }
    ```

    - **If you don't have any service need to be registered/booted in your module. You can delete this file.**

#### Step-2 \_zento_assembly Settings

- Now, to register your module, let your module be able to loaded by Alphazento framework. We need to edit the **\_zento_assembly.php** inside your package.

  ```php
    <?php
    return [
        "ACME_SamplePackage" => [
            "version" => "0.0.1",
            //'vuetheme_type' => 'frontend', //if your package is a theme package. frontend or backend
            //"vue_component" => true, //if you have custome vue component defined.
            "commands" => [], //Extended command classes
            "providers" => [
                "\\ACME\\SamplePackage\\Providers\\Plugin",
            ],
            "listeners" => [],
            "middlewares" => [],
            "middlewaregroup" => [],
            //"depends" => ["ACME_SamplePackage1"],
        ],
    ];
  ```

  - [Zento Assembly](https://laravel.com/docs/7.x/providers#the-boot-method){: target="\_blank"}
  - Now, the package should be able to found when you run command line:
    ```bash
    php artisan package:discover
    ```

#### Step-3 Prepare Database Migration Resources

- Now, we need to prepare database resources for the package.
  - In the folder **setup\database** we put the Laravel database migration script in the specified package version's(witch defined in **\_zento_assembly.php**) folder.
  - For current package version **0.0.1**, so go to folder **setup\database\0.0.1**, we add migration file by running the command:
    ```php
    php artisan package:make-migration ACME_SamplePackage sample_package_table
    ```
- Refer to [Laravel Migration Concept](https://laravel.com/docs/7.x/migrations){: target="\_blank"}

#### Step-4 Enable Your Package

- Now, the package is ready to load to the application.
- By running command:

  ```php
  php artisan package:enable ACME_SamplePackage
  ```

  - First it will create a table in you database.
  - Then the module will be registered into the database, and your package is ready to use.

- Now, when you run the command **package:discover**, your package will shows as enabled.

#### Step-4 Controller

- By running command:

  ```php
  php artisan make:controller ACME_SamplePackage WebController
  ```

  Or you can create API controller like this:

  ```php
  php artisan make:controller ACME_SamplePackage ApiController --api
  ```

- The web controller will be in the folder **Http/Controllers/Web**
- The API controller will be in the folder **Http/Controllers/Api**

- **example**

  - Now we create a web controller

  ```php
  php artisan make:controller ACME_SamplePackage WebController
  ```

  - Then add a _**index**_ function

  ```php
    <?php

    namespace ACME\SamplePackage\Http\Controllers\Web;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;

    class WebController extends Controller
    {
        public funciton index() {
            return view('acme-sample-package.index');
        }
    }
  ```

#### Step-4 Setup routes

- **For routes**: Create **routes** folder of your package. Base on your package, available routes files are **_admin-api.php_**, **_admin-web.php_**, **_frontend-api.php_**, **_frontend-web.php_**.

  - **admin-api.php**: This file is for the admin API routes. Add below codes to this file,

    ```php
    <?php
        Route::group(['middleware' => ['backend', 'auth:api']], function () {

            // all admin api routes will place here

        });
    ```

  - **admin-web.php**: This file is for the API routes. Add below codes to this file,

    ```php
    <?php
        Route::group(['middleware' => ['backend', 'web']], function () {

            // all admin web routes will place here

        });
    ```

  - **frontend-api.php**: This file is for the admin API routes. Add below codes to this file.

  ```php
  <?php
      Route::group(['middleware' => ['cors', 'guesttoken', 'auth:api']], function () {

          // all frontend api routes will place here

      });
  ```

  - **frontend-web.php**: This file is for the API routes. Add below codes to this file,

  ```php
  <?php
      Route::group(['middleware' => ['web']], function () {

          // all frontend web routes will place here

      });
  ```

- **example**

  - Now we _**frontend-web.php**_ inside **routes** folder. Add below codes to this file,

  ```php
    Route::group(
    [
        'namespace' => '\ACME\SamplePackage\Http\Controllers\Web',
        'middleware' => ['web'],
        'prefix' => '/',
    ], function () {
        Route::get(
            '/samplepackage',
           'WebController@index'
        );
    }
  );

  ```

* All these routes will be auto loaded by Zento_PackageManager.

#### Step-5 Package View

- **For views**: inside the package, the **resources** folder has a sub folder **views**. We will add a sub folder _**acme-sample-package**_ and inside the sub folder, add a view file.

  ```php
  - Resources/
    - assets/
    - i18n/
    - views/
      - acme-sample-package
    - vue/
  ```

  - Inside each folder i.e. **admin** and **shop** create a file named as **index.blade.php**. Add some data to **index.blade.php**,

    - \***\*acme-sample-package/index.blade.php\*\***

      ```php
      <h2>Hello World! acme-sample-package </h2>
      ```

  - Now try to access 'http://your-domain/samplepackage', you should be able to visit the page.

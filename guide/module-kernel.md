# Alphazento Zento_Kernel Module

Zento_Kernel extends some important features from Laravel.
It's under vendor/zento/src/Kernel

## Services

### PackageManager

PackageManager service is the key service to support alphazento modular.
It handles packages by package's configuration, loads packages by their package's dependencies, actives/inactives packages, manages their code and database version.
And cache these configurations to local cache.

### ThemeManager

Theme Manager bring multiple theme package support to the system. And these theme package even can extens to a parent package.

When extend a parent theme package, you don't need to extends all theme resources, you just only extend/overwrite some resources and it will try to load your blade view by fallback to view paths.

#### Define a theme package

In the file "\_zento_assembly.php", add node "theme".

**Zento_BladeTheme** is the root theme we already provided. If you define any other theme, you need to extends from 'Zento_BladeTheme', or other theme which extends from 'Zento_BladeTheme'

```js

<?php
return [
    "Zento_VueTheme" => [
        'version' => '0.0.1',
        'theme' => 'Zento_BladeTheme',
```

##### Theme mounted callback

When a Theme is mounted to the application, a callback **whenSetTheme** will be called.
You can defined a callback in your theme, so when your theme is loaded, it will prepare some extra logic for your theme.

For example, we define a theme **Zento_VueTheme** and it needs to overriwte some controller logic:

```js

<?php

namespace Zento\VueTheme\Providers;

use Auth;
use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Str;
use Storage;
use Zento\BladeTheme\Facades\BladeTheme;
use Zento\BladeTheme\Http\Controllers\CatalogController;
use Zento\BladeTheme\Http\Controllers\GeneralController;
use Zento\Kernel\Facades\PackageManager;
use Zento\Kernel\Facades\ThemeManager;
use Zento\StoreFront\Consts as StoreFrontConsts;
use Zento\VueTheme\Consts as VueThemeConsts;
use Zento\VueTheme\Http\Controllers\CatalogController as VueThemeCatalogController;
use Zento\VueTheme\Http\Controllers\GeneralController as VueThemeGeneralController;


class Plugin extends ServiceProvider
{
    public function register()
    {
        public function register()
        {
            $this->app->bind(CatalogController::class, VueThemeCatalogController::class);
            $this->app->bind(GeneralController::class, VueThemeGeneralController::class);

            ThemeManager::whenSetTheme('Zento_VueTheme', function ($app) {
                $viewLocation = sprintf('%s/notifications', PackageManager::packageViewsPath('Zento_VueTheme'));
                $app['view']->addNamespace('notifications', $viewLocation);
            });
        }
    }
```

### EventsManager

Laravel provides a very good event and listener system. But for an event it's listeners are called base on when the listener is registered to the system. If a package are registered early and the listener maybe called earlier.

alphazento/zento extends Laravel Event and Listener, you just need to configure your listener in your composer.json, and each listener with it's sequence defined, then these listener will be called by your defined sequence.

Please see "Event And Sequence Listener" below.

### ShareBucket

ShareBucket allow you share data between different instance.

## Features

### Dynamic Attribute

Zento Kernel package bring dynamic Attribute feature to Eloqument. You can easily extends attributes to an exist eloqument without change model's database table.

Dynamic Attribute has two types:

#### Single Attribute

attribute only has a value

#### Option Attribute

attribe has multiple option values.

#### Create a dynamic Attribute for a model

DanamicAttributeFactory::createRelationShipORM($modelClassName, $dynamicAttributeName, $optionArray, $isSingleOrOptions)

By calling this function, it will generate a dynamic Attribute table for the model.
DanamicAttributeFactory::createRelationShipORM(\namespace\class::class,
'attribute', ['char', 32], true);

##### Extend withDynamicSingleAttribute and withDynamicOptionAttribute to retrieve dynamic attribute

You can use withDynamicSingleAttribute(single), or withDynamicOptionAttribute(option)
\$collection = \Zento\Kernel\TestModel::where('id', 1)->withDynamicSingleAttribute('new_column')->first();

##### listDynamicAttributes

This function will list all dynamic attributes for an exists model

##### How to use it

If you want your Eloquemnt Model has ability of dynamic Attributes, you may do so using the Zento\Kernel\Booster\Database\Eloquent\DynamicAttribute\DynamicAttributeAbility trait. This trait is imported by default on hasOneDyn, hasManyDyns functions and they work with Zento\Kernel\Booster\Database\Eloquent\DynamicAttribute\Builder to provide withDynamicSingleAttribute and withDynamicOptionAttribute:

```js

class TestModel extends \Illuminate\Database\Eloquent\Model {
use \Zento\Kernel\Booster\Database\Eloquent\DynamicAttribute\DynamicAttributeAbility;
}

DanamicAttributeFactory::createRelationShipORM(TestModel::class,
'new_column', ['char', 32], true);
DanamicAttributeFactory::createRelationShipORM(TestModel::class,
'new_column1', ['char', 32], false);

TestModel::listDynamicAttributes(); //list all dynamic Attributes

$collection = TestModel::where('id', 1)->withDynamicSingleAttribute('new_column')->withDynamicOptionAttribute('new_column1')->first();
```

### Config extends

Zento\Kernel package extends the original Laravel config feature. The original config only load config from config's folder. With this extends, we create a "config_items" table in the database, and "Zento\Kernel\Booster\Config\ConfigInDB\ConfigRepository" as the default database config repository.

### Event And Sequence Listener

If a package defined a listener for an event "EVENT-CLASS-NAME":

```js
"listeners" : {
        "EVENT-CLASS-NAME" : {
            "10":"OBSERVER-CLASS-NAME1"
            }
    },
```

And another package defined a listener for the same event "EVENT-CLASS-NAME":

```js
"listeners" : {
        "EVENT-CLASS-NAME" : {
            "5":"OBSERVER-CLASS-NAME2"
            }
    },
```

Then the call sequence will be:
OBSERVER-CLASS-NAME2, OBSERVER-CLASS-NAME1

#### Base Event Class and Base Listener Class

Zento\Kernel\Booster\Events\BaseEvent
Zento\Kernel\Booster\Events\BaseListener

Please extends these base classes that we will gather the event will be handled by which listeners and their call sequence details.

        // DanamicAttributeFactory::createRelationShipORM(\Zento\Kernel\Booster\Config\ConfigInDB\ORMModel\ConfigItem::class,
        //     'tcol', ['char', 32], true);
        // // $collection = DanamicAttributeFactory::withDynamicSingleAttribute(\Zento\Kernel\Booster\Config\ConfigInDB\ORMModel\ConfigItem::where('key', 'test'),
        // //     'new_column')->get();
        // DanamicAttributeFactory::createRelationShipORM(\Zento\Kernel\TestModel::class,
        //     'new_column', ['char', 32], true);
        // DanamicAttributeFactory::createRelationShipORM(\Zento\Kernel\TestModel::class,
        //     'new_column1', ['char', 32], false);

        // \Zento\Kernel\TestModel::listDynamicAttributes();
        // $collection = \Zento\Kernel\TestModel::where('id', 1)->withDynamicSingleAttribute('new_column')->withDynamicOptionAttribute('new_column1')->first();
        // // $collection = \Zento\Kernel\TestModel::where('id', 2)->withDynamicSingleAttribute('new_column')->first();
        // // echo '<pre>';
        // DanamicAttributeFactory::single($collection, 'new_column')->new('OKOK');
        // DanamicAttributeFactory::option($collection, 'new_column1')->new('this is a test');
        // DanamicAttributeFactory::option($collection, 'new_column1')->setValues(['this is a test', 'newvalue']);

        // $collection = \Zento\Kernel\TestModel::where('id', 1)->withDynamicOptionAttributeet()->first();
        // var_dump($collection->attributesets->attributes);die;

### Command Lines

This package extends some command lines:

#### 1) make:package

#### 2) package:enable

```bash

artisan package:enable VendorName_PackageName
```

It will register the package to the system, so it's provider, middleware, middlewaregroup, command lines and event listeners will be registered, then you can use these resources.

If a Zento package is not registered, those resource(list above) will not able to be used. But of cause, you still can use it's classes.

#### 2) package:disable

Disable package.(but it's classes still can be used.

```bash

artisan package:disable VendorName_PackageName
```

#### 3) package:discover

This command line is provide from original Laravel, but we extend it to discover the packages that you created in **mypackages**.
And it also merge and cache configuration items in your package's **composer.json** file.

```bash

artisan package:discover
```

#### 4) listeners

Zento Kernel has extended original Laravel Event/Listener. Original Laravel Event's Listener doesnpt support control listener call Sequence, but many time your listener must be called by a special Sequence.
By running the command:

```bash

php artisan listeners
```

It will list your package listening to events and these listeners calling Sequence.

## Useful Models

### class InnerApiClient

### trait SelfMorphModel

### trait DynamicAttributeAbility

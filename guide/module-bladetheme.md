# BladeTheme Module

Zento_BladeTheme is an empty theme package. It provides some basic route interface to the application.

And it also provides a command 'php artisan vueproject:prepare' which analyze all packages has vue project or components, and generate a mix task, so you can compile the vue project and provide a vue theme for the application.

## Vue Theme Package

A Vue theme package need to define node "vuetheme_type' in "\_zento_assembly.php", it can be "frontstore" or "backend".
Then command line 'php artisan vueproject:prepare' will analyze all it's dependcy packages and gernerate ".\_app.support.js" file under folder 'resources/vue'.

```js
<?php
return [
    "Zento_VueTheme" => [
        'version' => '0.0.1',
        'theme' => 'Zento_BladeTheme',
        'vuetheme_type' => 'frontstore',
```

## Vue Components Package

Some times your pacakge is not a theme, but it will provide some vue components to the application. So application can use them directly.

Just define node "vue_component" in "\_zento_assembly.php"

```js

<?php
return [
    "Zento_Catalog" => [
        "version" => "0.0.1",
        "vue_component" => true,
```

Then register the vue components in the file "resources/vue/\_backend.asm.js"(for backend) or "resources/vue/\_frontstore.asm.js"(for frontend)

```js
import CategoryRoutePage from "./pages/catalog/category/RoutePage";
import ProductRoutePage from "./pages/catalog/product/RoutePage";

export default {
  components: {
    "category-treeview": "components/CategoryTreeview.vue",
    "z-dyna-attr-model-editor": "components/DAModelEditor.vue",
  },
  routes: [
    {
      name: "catalog.category",
      path: "/admin/catalog/category",
      component: CategoryRoutePage,
      meta: {
        title: "Catalog Category",
      },
    },
    {
      name: "catalog.product",
      path: "/admin/catalog/product",
      component: ProductRoutePage,
      meta: {
        title: "Catalog Product",
      },
    },
  ],
};
```

So in your vue application scope(frontend or backend), you can use them.

## Vue Theme Compilation

```bash

cd /var/www/html
php artisan vueproject:prepare
npm run build "'--theme=Zento_VueTheme'"
```

## Inner Api Request function requestInnerApi

```php
public function requestInnerApi($method, $url, $data = [], $headers = [])
```

You can use this function when you need to request an api resource internel.

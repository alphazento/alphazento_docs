# RouteAndRewriter Module

Zento_RouteAndRewriter provides RouteAndRewriterService which allow to define your rewrite engine, the engine will handle the request and try to find the route.
And it also allow you to add/del your self url rewrite rule.

## Functions

### 1. addRewriteRule

Add or update a new url rewrite rule and store it to the database table 'url_rewrite_rule'.

```php
 /**
     * add new rewrite rule
     *
     * @param string $reqUri
     * @param string $toUri
     * @param integer $status_code
     * @param string $description
     * @return $this
     */
    public function addRewriteRule(string $reqUri,
        string $toUri,
        $status_code = 302,
        string $description = '');
```

**Please make sure the \$toUri must be the exists route.**

### 2. delRewriteRule

Delelte a url rewrite rule from the database table 'url_rewrite_rule'.

### 3. appendRewriteEngine

Append a url rewrite engine to route finder.

For example, in module 'Catalog' the class \Zento\Catalog\Model\ProductUrlRewriteEngine defines a rewrite engine:

```php
<?php

namespace Zento\Catalog\Model;

use Illuminate\Support\Str;
use Zento\Catalog\Model\ORM\Product;
use Zento\RouteAndRewriter\Facades\RouteAndRewriterService;
use Zento\RouteAndRewriter\Model\UrlRewriteRule as RuleModel;

class ProductUrlRewriteEngine extends \Zento\RouteAndRewriter\Engine\UrlRewriteEngineAbstract
{
    public function findRewriteRule(string $url)
    {
        $url = Str::endsWith($url, '.html') ? substr($url, 0, -5) : $url;
        if ($product = Product::where('url_key', '=', $url)
            ->first(['id'])) {
            $rule = new RuleModel();
            $rule->req_uri = $url;
            // $rule->to_uri = '/product/' . $product->id;
            $rule->to_uri = RouteAndRewriterService::buildToUri('product', $product->id);
            $rule->status_code = 200;
            return $rule;
        }
        return false;
    }
}
```

And the engine is appended to route finder when boot the catalog provider.
See the class: \Zento\Catalog\Providers\Plugin

```php
<?php

namespace Zento\Catalog\Providers;

use Illuminate\Support\ServiceProvider;
use Zento\Catalog\Services\CategoryService;
use Zento\Catalog\Services\ProductService;
use Zento\Kernel\Facades\PackageManager;

class Plugin extends ServiceProvider
{
    ...
    public function boot()
    {
        if (!$this->app->runningInConsole()) {
            $this->app->booted(function ($app) {
                $rewriteSvc = $app['routeandrewriter_svc'];
                $rewriteSvc->appendRewriteEngine(new \Zento\Catalog\Model\CategoryUrlRewriteEngine());
                $rewriteSvc->appendRewriteEngine(new \Zento\Catalog\Model\ProductUrlRewriteEngine());

                //uri builder for web
                $rewriteSvc->setUriBuilder('category', function ($id) {
                    return sprintf('/categories/%s', $id);
                });
                $rewriteSvc->setUriBuilder('product', function ($id) {
                    // return route('product', ['id' => $id]);
                    return sprintf('/products/%s', $id);
                });
            });
        }
    }
    ...
}
```

# Zento_Acl Module

Zento_Acl works with Zento_Passport to provide API resource access control.

## AclRoute Item

Zento_Acl analyzes the API routes definition and also the doc block of class Controller's functions to generate the AclRoute permission items.

The key paramters of permission item are: Http Method, Http Route

### How to doc a founction of controll to support ACL

- @no-acl as default, if don't put "@no-acl" in the doc block, the function auth
- @group will group the api resource to same group id.

```php
    /**
     * Retrieves user details
     * @authenticated
     * @group ACL Management
     * @urlParam scope required options of ['administrator', 'customer']. Indicate backend or frontend. Example:administrator
     * @urlParam id required number user's id Example:1
     * @responseModel \Zento\Acl\Model\Auth\Administrator
     */
    public function user()
    {
        if ($id = Route::input('id')) {
            $model = $this->getUserModel();
            if ($user = $model::find($id)) {
                return $this->withData($user);
            }
        }
        return $this->error(404);
    }
```

By running command "php artisan acl:sync" and it will analyze the ACL permissions and store to the DB.

```bash
php artisan acl:sync
```

## AclRole

ACL Role will contains many pemission items.
You can manage the role and acl route link on the admin panel.

## AclRoleUser

ACL users will have differect roles
You can manage the role and user link on the admin panel.

## Root Role

The module defines a root role, which has all permissions.

## Commands

### Add Administrator

```bash
php artisan acl:addadmin {email : email address} {--password=false} {--root}
```

### Set Administrator Password

```bash
php artisan acl:password {email : email address} {password : password}
```

### Disable User

```bash
php artisan acl:user:disable {email : email address}
```

### Enable User

```bash
php artisan acl:user:enable {email : email address}
```

### Sync ACL Route

```bash
php artisan acl:sync
```

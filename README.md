# Nova issue

## Run project

For quick launch you can use [Herd](https://herd.laravel.com/).


```bash
git clone <your-repo-url>
cd <your-repo-directory>

composer install

php artisan key:generate

php artisan migrate

php artisan db:seed
```

## Error Reproduction
1. Login and go to the user resource.
2. Create a minimum of 3 users.
3. Open the file `vendor/laravel/nova/src/Http/Requests/ActionRequest.php` and replace the `toQueryWithoutScopes` method with the code below:

```php
public function toQueryWithoutScopes()
{
    return tap($this->newQueryWithoutScopes(), function ($query) {
        $resource = $this->resource();
        $query->with($resource::$with);

        dd($this->allResourcesSelected(), $this->selectedResourceIds()->count(), $this->selectedResourceIds());
        if (! $this->allResourcesSelected() && $this->selectedResourceIds()->count() === 1) {
            $resource::detailQuery($this, $query);
        } else {
            $resource::indexQuery($this, $query);
        }
    });
}
```

4. Select 2 users (not all) and apply batch action "test".

**Actual Result:**
```php
$this->selectedResourceIds()->count() returns 1
```

**Expected Result:**
```php
$this->selectedResourceIds()->count() should return 2
```

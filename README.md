<img src="https://media4.giphy.com/media/ES4Vcv8zWfIt2/giphy.gif" width="400">

## PREREQUISITE

- [x] Apache24
- [x] php7 or higher
- [x] NodeJs (Recommended)
- [x] MySQL
- [x] PhpMyadmin
<!-- make it links -->

## Installation of Laravel

```
composer create-project laravel/laravel --prefer-dist Larabscs
```
>`--prefer-dist` refers to the systems' php version
```
cd Larabscs
```

1. create a database in local phpmyadmin name `car_system`
2. create an .env file
3. add the following:

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=car_system #???
DB_USERNAME=root
DB_PASSWORD=admin #???
```
<!-- LINK - click here to advance env connection (database and other `HRDSQL`) -->
## Run our laravel boiler plate if has no errors

```
run php artisan serve
```
# Model, Migration and Controller File

### Make a model, migration and controller file

```
php artisan make:model carTable -mc
```

### as you notice we have created new files inside,
```
app
    >Models
        >carTable.php
```
```
database
    >migrations
        >2024_01_17_005027_create_table_car.php
```
```
app
    >Http
        >controllers
            >carTableController.php
```

<img src="https://cdn.dribbble.com/users/42048/screenshots/8350927/media/23289b76ac7353ad4f0d0619ce6e9f2d.gif" width="400">

# Model - Configuration

### inside carTable.php

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class carTable extends Model
{
    use HasFactory;
}
```
### add the following to the code;
```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\softDeletes; //???

class car extends Model
{
    use HasFactory;
    use softDeletes; //???

    protected $connection = 'mysql'; //???
    protected $table = 'table_car'; //???
}
```
>`softDeletes` means that whenever we use `->delete();` function in our controller, it wouldn't remove it completely but instead put a `deleted_at` date in it.

>`protected $connection = 'mysql';` means that this model needs to connect in a `mysql` from `config/database.php`
```
'mysql' => [
    'driver' => 'mysql',
    'url' => env('DATABASE_URL'),
    'host' => env('DB_HOST', '127.0.0.1'),
    'port' => env('DB_PORT', '3306'),
    'database' => env('DB_DATABASE', 'forge'),
```
>that is created inside `.env` file
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=car_system
DB_USERNAME=root
DB_PASSWORD=admin
```
>and that `protected $table = 'table_car';` means connected to `car_system`'s table named `table_car`.

<!-- add a link to multiple connection especially with HRDSQL and MySQL with .enc and database.php (sekou template) -->

<img src="https://media3.giphy.com/media/lJYNIgDQXJPfdhevId/giphy.gif?cid=6c09b952k7shqzaisbn52rojwqnch2la51mcvojm6kiizeqn&ep=v1_internal_gif_by_id&rid=giphy.gif&ct=g" width="400">

# Migration - Configuration

### inside 2024_01_17_005027_create_table_car.php

```
public function up()
    {
        Schema::create('car_tables', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }
```
### add the following to the code;
```
public function up()
    {
        Schema::create('table_car', function (Blueprint $table) {
            $table->id();
            $table->string('brand')->nullable(); //???
            $table->string('model')->nullable(); //???
            $table->integer('total')->nullable(); //???
            $table->timestamps();
            $table->softDeletes(); //???
        });
    }
```
### Run the following to migrate data in our database (PhpMyAdmin)
```
php artisan migrate
```
### you should see
```
Migrating: 2024_03_06_012858_create_car_tables_table
Migrated:  2024_03_06_012858_create_car_tables_table (49.13ms)
```
### check your database `car_system` if table `table_car` is already created.

## Additional: Run the following if you have changes within your migration file
```
php artisan migrate:rollback
```

<img src="https://cdn.dribbble.com/users/24711/screenshots/3886002/falcon_persistent_connection_2x.gif" width="400">

## Database - Add data

### go to your PhpMyAdmin and Run the following inside `car_system` database:
``
INSERT INTO table_car (brand, model, total, created_at) 

VALUES ('honda' ,'civic' ,1 ,now()), ('toyota' ,'vios' ,3 ,now())
``
### check you `table_car` table if the data is successfully inserted



<img src="https://i.pinimg.com/originals/75/72/c9/7572c9246c16cd21e0b126a4e989de9a.gif" width="400">

# Controller - Configuration

### inside carTableController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class CarTableController extends Controller
{
    //
}
```
### add the following to the code `Get function in Eloquent`;
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\carTable; //??

class CarTableController extends Controller
{
    public function getData(){ //??
        try {
            $car = carTable::all();
            return $car;

        } catch (\Exception $e){
            return response()->json(['error' => 'Failed' . $e->getMessage()], 500);
        }
    }
}
```
>`use App\Models\carTable;` means we connect our controller to model `carTable` that we created earlier 

>we get all of the data from carTable using `$car = carTable::all();` and returns it `$car`.

>since `table_car` table is used inside carTable model then `carTable::all()` means getting all of the data inside `table_car` table.

<h1></h1>

<img src="https://miro.medium.com/v2/resize:fit:1358/1*HAjIrZ38QIiqdy57iGvfzQ.gif" width="400">

# Accessing Api(s)

### go to 

```
routes
    >api.php
```
### Add the following:
```
<?php

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\carTableController; //???

Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});

Route::get('getData',[carTableController::class,'getData']); //???
```
>adding `use App\Http\Controllers\CarTableController;` into `api.php` means connecting `CarTableController.php` into `api.php`

>`Route::get('getData',[CarTableController::class,'getData']);` means running a get function inside `CarTableController.php` named `getData`

### Run the following:
```
php artisan serve
```
### To your Browser 
```
http://127.0.0.1:8000/api/getData
```


# CRUD - Eloquent (ref, sekou)

### Create
### Edit/Update
### Remove/Delete

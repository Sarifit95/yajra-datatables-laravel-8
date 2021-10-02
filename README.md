<h2 align="center" style="color: green">
yajra-datatables-laravel-8</h2>

## About Project

I am create simple Yajra Datatables using laravel 8.


##Install Laravel App

In general, our first step primarily focuses on installing a new laravel application. Run the below-mentioned artisan command to install the sacred canon.

```

composer create-project laravel/laravel yajra-datatables --prefer-dist

```
##Get into the project:

```
cd yajra-datatables 
```
##Install Yajra Datatable Package
I wonder if you haven’t heard about <a target="_blank" href="https://github.com/yajra/laravel-datatables">Yajra Datatables</a> library, it is a jQuery DataTables API for Laravel 4|5|6|7. This plugin handles server-side works of DataTables jQuery plugin through AJAX option by considering the Eloquent ORM, Fluent Query Builder or Collection.

Theoretically, the following command helps you installing the Yajra DataTable plugin in Laravel.

```
composer require yajra/laravel-datatables-oracle
```
Additionally, datatable service provider in providers and alias inside the config/app.php file.

```
'providers' => [
	
	
	Yajra\DataTables\DataTablesServiceProvider::class,
]
'aliases' => [
	
	
	'DataTables' => Yajra\DataTables\Facades\DataTables::class,
]
```

##Run vendor publish command further this step is optional:

```
php artisan vendor:publish --provider="Yajra\DataTables\DataTablesServiceProvider"
```
Create Model and Migration file with this command
```
php artisan make:model Employee -m

```

##Open
database/migrations/timestamp_create_employees_table.php 
file and add the given below code.


```
public function up()
{
    Schema::create('employees', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('email')->unique();
        $table->string('username');
        $table->string('phone');
        $table->string('dob');
        $table->timestamps();
    });
}

```
##Open app/Models/Employee.php not only – but also lay down the schema in the $fillable array.

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Employee extends Model
{
    use HasFactory;
    protected $fillable = [
        'name',
        'email',
        'username',
        'phone',
        'dob',
    ];    
}
```
##Run migration using the following command.




```
php artisan migrate
```
## Insert Dummy Data or Records with  Use Seed and the built-in plugin Faker 
Open the database/seeds/DatabaseSeeder.php file and add the following code.

```
<?php

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Faker\Factory as Faker;

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     *
     * @return void
     */
    public function run()
    {
        $faker = Faker::create();

        $gender = $faker->randomElement(['male', 'female']);

    	foreach (range(1,200) as $index) {
            DB::table('employees')->insert([
                'name' => $faker->name($gender),
                'email' => $faker->email,
                'username' => $faker->username,
                'phone' => $faker->phoneNumber,
                'dob' => $faker->date($format = 'Y-m-d', $max = 'now')
            ]);
        }
    }
}

```
##Run the following command to generate dummy data:

```
php artisan db:seed

```
##Create Controller
```
php artisan make:controller EmployeeController

```
###Open app/Http/Controllers/EmployeeController.php file and add the following code.
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Employee;
use DataTables;

class EmployeeController extends Controller
{
    public function index()
    {
        return view('welcome');
    }


    public function getemployees(Request $request)
    {
        if ($request->ajax()) {
            $data = Employee::latest()->get();
            return Datatables::of($data)
                ->addIndexColumn()
                ->addColumn('action', function($row){
                    $actionBtn = '<a href="javascript:void(0)" class="edit btn btn-success btn-sm">Edit</a> <a href="javascript:void(0)" class="delete btn btn-danger btn-sm">Delete</a>';
                    return $actionBtn;
                })
                ->rawColumns(['action'])
                ->make(true);
        }
    }
}
```
##Define Route
###Open routes/web.php and add the given code.
```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\EmployeeController;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::get('employees', [employee::class, 'index']);

Route::get('employees/list', [EmployeeController::class, 'getemployees'])->name('employees.list');
```
##Display Data
###Open resources/views/welcome.blade.php file and place the following code.

```
<!DOCTYPE html>
<html>
<head>
    <title>Yajra Datatable ,Laravel 8 </title>
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"/>
    <link href="https://cdn.datatables.net/1.10.21/css/jquery.dataTables.min.css" rel="stylesheet">
    <link href="https://cdn.datatables.net/1.10.21/css/dataTables.bootstrap4.min.css" rel="stylesheet">
</head>
<body>

<div class="container mt-5">
    <h3 class="mb-4 text-center" >Laravel 8 Yajra Datatables </h3>
    <h3 class="mb-4 text-center" >Employee List </h3>
    <table class="table table-bordered yajra-datatable">
        <thead>

        <tr>
            <th>No</th>
            <th>Name</th>
            <th>Email</th>
            <th>Username</th>
            <th>Phone</th>
            <th>DOB</th>
            <th>Action</th>
        </tr>
        </thead>
        <tbody>
        </tbody>
    </table>
</div>

</body>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.0/jquery.validate.js"></script>
<script src="https://cdn.datatables.net/1.10.21/js/jquery.dataTables.min.js"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/js/bootstrap.min.js"></script>
<script src="https://cdn.datatables.net/1.10.21/js/dataTables.bootstrap4.min.js"></script>

<script type="text/javascript">
    $(function () {

        var table = $('.yajra-datatable').DataTable({
            processing: true,
            serverSide: true,
            ajax: "{{ route('getemployee.list') }}",
            columns: [
                {data: 'DT_RowIndex', name: 'DT_RowIndex'},
                {data: 'name', name: 'name'},
                {data: 'email', name: 'email'},
                {data: 'username', name: 'username'},
                {data: 'phone', name: 'phone'},
                {data: 'dob', name: 'dob'},
                {
                    data: 'action',
                    name: 'action',
                    orderable: true,
                    searchable: true
                },
            ]
        });

    });
</script>
</html>

```
##Run the following command and check our progress on the browser.

```
php artisan serve

```


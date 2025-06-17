# Laravel User CRUD API

This is a complete Laravel 10+ RESTful API that provides **CRUD operations for User data**. It avoids `apiResource` for better understanding and allows manual control over all routes and logic. Passwords are hashed, and all responses are in JSON format.

---

## ✅ Why This Project?

User management is a must-have in almost every web or mobile app. This project teaches:

- Setting up Laravel manually for API
- Writing custom route logic
- Validating user input
- Hashing passwords securely
- Creating clean, maintainable controller logic

---

## 🚀 Installation & Setup

### 1. Create Laravel Project

composer create-project laravel/laravel crud-api
cd crud-api

### 2. Configure Database

Edit the `.env` file and set your database credentials:

DB_CONNECTION=mysql  
DB_HOST=127.0.0.1  
DB_PORT=3306  
DB_DATABASE=crud_api  
DB_USERNAME=root  
DB_PASSWORD=

### 3. Generate App Key

php artisan key:generate

---

## 🧱 Create User Model and Migration

Run this command to generate the model and migration:

php artisan make:model User -m

Open the migration file and update it like this:

public function up()
{
    Schema::create('users', function (Blueprint $table) {
        $table->id();
        $table->string('first_name');
        $table->string('last_name');
        $table->string('username')->unique();
        $table->string('email')->unique();
        $table->string('password');
        $table->string('phone_no')->nullable();
        $table->timestamps();
    });
}

Run the migration:

php artisan migrate

---

## 🧩 User Model Fillable Fields

Update `app/Models/User.php`:

protected $fillable = [
    'first_name',
    'last_name',
    'username',
    'email',
    'password',
    'phone_no',
];

---

## 🧠 Create Controller

Create a controller using Artisan:

php artisan make:controller API/UserController

Now update `app/Http/Controllers/API/UserController.php` with:

namespace App\Http\Controllers\API;

use App\Models\User;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use Illuminate\Support\Facades\Hash;

class UserController extends Controller
{
    public function index()
    {
        return response()->json(User::all(), 200);
    }

    public function store(Request $request)
    {
        $request->validate([
            'first_name' => 'required|string|max:255',
            'last_name'  => 'required|string|max:255',
            'username'   => 'required|string|max:255|unique:users',
            'email'      => 'required|email|unique:users',
            'password'   => 'required|string|min:6',
            'phone_no'   => 'nullable|string|max:20',
        ]);

        $user = User::create([
            'first_name' => $request->first_name,
            'last_name'  => $request->last_name,
            'username'   => $request->username,
            'email'      => $request->email,
            'password'   => Hash::make($request->password),
            'phone_no'   => $request->phone_no,
        ]);

        return response()->json($user, 201);
    }

    public function show($id)
    {
        $user = User::find($id);
        if (!$user) {
            return response()->json(['message' => 'User not found'], 404);
        }
        return response()->json($user, 200);
    }

    public function update(Request $request, $id)
    {
        $user = User::find($id);
        if (!$user) {
            return response()->json(['message' => 'User not found'], 404);
        }

        $data = $request->only([
            'first_name', 'last_name', 'username', 'email', 'phone_no'
        ]);

        if ($request->filled('password')) {
            $data['password'] = Hash::make($request->password);
        }

        $user->update($data);

        return response()->json($user, 200);
    }

    public function destroy($id)
    {
        $user = User::find($id);
        if (!$user) {
            return response()->json(['message' => 'User not found'], 404);
        }

        $user->delete();

        return response()->json(['message' => 'User deleted successfully'], 200);
    }
}

---

## 🌐 Define API Routes

Open `routes/api.php` and define the routes manually:

use App\Http\Controllers\API\UserController;

Route::get('/users', [UserController::class, 'index']);
Route::post('/users', [UserController::class, 'store']);
Route::get('/users/{id}', [UserController::class, 'show']);
Route::put('/users/{id}', [UserController::class, 'update']);
Route::delete('/users/{id}', [UserController::class, 'destroy']);

---

## 🧪 Sample API Requests

### ➕ Create User

POST /api/users

{
  "first_name": "John",
  "last_name": "Doe",
  "username": "johndoe",
  "email": "john@example.com",
  "password": "secret123",
  "phone_no": "9876543210"
}

### 📃 Get All Users

GET /api/users

### 🔍 Get One User

GET /api/users/{id}

### 🖊️ Update User

PUT /api/users/{id}

{
  "first_name": "Jane",
  "email": "jane@example.com"
}

### ❌ Delete User

DELETE /api/users/{id}

---

## 🔐 Security Notes

- Passwords are hashed using `Hash::make()`
- Unique constraints for `username` and `email`
- Basic validation is applied on create/update
- No authentication is implemented — this is just core CRUD logic

---

## 📜 License

This project is open-source and available under the [MIT License](https://opensource.org/licenses/MIT).

---

## 🙌 Contributing

Want to improve or extend the API? Fork this repo and submit a PR — ideas like auth, pagination, filtering, etc., are welcome!
```

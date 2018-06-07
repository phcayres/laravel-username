# laravel-username

Setting up the username field to authenticate users on Laravel. By default Laravel uses user email when we use **php artisan make:auth** to do the authentication process.

## Getting Started
Laravel has an authentication system that simplifies the access control into our systems. This article presents the steps to change the default access field **email** to the **username** field.

## Eteps

**1ª** - Create a new Laravel project
```sh
laravel new <nome_projeto>
```
**2ª** - Set up the database acess information editting the **.env** file.

**3ª** - Run the command ```php artisan make:auth``` inside de project root directory. This command should be used in fresh applications and will install a layout view, registration and login views, as well as routes for all authentication endpoints.

-Locate and edit the user migration file and add the username field as shown below:

```sh
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            
            $table->string('username', 20)->unique();
            
            $table->string('email', 150)->unique();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }
```
Salve and run ```php artisan migrate``` command.

**4ª** - Edit de Model **User.php** file to add the **username** field inside **protected $fillable[ ... ]** definitions as shown below:

```
    protected $fillable = [
        'name', 'username', 'email', 'password',
    ];
```

**5ª** - Edit the Controller **RegisterController.php** file to add the instruction **'username' => 'required|string|max:255',** inside the method **protected function validator(array $data){ ... }** and the instruction **'username' => $data['username'],** inside the method **protected function create(array $data){ ... }** as shown below:

```sh
 protected function validator(array $data)
    {
        return Validator::make($data, [
            'name' => 'required|string|max:255',
            
            'username' => 'required|string|max:20|unique:users',
            
            'email' => 'required|string|email|max:150|unique:users',
            'password' => 'required|string|min:6|confirmed',
        ]);
    }
```
and 
```sh
    protected function create(array $data)
    {
        return User::create([
            'name' => $data['name'],
            
            'username' => $data['username'],
            
            'email' => $data['email'],
            'password' => Hash::make($data['password']),
        ]);
    }
```
**6ª** - Edit the file **AuthenticatesUsers** located in **vendor\laravel\framework\src\Illuminate\Foundation\Auth**. Search for **public function username(){ ... }** method and change the return field **email** for **username** as shown below:

```sh
    public function username()
    {
        //return 'email';
        return 'username';
    }
```

Now, let's make changes into logon and register users views files including the **username** fieild as a requered.

**7ª** - Edit **register.blade.php** file located in **\\resources\views\auth** and insert the code as shown below:

```sh
    <div class="form-group row">
        <label for="username" class="col-md-4 col-form-label text-md-right">{{ __('User') }}</label>

        <div class="col-md-6">
            <input id="username" type="text" class="form-control{{ $errors->has('username') ? ' is-invalid' : '' }}" name="username" value="{{ old('username') }}" required autofocus>

            @if ($errors->has('username'))
                <span class="invalid-feedback">
                    <strong>{{ $errors->first('username') }}</strong>
                </span>
            @endif
        </div>
    </div>
```

**8ª** - Edit the **login.blade.php** file located in **\\resources\views\auth** and delete the **\<div>...<\/div>** corresponding to the **email** field and insert the code as shown below:

```sh
    <div class="form-group row">
        <label for="username" class="col-sm-4 col-form-label text-md-right">{{ __('User') }}</label>

        <div class="col-md-6">
            <input id="username" type="username" class="form-control{{ $errors->has('username') ? ' is-invalid' : '' }}" name="username" value="{{ old('username') }}" required autofocus>

            @if ($errors->has('username'))
                <span class="invalid-feedback">
                    <strong>{{ $errors->first('username') }}</strong>
                </span>
            @endif
        </div>
    </div>
```

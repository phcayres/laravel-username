# laravel-username
Alterar autenticação padrão do Laravel de e-mail para username

## Introdução
O Laravel disponibiliza, a partir da versão 5.2, um sistema de autenticação que simplifica a implementação de controle de acesso. Neste artigo irei utiliza-lo e alterar o forma padrão de acesso trocando a autenticação padrão de e-mail para o campo username.

## Etapas

**1ª** - Crie um novo projeto Laravel
```sh
laravel new <nome_projeto>
```
**2ª** - Configure as informações de acesso ao banco de dados de sua aplicação no arquivo **.env**.

**3ª** - Acesse o diretório do projeto e execute o comando ```php artisan make:auth```. Este comando organiza de maneira rápida todas as rotas e visualizações necessárias para autenticação dentro de seu projeto.

- Localize o arquivo de migration da tabela de usuários e adicione o campo username como segue:

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
Salve a alteração e execute o comando ```php artisan migrate```.

**4ª** - Edite o Model **User.php** e adicione o campo **username** em **protected $fillable[ ... ]** como segue:

```
    protected $fillable = [
        'name', 'username', 'email', 'password',
    ];
```

**5ª** - Edite o controller **RegisterController.php** e adicione a linha **'username' => 'required|string|max:255',** no método **protected function validator(array $data){ ... }** e a linha **'username' => $data['username'],** no método **protected function create(array $data){ ... }**.

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
e 
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
**6ª** - edite o arquivo **AuthenticatesUsers** que se encontra dentro do caminho **vendor\laravel\framework\src\Illuminate\Foundation\Auth**. Localize o método **public function username(){ ... }** e altere o seu retorno de **email** para **username**, como segue:

```sh
    public function username()
    {
        //return 'email';
        return 'username';
    }
```

Agora vamos alterar as views de logon e registro de usuários para incluir o campo username.

**7ª** - Edite o arquivo **register.blade.php** disponível dentro de **\\resources\views\auth** e adicione o trecho de código abaixo:

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

**8ª** - Edite o arquivo **login.blade.php** disponível dentro de **\\resources\views\auth**, remova a div correspondente ao **email** e adicione o trecho de código abaixo:

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

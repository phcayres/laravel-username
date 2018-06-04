# laravel-username
Alterar autenticação padrão do Laravel de e-mail para username

## Introdução
O Laravel disponibiliza, a partir da versão 5.2, um sistema de autenticação que simplifica a implementação de controle de acesso. Neste artigo irei utiliza-lo e alterar o forma padrão de acesso trocando a autenticação padrão de e-mail para o campo username.

## Etapas

1º - crie um novo projeto Laravel
```sh
laravel new <nome_projeto>
```
2º - configure as informações de acesso ao banco de dados de sua aplicação no arquivo **.env**.

3º - acesse o diretório do projeto e execute o comando ```sh php artisan make:auth ```. Este comando organiza de  maneira rápida todas as rotas e visualizações necessárias para autenticação dentro de seu projeto.

- Localize o arquivo de migration da tabela de usuários e adicione o campo username como segue:
```sh

    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            
            $table->string('username', 20);
            
            $table->string('email')->unique();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }
```
Salve a alteração e execute o comando ```php artisan migrate```.

4º - Edite o controller **RegisterController.php** e adicione a linha **'username' => 'required|string|max:255',** no método **protected function validator(array $data){ ... }** e a linha **'username' => $data['username'],** no método **protected function create(array $data){ ... }**.

```sh
 protected function validator(array $data)
    {
        return Validator::make($data, [
            'name' => 'required|string|max:255',
            
            'username' => 'required|string|max:255',
            
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


# laravel-username
Alterar autenticação padrão do Laravel de e-mail para username

## Introdução
O Laravel disponibiliza, a partir da versão 5.2, um sistema de autenticação que simplifica a implementação de controle de acesso. Neste artigo irei utiliza-lo e alterar o forma padrão de acesso trocando a autenticação padrão de e-mail para o campo username.

## Etapas

- crie um novo projeto Laravel
```sh
laravel new <nome_projeto>
```

- acesse o diretório do projeto e execute o comando 
```sh
php artisan make:auth
```
Este comando organiza de  maneira rápida todas as rotas e visualizações necessárias para autenticação dentro de seu projeto.

- Localize o arquivo de migration da tabela de usuários e adicione o campo username como segue:
```sh
    public function up()
    {
        $this->eachTable( function($table) {    
            Schema::create($table, function (Blueprint $temp) {
                $temp->increments('id');
                $temp->string('name');
                
                $temp->string('username', 20);
                
                $temp->string('email', 150)->unique();
                $temp->string('password');
                $temp->rememberToken();
                $temp->timestamps();
            });
        });
    }
```
Este comando organiza de  maneira rápida todas as rotas e visualizações necessárias para autenticação dentro de seu projeto.


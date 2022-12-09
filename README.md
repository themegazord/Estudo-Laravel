# Laravel

## Configurações Iniciais

### Configuração de onde o Composer vai pegar os repositórios

```shell
composer config -g repo.packagist composer https://packagist.org
```

### Configurando os protocolos HTTPS/SSH para pegar os repositórios do GitHub

```shell
composer config -g github-protocols https ssh
```
<hr>

### Criar um projeto com Laravel via Composer

```shell
composer create-project --prefer-dist laravel/laravel [nome-do-projeto]
```
## Comandos principais do Artisan

### Versão do Framework

```shell
php artisan -V
```
### Servir a aplicação com um servidor externo

```shell
php artisan serve
```

* #### Servir a aplicação com um servidor externo especificando uma porta

```shell
php artisan serve --port=9000
```

### Fazer com que a aplicação entre em modo de manutenção

```shell
php artisan down
```

### Fazer a aplicação sair do modo de manutenção
```shell
php artisan up
```

### Criando Controllers

```shell
php artisan make:controller nome_do_controller
```

### Criando Models

```shell
php artisan make:model nome_do_model
```

### Criando Model com Migration

```shell
php artisan make:model nome_do_model -m
```

### Criando Migration
```shell
php artisan make:migration nome_da_migration
```

### Persistindo a migration para o banco de dados
```shell
php artisan migrate
```

### Status das Migrations
```shell
php artisan migrate:status
```

### Reset das Migrations
Executa um rollback de todas as migrações
```shell
php artisan migrate:reset
```

### Refresh das Migrations
Executa um rollback de todas as migrations e depois executa todos os migrate.
```shell
php artisan migrate:refresh
```

### Fresh das Migrations
Dropa todos os elementos do banco de dados e executa o migrate de todos as migrations
```shell
php artisan migrate:fresh
```

### Insere um seeder
```shell
php artisan make:seeder nome_da_seeder
```

### Executar todas as seeder
```shell
php artisan db:seed
```
### Executar apenas uma seed especifica
```shell
php artisan db:seed --class=nome_da_classe_seeder
```

### Criar uma factory
```shell
php artisan make:factory nome_da_factory --model=nome_da_model
```

### Criar um Middleware
```shell
php artisan make:middleware nome_do_middleware
```

<hr>

## Routes

### WEB

O arquivo web.php trabalhará as rotas no estilo web, processando páginas(views) do lado do backend e servindo as mesmas do lado do front-end, permitindo tratamento de cookies e sessios

### API

Responder dados as requisições que são feitas.

### Channel/Broadcasting

Invés do serviço ser feito através de requisição-resposta, o broadcasting trabalho o processo inverso, onde o servidor sinalizará o front-end sobre algo, dando uma sensação de _real time_.

### Console

Usado para criar comandos personalizados do artisan.


### Criando rotas

#### GET

```php
Route::get('/', function() {}) // Com função de callback
Route::get('/', [HomeController::class, 'nome-da-função']) // Com controllers
```

#### POST

```php
Route::post('/', function() {}) // Com função de callback
Route::post('/', [HomeController::class, 'nome-da-função']) // Com controllers
```
### Enviando parametros pela rota.

Para encaminhar parametros da rota para as views, é feito a partir de / a frente da rota, encapsulado por {} e resgatando como parametro da função callback

```php
Route::get('get/{p1}', function($p1) {
    echo 'Esse é o parametro 1 = ' . $p1;
})
```

Caso seja por Controller que a rota esteja sendo tratada, basta lá no metódo do Controller _setado_ na rota, passar os parametros pro método.

* #### Passando parametros opcionais e valores _default_
  * Para passar parametros opcionais, basta inserir sempre por ultimo, na uri da rota, inserindo como sufixo o `?` e o valor padrão é ditado na passagem do parametro setando um valor ao mesmo.
  * ```Route::get('get/{p1}/{p2?}, function($p1, $p2 = 'Este é o valor padrão') {}')``` 

### Agrupamento de Rotas

```php
Route::prefix('/uri')->group(function() {
    Route::get('/get', function() {});
    Route::post('/post', function() {});
})

// Para acessar a rota get, vai ter que passar localhost/uri/get
// Para acessar a rota post, vai ter que passar localhost/uri/post
```

### Nomeando rotas

Para nomear rotas, basta usar o método `` ->name('nome-dado') ``

```php
Route::get('/get', function() {})->name('rota.get')
```

### Redirecionamento de Rota

Para redirecionar o acesso de uma rota para outra, usaremos a função estática da classe Route `` ::redirect($uri, $destination, $status) ``, passando como parametro a, rota que está sendo acessada, a rota para qual vai ser redirecionado, e o status da chamada de redirencionamente, onde este não é obrigatório.


```php
Route::redirect('/get', '/post')
```

### Rota de contingência (fallback)

Para criar uma rota de contingência para acesso de pagínas inexistentes, é utilizado a função estática da classe Route `` ::fallback($callback || Controller) `` que recebe como parametro ou uma função de callback ou um Controller apontando para alguma view.

```php
Route::fallback(function() {
    echo "Pagina 404, não encontrada!";

    // Caso seja acessado uma rota que não existe, será printado na tela esta mensagem.
})
```

## Controller

### Passando parametros do Controller para a View.

Para passar o parametro para a view, pode ser feito de 3 maneiras.

1. Array associativo
2. Metodo with
3. Compact

#### Array associativo

```php
public function index($p1, $p2) {
    return view('site.contact', ['p1' => $p1, 'p2' => $p2]);
}
```

#### Metodo with

```php
public function index($p1, $p2) {
    return view('site.contact')->with('p1', $p1)->with('p2', $p2);
}
```

#### Compact

```php
public function index($p1, $p2) {
    return view('site.contact', compact('p1', 'p2'));
}
```

## Migrations

### Esqueleto da Migration

#### Metodo Up

O metodo up é o responsável por persistir toda a informação contida nele para o banco de dados atráves da linguagem SQL, porém, você precisa passar essas instrução em forma de POO.


#### Metodo Down

O metodo down vai ser chamado quando for realizado _rollback_ da migration pelo artisan. Quando uma migration é feita, tudo que você fizer de alteração ou inserção no banco de dados você tem de inserir no método Down, para que a integridade do banco de dados não seja comprometida no momento do _rollback_.

### Metodos para tabelas

Para manipular tabelas no banco de dados dentro das migrations vamos ter de usar as funções estáticas da Facade Schema citadas a baixa.

1. `Schema::create`
2. `Schema::table`
3. `Schema::hasTable`
4. `Schema::hasColumn`
5. `Schema::rename`
6. `Schema::drop`
7. `Schema::dropIfExist`

#### Schema::create

Função estática usada para manipular a criação de tabelas no banco de dados.
Essa função recebe 2 parametros, um é o nome da tabela que vai ser criada e o outro é uma função de callback onde passaremos como parametro uma variavel englobada na classe Blueprint, responsável por ser o molde para as questão de parametrização do banco de dados.

```php
Schema::create('users', function(Blueprint $table) {
    $table->id();
})
```

Os metodos da Blueprint, poderão ser abordados melhor na documentação [Clicando aqui](https://laravel.com/docs/9.x/migrations#creating-columns)

#### Schema::table

Função estática utilizada para modificar colunas e indices dentro de tabelas existentes.

```php
if(Schema::table('users', function(Blueprint $table)){
    $table->integer('votes'); // Inserindo a coluna votes como integer na tabela users.
})
```


#### Checando a existência de tabelas e colunas
Para checar se tem a existência de tabelas e colunas antes da execução de um bloco de código, pode ser utilizado a seguinte prerrogativa.
```php
  if(Schema::hasTable('users')) {
    // Passa como parametro o nome da tabela que vai ser verificada
  }

  if(Schema::hasColumn('users', 'email')) {
    // Passa como parametro o nome da tabela e o nome da coluna que vão ser verificadas.
  }
```

#### Schema::rename

Função estatica utilizada para renomear uma tabela, recebendo como parametro o nome atual da tabela e por segundo o nome para qual a tabela vai se passar a chamar.

```php
Schema::rename('users','usuarios') // Vai modificar o nome da tabela users para usuarios.
```

#### Schema::drop

Função estática responsável por apagar uma tabela por inteiro, recebendo como parametro o nome da tabela alvo.

```php
Schema::drop('users');
```

#### Schema::dropIfExists

Função estática que tem a mesma função da `Schema::drop`, porém, essa vai dropar a tabela apenas se ela existir, trazendo mais segurança e controle para o código.

```php
Schema::dropIfExists('users')
```

### Metodos para Colunas

#### Modificadores para colunas

Além dos tipos de dados que você pode _setar_ para uma coluna, podemos passar modificadores que atribuirão a coluna caracteristicas especificas, por exemplo, `->nullable()`, tornando a coluna apta para receber valores nulos como dado.

Podemos verificar mais desses modificadores [Clicando aqui](https://laravel.com/docs/9.x/migrations#column-modifiers)

#### Renomeando uma coluna

Ao utilizar o método `->renameColumn($from, $to)` onde recebe o parametro `$from` indicando o nome atual da coluna e `$to` o nome ao qual a coluna vai passar a ser chamada, poderemos renomear colunas dentro do banco de dados

#### Dropando uma coluna

Ao utilizar o método `->dropColumn($column) || ->dropColumn([$array_de_colunas])` podemos eliminar uma coluna de uma tabela especifica.

### Inserindo relacionamento 1-1

Para que o relacionamento 1-1 funcione, temos que setar para a coluna que vai receber as informações de relacionamente o tipo `unsignedBigInteger`. Para setar a _constraint_ da foreign key, vamos ter de usar o método `->foreign()` passando como parametro o nome da coluna que vai ser a foreign key, em seguida o método `->references()` passando como parametro o nome da coluna de referência na tabela que vai ser usada e por ultimo o método `->on()` passando como parametro a tabela da onde vai sair o relacionamento. Para que a relação seja 1-1, devemos setar o metodo `->unique()` passando como parametro o nome da tabela que vai ser unica, segue um exemplo

```php
Schema::create('products', function(Blueprint $table) {
    $table->id();
    // setando o valor unsignedBigInteger para a coluna da tabela products
    $table->unsignedBigInteger('cod_grupo');

    //constraint vindo da tabela grupos
    $table->foreign('cod_grupo')->references('id')->on('grupo');

    //utilizando o unique()
    $table->unique('cod_grupo');
});
```

Dentro do método down da migration, deve ser inserido a retirada dessas informações para que quando houver o rollback da tabela ou do banco, a integridade não seja rompida.
Deve ser feito na ordem inversa, sendo assim relacionamento > coluna > tabela

```php
public function down() {

    Schema::table('products', function(Blueprint $table) {
        //Irá dropar o relacionamento especificado.
        //Deve passar o nome da constraint que sempre vai seguir este padrão
        //[nome_da_table]_[nome_da_column]_foreign
        $table->dropForeign('products_cod_grupo_foreign');
        $table->dropColumn('cod_grupo');
    })

    Schema::dropIfExists('products');
}
```

### Inserindo relacionamento 1-n e n-n

É a mesma coisa da 1-1, com a diferença que vai ser sem o método `->unique()` permitindo ter mais relações dentro da tabela.

## Eloquent ORM

### Tinker

#### Inserindo dados com create() ou fill();

Conseguimos persistir dados no banco de dados usando o `create()` passando um array associativo onde a chave é o nome da coluna e o valor é o dado.

Porém, para que isso seja feito, dentro do Model utilizado, deve ser inserido a propriedade `protected $fillable` onde você vai passar um array para essa propriedade inserindo o nome das colunas que vão ser autorizadas a receberem dados diretos pelo create

```php
class Provider extends Model {
    protected $fillable = ['name', 'lastname'];
}
```

```php
use \App\Models\Provider;

\App\Models\Provider::create([
    'name' => 'Gustavo',
    'lastname' => 'Camargo Campost'
]);
```


#### Buscando todos os dados com all()

Para retornar todos os dados de uma determinada tabela, segue-se o script abaixo

```php
use \App\Models\Provider;

$providers = Provider::all();
```

Equivalente a

```sql
SELECT * FROM providers
```

#### Buscando um dado especifico pela PK usando find()

```php
use \App\Models\Provider;

$provider = Provider::find(5)
```

Equivalente a

```sql
SELECT * FROM providers WHERE id = 5
```
#### Realizando consulta com a clausula where()

A clausula `where($column, $op = '=', $value)` espera 3 parametros sendo 1 deles com valor defalt
1. `$column` -> Recebe o nome da coluna que vai ser feita a consulta
2. `$op` -> Recebe o operador de comparação, sendo eles, `>, >=, <, <=, <>, LIKE`, caso não seja passado nenhum dos dois, o Eloquent entenderá que o operador vai ser o `=`.
3. `$value` -> Valor a ser buscado.

```php
use \App\Models\Provider;

$provider = Provider::where('name', 'Gustavo');
```

Equivalente a

```sql
SELECT * FROM providers WHERE name = 'Gustavo'
```

#### whereIn e whereNotIn

A clausula `whereIn($column, $array_de_comparação) || whereNotIn($column, $array_de_comparação)` recebem 2 parametros

1.`$column` -> Recebe o nome da coluna que vai ser realizado a consulta
2.`$array_de_comparação` -> Recebe um array de valor aos quais vão ser testados no SQL

```php
use \App\Models\Provider;

$providers = Provider::whereIn('id', [1, 3]); // Vai retornar todos os dados com id igual a 1 e 3.

$providersNotIn = Provider::whereNotIn('id', [1, 3]); // Vai retornar todos os dados com id diferente de 1 e 3
```

Equivalente a 
```sql
SELECT * FROM providers WHERE id IN (1, 3);

SELECT * FROM providers WHERE id NOT IN (1, 3);
```

#### whereBetween e whereNotBetween

Clausula `whereBetween($column, $array) || whereNotBetween($column, $array)` recebe verificar os valores passados no array e e tras no resultado tudo que estiver entre os valores do array incluindo os informados

1.`$column` -> Recebe o nome da coluna que vai ser realizado a consulta
2.`$array` -> Recebe um array de valor aos quais vão ser testados no SQL

```php
use \App\Models\Provider;

$providers = Provider::whereBetween('id', [1, 3]); // vai trazer os dados com id 1, 2, 3

$providersNotBetween = Provider::whereNotBetween('id', [1, 3]); // vai trazer os dados com o id diferente de 1, 2, 3
```

Equivalente a
```sql
SELECT * FROM providers WHERE id BETWEEN(1, 3);

SELECT * FROM providers WHERE id NOT BETWEEN (1, 3);
```
#### orWhere

Todos as clausulas apresentadas até agora podem ser aplicadas para orWhere, a diferença entre `where` e `orWhere` é o operador lógico, visto que, `where` já é por padrão com o operador `AND` e `orWhere` vem com o operador lógico `OR`.

#### whereNull e whereNotNull

A clausula `whereNull($column) || whereNotNull($column)` vai verificar e retornar todos os dados que são ou não nulos, passando como parametro o nome da coluna

```php
use \App\Models\Provider;

$providers = Provider::whereNull('created_at'); // vai retornar todos os dados que forem null na coluna created_at

$providers = Provider::whereNotNull('created_at'); //vai retornar todos os dados que não forem null na coluna created_at
```

Equivalente a 
```sql
SELECT * FROM providers WHERE created_at IS NULL;
SELECT * FROM providers WHERE created_at IS NOT NULL;
```

#### whereColumn

A clausula `whereColumn($c1, $c2)` verifica se os valores das duas colunas passadas são iguais, caso sejam, os valores são retornados

1. `$c1` -> Coluna 1
2. `$c2` -> Coluna 2

```php
use \App\Models\Provider;

$providers = Provider::whereColumn('created_at', 'updated_at');
```

Possivel equivalencia

```sql
SELECT * FROM providers WHERE created_at = updated_at
```

#### Consultando por agrupamento

Para realizar consulta por agrupamento, deve-se passar como parametro do where, uma função de callback onde a mesma vai receber uma parametro `$query` que será o objeto da consulta.

```php
use \App\Models\Provider;

$providers = Provider::where(function($query){
    $query->where('name', 'Jorge')
          ->orWhere('name', 'Joana');
})->where(function($query){
    $query->whereIn('motivo', [1, 2])
          ->orwhereBetween('id', [4, 6]);
})->get();
```
Equivalente a
```sql
SELECT * FROM providers WHERE (name = 'Jorge' OR name = 'Joana') AND (motivo IN (1, 2) OR BETWEEN (4,6));
```

#### Ordenando consultas

A clausula `orderBy($order = 'ASC')` vai ordernar por padrão a consulta em ordem ascendente, caso seja necessário a ordenação descendente, aplica-se o parametro `'DESC'`

```php
use \App\Models\Provider;

$providers = Provider::orderBy();
```

Equivalente a

```sql
SELECT * FROM providers ORDER BY id ASC
```


## Collections

### Metodos

#### first, last, reverse

1. `first()` -> Vai retornar a primeira ocorrencia de uma Collection;
    ```php
    use \App\Models\Provider;

    $providers = Provider::all();

    $providers->first();
    ```
2. `last()` -> Vai retornar a ultima ocorrencia de uma Collection;
    ```php
    use \App\Models\Provider;

    $providers = Provider::all();
    $providers->last();
    ```
3. `reverse()` -> Inverte a Collection
    ```php
    use \App\Models\Provider;

    $providers = Provider::all();
    $providers->reverse();
    ```

#### toArray() e toJson()

1. `toArray()` -> transforma a colection em um array
    ```php
    use \App\Models\Provider;

    $providers = Provider::all();
    $providers->toArray();
    ```
2. `toJson()` -> transforma a collection em um json
    ```php
    use \App\Models\Provider;

    $providers = Provider::all();
    $providers->toJson();
    ```
#### pluck()

1. `pluck()` -> Vai receber uma chave como parametro e retornar uma collection com os valores dessas chaves.
    ```php
    use \App\Models\Provider;

    Provider::all()->pluck('email');
    ```

## Atualizando registros com Eloquent ORM

### save()

```php
use \App\Models\Provider;

$provider = Provider::find(1);
$provider->name = 'Gustavo de Camargo Campos'; // alterando o valor da propriedade name da classe Provider.
$provider->save(); // agora sim os dados foram persistidos no banco de dados.
```


### fill()

Para utilizar o metodo `fill()` a classe utilizada deve estar configurada com a propriedade `protected $fillable` com os campos que serão usando dentro de um array. O metodo `fill()` recebe como um parametro um array associativo, tornando mais a atualização de dados.



```php
use \App\Models\Provider;

protected $fillabe = ['name', 'lastname'];

Provider::find(1)->fill('name'=>'Gustavo', 'lastname'=>'Camargo Campos')->save();
```

#### where() com update()

Pode ser realizado a atualização dos dados atraves da clausula `update()` passada posterior a clausula `where()`.


```php
use \App\Models\Provider;

Provider::where('id', 1)->update(['name' => 'Gustavo']);// vai ser atualizado os dados da coluna name do fornecedor com id 1
```

## Deletando dados pelo Eloquent ORM

### delete() ou destroy()

Ao passar o metodo `delete()` ou `destroy()` você irá eliminar o registro do banco de dados.



```php
use \App\Models\Provider;

Provider::find(1)->delete(); // registro de id 1 vai ser removido

Provider::destroy(5); // registro de id 5 vai ser removido.
```

### SoftDelete

O recurso de `SoftDelete` precisa que seja criado na tabela uma coluna chamada `deleted_at`, onde quando for chamado o `SoftDelete` ele irá verificar nessa tabela se o registro está `null` ou preenchido com alguma data. Caso esteja `null` o Eloquent irá interpretar que aquele registro não foi 'excluido', porém, caso aja alguma data, o Eloquent interpretará aquele registro como excluido, fazendo com que não seja retornado em query's normais.


**Model Provider**
```php
use Illuminate\Database\Eloquent\SoftDeletes; // é uma trait

class Provider extends Model {
    use SoftDeletes;
}
```
**Migration**
```php
public function up (){
    Schema::table('providers', function(Blueprint $table) {
        $table->softDeletes();
    });
}


public function down () {
    Schema::table('providers', function(Blueprint $table) {
        $table->dropSoftDeletes();
    })
}
```

Agora, quando for utilizado o metodo `delete()` será inserido uma data na coluna `deleted_at`.
Para realizar a real eliminação do dado do banco de dados, deveremos utilizar o metodo `forceDelete()`.

```php
use \App\Models\Provider;

Provider::find(1)->forceDelete();
```

#### Metodo withTrashed()

O metodo `withTrashed()` vai fazer com que as consultas tragam os registros que contenham data no `deleted_at`.

```php
use \App\Models\Provider;

Provider::withTrashed();
```

#### Metodo onlyTrashed()

Assim como o método `withTrashed()`, o `onlyTrashed()` irá tratar das consultas com os registros que contenham data no `deleted_at`, a diferença é que o `onlyTrashed()` irá retornar apenas os registros 'excluidos' ou seja, que contenham data no `deleted_at`.

```php
use \App\Models\Provider;

Provider::onlyTrashed();
```

#### Metodo restore

Metodo utilizado para restaurar o dado do SoftDelete.

```php
use \App\Models\Provider;

$providers = Provider::withTrashed();

$providers[0]->restore();
```

## Seeders

Seeder é uma forma mais fácil de se popular uma tabela no banco de dados através do model em sim.

Depois de criar um seeder e encontra-lo, iremos instanciar o um objeto da classe do model em si e preencher as propriedades.

```php
class ProviderSeeder extends Seeder {
    public function run() {
        //Inserindo utilizando instanciamento do objeto
        $provider = new Fornecedor();
        $provider->name = 'Gustavo';
        $provider->lastname = 'Camargo Campos';
        $provider->save();

        //Inserindo utilizando a função estatica create
        //lembrando que a variavel $fillable deve estar preenchida no model.
        Provider::create([
            'name'=>'Gustavo',
            'lastname'=>'Camargo Campos'
        ]);
    }
}


```

Após isso, devemos localizar o arquivo `DatabaseSeeder.php` localizado em `database/seeders` e dentro do método `run()` chamar a classe seeder que você criou

```php
class DatabaseSeeder extends Seeder {
    public function run(){
        $this->call(ProviderSeeder::class)
    }
}
```

Uma vez chamado no arquivo `DatabaseSeeder`, basta executar o comando para rodar o seeder.

Caso em algum momento, você insira outro seeder e não quer repopular seu banco de dados com as informações que você já inserir no passado, utilize o comando `php artisan db:seed nome_da_classe_seeder`


## Factories

Factories são uma forma de popular um banco de dados em massa usando seeders.

As factories ficam em `database/factories`.

Geralmente são utilizados dados _fakes_ para preenchimento e teste dos mesmo no banco de dados e para isso vamos usar dependencia `Faker` que vem como padrão dentro das factories, podendo ser acessado como uma propriedade da class extendida Factory.

Ao criar a factory usando `php artisan make:factory nome_da_factory --model=nome_da_model` iremos preencher o precisamos dentro da classe criada no return do metodo `definition`.

```php
class ProviderFactory extends Factory {
    public function definition() {
        return [
            'name' => $this->faker->name()
        ];
    }
}
```

Uma vez preenchido a factory, devemos chamar ela com a função estática `factory()` do model hasFactory vindo lá do seu model dentro do seu respectivo seeder.

```php
class ProviderSeeder extends Seeder{
    public function run() {
        Provider::factory()->count(20)->create(); // count() vai definir quantas vezes essa seed vai ser implementada.
    }
}
```

Por fim, basta executar `php artisan db:seed --class=ProviderSeeder` para que os registros sejam inseridos no banco.


## Request

Todas as vezes que acessamos uma rota, o navegador manda uma requisição para a rota especificada e está rota por sua vez passa essa informação para o Controller responsável, onde o mesmo pode receber essa informação pela passagem de parametro da função


```php
class ProvidersController extends Controller {
    public function index(Request $request) {

    }
}
```

Podemos capturar os dados vindo de um método POST de um formulário, usando o método `all()`, trazendo em forma de array associativo os dados vindo do formulário, sendo a key os valores inseridos nos `name` dos input e o valor, sendo o que foi passado enviado propriamente dito no formulário.


```php
class ProvidersController extends Controller {
    public function index(Request $request) {
        print_r($request->all());
    }
}
```

Podemos capturar um dado especifico desse array usando o método `input()` 


```php
class ProvidersController extends Controller {
    public function index(Request $request) {
        echo $request->input('email'); //vai retornar o valor do input de name email
    }
}
```

Sendo assim, podemos inserir os dados vindos do formulário no banco de dados. Para isso, teremos que trazer para o contexto da classe `ProvidersController` o seu model correspondente, ou seja, `Provider` e instanciar o objeto do mesmo.

```php
use \App\Models\Provider;

class ProvidersController extends Controller {
    public function index(Request $request) {
        $provider = new Provider();

        $provider->name = $request->input('name');
        $provider->save(); // método save para persistir a alteração no banco de dados.
    }
}
```

Podemos usufruir da facilidade do método `fill()` em conjunto do método `all()`, visto que o método `all()` retorna um array associativo dos valores do formulário. Isso só sera possivel se o nome das colunas forem os mesmo dos `name` no formulário HTML

```php
class ProvidersController extends Controller {
    public function index(Request $request) {
        $provider = new Provider();
        $provider->fill($request->all())->save();
    }
}
```

Também, podemos usar o método `create()` no lugar do metodo `fill()`


```php
class ProvidersController extends Controller {
    public function index(Request $request) {
        $provider = new Provider();
        $provider->create($request->all())->save();
    }
}
```

Ou caso queria encurtar ainda mais, não precisando da instanciação do objeto, usando de forma estática o metodo `create()`


```php
class ProvidersController extends Controller {
    public function index(Request $request) {
        Provider::create($request->all())->save();
    }
}
```


## Validação de dados do Request

Para validar os dados que vêm do Request, visto que, caso o dado não possa ir como `null` para o banco de dados, usamos o método `validate()` que espera um array associativo de validação;

```php
class ProvidersController extends Controller {
    public function save(Request $request) {
        $request->validate([

        ]);
    }
}
```

Esse array vai receber como _key_ o name do input e o valor vai ser as validações

```php
class ProvidersController extends Controller {
    public function save(Request $request) {
        $request->validate([
            'name' => 'required',
        ]);
    }
}
```

Para realizar a alteração da mensagem de erro, basta inserir mais um array no metodo `validate()`, passando como _key_ [name_do_input].[validacao] 

```php
class ProvidersController extends Controller {
    public function save(Request $request) {
        $request->validate([
            'name' => 'required',
        ],
        [
            'name.required' => 'O campo nome é obrigatório',
        ]);
    }
}
```

As regras que podem ser aplicadas como value do array assoc, podem ser encontradas [clicando aqui.](https://laravel.com/docs/9.x/validation#available-validation-rules)

Agora, quando usarmos a rota o método `save()` em alguma rota de POST, o Laravel vai retornar para a mesma rota, porém, enviando erros na variavel `$errors`.

### Metodo old()

Como o laravel retorna para a rota que chamou o controlador que continha o metodo `validate()` com os dados da váriavel `$errors` contendo as mensagens das validações, todos os possiveis campos que passaram pela validação estão apagados, isso é uma má experiencia para o usuário ter de digitar novamente tudo que ele digitou antes. Para que isso não aconteca, no formulário, precisamos _setar_ na propriedade `value` dos input's o valor da váriavel `old()`, que vai esperar como parametro o `name` do input para retornar o último valor daquele input.

```html
<input type="text" value="{{ old('name') }}" name="name" placeholder="Nome">
```

Assim, o Laravel vai entender que, quando ele recarregar a página com o retorno das validações, vai saber que deve repopular aquele input com os dados do último request.


## Midllewares

São camadas de softwares aplicadas entre aplicações distintes. Atuam na interceptação das requisição HTTP feita por browser e tambem na manipulação das respostas feitas para esss browsers.

Para criar um middleware basta usar o comando `php artisan make:middleware nome_do_middleware` e ele será criado no diretório `app\Http\Middleware`

Dentro do Middleware, a função que vai ser chamada assim que o middleware for acionado vai ser a `handle(Request $request, Closure $next)`

```php
class LogAccessMiddleware {
    public function handle(Request $request, Closure $next) {
        return $next($request);
    }
}
```
### Middlewares em Rotas

Os middlewares podem ser usados tanto em rotas quanto em controllers, por agora, iremos falar dentro das rotas.

Nas rotas, devemos trazer para o contexto do `web.php` a classe do middleware. Em seguida, temos que inserir o middlare na rota e podemos fazer isso de qualquer forma, porém, a que me faz mais sentido e inserir o middleware como 'função estatica' de `Route`

```php
use \App\Http\Middleware\LogAccessMiddleware;
use \App\Http\Controllers\HomeController;

Route::middleware(LogAccessMiddleware::class)
    ->get('/', [HomeController::class, 'principal'])
    ->name('site.index');
```

Dessa forma faz mais sentido, visto que, antes de acessar de fato o controller, a requisição vai passar pelo middleware.

### Middleware em Controllers
Para utilizar o Middleware em contexto de Controller, devemos inserir no construtor do Controller, no método `$this->middleware()`

```php
use App\Http\Middleware\LogAccessMiddleware;

class AboutController extends Controller
{
    public function __construct()
    {
        $this->middleware(LogAccessMiddleware::class);
    }
}
```
### Universalidade dos Middlewares
Podemos fazer com que todos as rotas sejam web ou api utilizem o mesmo middleware sempre, basta inserir na propriedade `$middlewareGroups` do classe `Kernel`, localizado em `App/Http/Kernel.php`

```php

class Kernel extends HttpKernel {
    protected $middlewareGroups = [
        web => [
            ...,
            \App\Http\Middleware\LogAccessMiddleware::class,
        ]   
    ]
}
```

Assim todas as rotas web passaram a usar esse middleware sempre que forem requisitadas.

### Nomeando Middlewares

Para nomear middlewares, precisamos inseri-lo dentro da variavel array `protected $routeMiddleware`, onde, como _key_ iremos inserir o apelido do Middleware, e seu _value_ sera a classe do middleware propriamente dita.

````php
class Kernel extends HttpKernel {
    protected $routeMiddleware = [
        ...,
        'log.access' => \App\Http\Middleware\LogAccessMiddleware::class,
    ]
}
````

Agora, tanto nas rotas quanto nos controladores, podemos apenas passar o apelido do middleware dentro do método `middleware()`.

- Route
    ````php
    Route::get('/about', [AboutController::class, 'about'])->name('site.about')->middleware('log.access');
    ````
- Controller
    ````php
    class AboutController extends Controller {
        public function __construct() { $this->middleware('log.access') }
    }   
    ````

### Passando parametros para os Middlewares

Caso seja necessário, podemos passar parametros para os middleware, inserindo um prefixo na chamado do middleware `:[params]`, separando-os por virgulas e capturando eles como parametros da função `handle().`

- Route
```php
    Route::get('/about', [AboutController::class, 'about'])->name('site.about')->middleware('log.access:param1, paramm2');
```
- Middleware
```php
    class LogAccessMiddleware {
        public function handle ($request, $next, $param1, $param2){
            
        }
    }
```

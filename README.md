# Crud App

Está aplicação é um exemplo de um sistema CRUD (Create, Read, Update, Delete) construído com laravel.

# Passo a Passo

## Criação do Modelo

O modelo é o arquivo responsável pela lógica de negócios da aplicação. Ele representa a estrutura dos dados e as regras de manipulação desses dados. Para criar um modelo no Laravel, você pode usar o comando artisan:

```bash
php artisan make:model NomeDoModelo
```

Isso criará um arquivo de modelo na pasta `app/Models`. Você pode então definir as propriedades e métodos do modelo conforme necessário.

Na aplicação vamos utilizar o modelo `Produto` com as seguintes propriedades:

- `id`: Identificador único do produto
- `nome`: Nome do produto
- `descricao`: Descrição do produto
- `preco`: Preço do produto
- `quantidade`: Quantidade disponível do produto

Para criar vamos usar o comando:

```bash
php artisan make:model Produto
```

Isso criará um arquivo de modelo na pasta `app/Models`. Você pode então definir as propriedades e métodos do modelo conforme necessário.

No arquivo recem criado `app/Models/Produto.php`, você pode definir as propriedades do modelo:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Produto extends Model
{
    use HasFactory;

    protected $fillable = [
        'nome',
        'descricao',
        'preco',
        'quantidade',
    ];
}
```

## Criação da Migration

A migration é o arquivo responsável por criar e modificar as tabelas do banco de dados. Para criar uma migration no Laravel, você pode usar o comando artisan:

```bash
php artisan make:migration create_nome_da_tabela_table
```

Para o modelo de produtos, você pode criar a migration com o seguinte comando:

```bash
php artisan make:migration create_produtos_table
```

Isso criará um arquivo de migration na pasta `database/migrations`. Você pode então definir a estrutura da tabela no método `up` da migration:

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateProdutosTable extends Migration
{
    public function up()
    {
        Schema::create('produtos', function (Blueprint $table) {
            $table->id();
            $table->string('nome');
            $table->text('descricao');
            $table->decimal('preco', 10, 2);
            $table->integer('quantidade');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('produtos');
    }
}
```

A função Up é responsável por criar a tabela `produtos` com as colunas especificadas. A função Down é responsável por reverter a criação da tabela, caso seja necessário.

Após criar o arquivo de migration, você pode executá-lo com o seguinte comando:

```bash
php artisan migrate
```

Isso aplicará todas as migrations pendentes e criará a tabela `produtos` no banco de dados.

## Criação do Controller

```bash
php artisan make:controller ProdutoController
```

Isso criará um arquivo de controller na pasta `app/Http/Controllers`. Você pode então definir as ações do controller conforme necessário.

```php
namespace App\Http\Controllers;

use App\Models\Produto;
use Illuminate\Http\Request;

class ProdutoController extends Controller
{
    public function index()
    {
        $produtos = Produto::all();
        return view('produtos.index', compact('produtos'));
    }

    public function create()
    {
        return view('produtos.create');
    }

    public function store(Request $request)
    {
        Produto::create($request->all());
        return redirect()->route('produtos.index');
    }

    public function edit(Produto $produto)
    {
        return view('produtos.edit', compact('produto'));
    }

    public function update(Request $request, Produto $produto)
    {
        $produto->update($request->all());
        return redirect()->route('produtos.index');
    }

    public function destroy(Produto $produto)
    {
        $produto->delete();
        return redirect()->route('produtos.index');
    }
}
```

O controller será o responsável por lidar com as requisições HTTP e interagir com o modelo para realizar as operações CRUD.

## Criação das Views

Após a criação da estrutura de modelo, migration e controller, o próximo passo é criar as views para a aplicação. As views são responsáveis por exibir os dados para o usuário e fornecer formulários para criar e editar produtos.

As views serão criadas na pasta `resources/views/produtos`. Você pode criar as seguintes views:

- `index.blade.php`: Para listar todos os produtos.
- `create.blade.php`: Para exibir o formulário de criação de um novo produto.
- `edit.blade.php`: Para exibir o formulário de edição de um produto existente.

### Conteúdo das Views

#### index.blade.php

```blade
@extends('layouts.app')

@section('content')
    <h1>Lista de Produtos</h1>
    <a href="{{ route('produtos.create') }}">Criar Novo Produto</a>
    <table>
        <thead>
            <tr>
                <th>ID</th>
                <th>Nome</th>
                <th>Descrição</th>
                <th>Preço</th>
                <th>Quantidade</th>
                <th>Ações</th>
            </tr>
        </thead>
        <tbody>
            @foreach ($produtos as $produto)
                <tr>
                    <td>{{ $produto->id }}</td>
                    <td>{{ $produto->nome }}</td>
                    <td>{{ $produto->descricao }}</td>
                    <td>{{ $produto->preco }}</td>
                    <td>{{ $produto->quantidade }}</td>
                    <td>
                        <a href="{{ route('produtos.edit', $produto) }}">Editar</a>
                        <form action="{{ route('produtos.destroy', $produto) }}" method="POST" style="display:inline;">
                            @csrf
                            @method('DELETE')
                            <button type="submit">Excluir</button>
                        </form>
                    </td>
                </tr>
            @endforeach
        </tbody>
    </table>
@endsection
```

#### create.blade.php

```blade
@extends('layouts.app')

@section('content')
    <h1>Criar Novo Produto</h1>
    <form action="{{ route('produtos.store') }}" method="POST">
        @csrf
        <label for="nome">Nome:</label>
        <input type="text" name="nome" id="nome" required>
        <label for="descricao">Descrição:</label>
        <textarea name="descricao" id="descricao" required></textarea>
        <label for="preco">Preço:</label>
        <input type="number" name="preco" id="preco" step="0.01" required>
        <label for="quantidade">Quantidade:</label>
        <input type="number" name="quantidade" id="quantidade" required>
        <button type="submit">Criar</button>
    </form>
@endsection
```

#### edit.blade.php

```blade
@extends('layouts.app')

@section('content')
    <h1>Editar Produto</h1>
    <form action="{{ route('produtos.update', $produto) }}" method="POST">
        @csrf
        @method('PUT')
        <label for="nome">Nome:</label>
        <input type="text" name="nome" id="nome" value="{{ $produto->nome }}" required>
        <label for="descricao">Descrição:</label>
        <textarea name="descricao" id="descricao" required>{{ $produto->descricao }}</textarea>
        <label for="preco">Preço:</label>
        <input type="number" name="preco" id="preco" step="0.01" value="{{ $produto->preco }}" required>
        <label for="quantidade">Quantidade:</label>
        <input type="number" name="quantidade" id="quantidade" value="{{ $produto->quantidade }}" required>
        <button type="submit">Atualizar</button>
    </form>
@endsection
```

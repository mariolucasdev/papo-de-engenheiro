---
title: "Escrevendo Bons Models com CodeIgniter 3"
seoTitle: "Escrevendo Bons Models com Codeigniter 3 por Mário Lucas"
seoDescription: "Os Models no CodeIgniter 3 desempenham um papel fundamental na interação com o banco de dados e na implementação da lógica de negócios. Um Model bem est..."
datePublished: Mon Feb 10 2025 15:38:07 GMT+0000 (Coordinated Universal Time)
cuid: cm6z7wcsq002t09l4hbzmefkw
slug: escrevendo-bons-models-com-codeigniter-3
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1739202014486/74dcb1f9-a751-451c-98e4-dd8ded2f6485.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1739201811631/4cf1913c-9129-4e6a-946e-6e9bf6aa1fc8.png
tags: php, codeigniter, codeigniter-development

---

O CodeIgniter 3 é um framework PHP conhecido pela sua fácil implementação, atualização simplificada e uso dinâmico. Apesar de ser uma tecnologia relativamente antiga, muitas empresas ainda mantêm esse framework em sua base de código devido à sua leveza e eficiência.

Por isso, decidi escrever este artigo para compartilhar alguns insights e boas práticas no uso do CodeIgniter. Hoje vou falar especificamente sobre models. Espero que seja útil para quem trabalha com esse framework no dia a dia.

Os Models no CodeIgniter 3 desempenham um papel fundamental na interação com o banco de dados e na implementação da lógica de negócios. Um Model bem estruturado melhora a organização do código, facilita a manutenção e promove a reutilização.

Boa leitura!

## 1\. O que é um Model no CodeIgniter 3?

No CodeIgniter 3, os **Models** são classes responsáveis por manipular dados, realizando operações como consultas, inserções, atualizações e remoções no banco de dados. Eles são carregados no Controller e retornam os dados necessários para as Views.

### Estrutura Básica de um Model

```php
class Product_model extends CI_Model
{
    public function __construct()
    {
        parent::__construct();
    }
}
```

Este é o esqueleto básico de um Model no CodeIgniter 3. Agora, vamos aprofundar nas boas práticas para escrever um Model eficiente.

## 2\. Boas Práticas para Models no CodeIgniter 3

### 2.1 Mantenha a Lógica de Negócios no Model

Os Models devem conter toda a lógica relacionada ao banco de dados e às regras do negócio, deixando os Controllers responsáveis apenas por orquestrar as ações.

✅ **Bom Exemplo:**

```php
class Product_model extends CI_Model
{
    public function __construct()
    {
        parent::__construct();
    }

    /**
    * @return array<mixed|object>
    **/
    public function findAll(): array
    {
        return $this->db
            ->get('products')
            ->result();
    }
}
```

🚨 **Evite:** Colocar lógica de banco diretamente no Controller, como:

```php
class Produtos extends CI_Controller
{
    public function index(): void
    {
        $query = $this->db->get('products');
        $dados['products'] = $query->result_array();

        $this->load->view('product/listar', $dados);
    }
}
```

Esse código quebra o princípio da separação de responsabilidades, o “S” do SOLID e dificulta a reutilização do código.

### 2.2 Nomeie os Models de Forma Clara e Concisa

Dê nomes que representem corretamente a entidade que o Model gerencia. Por convenção, os nomes dos Models devem ser singulares e terminar com `_model`.

✅ **Exemplos de Nomes Correto:**

* `User_model`
    
* `Order_model`
    
* `Product_model`
    

🚨 **Evite Nomes Confusos:**

* `Usuarios`
    
* `ProdutosModel`
    
* `modelPedido`
    

Isso mantém uma padronização no projeto e facilita a manutenção.

### 2.3 Utilize Métodos para Operações CRUD

Crie métodos específicos para cada operação CRUD (Create, Read, Update, Delete) dentro do Model.

✅ **Bom Exemplo:**

```php
class Product_model extends CI_Model
{
    public function findAll(): array
    {
        return $this->db
            ->get('products')
            ->result();
    }

    public function find(int $id): object|null
    {
        return $this->db
            ->where('id', $id)
            ->get('products')
            ->row();
    }

    public function store(array $data): bool
    {
        return $this->db
            ->insert('products', $data);
    }

    public function update(int $id, array $data): bool
    {
        $this->db
            ->where('id', $id)
            ->update('products', $data);
    }

    public function delete($id): bool
    {
        return $this->db
            ->where('id', $id)
            ->delete('products');
    }
}
```

Isso mantém o código organizado e facilita a reutilização, podemos ainda melhorar já que o nome da tabela se repete em todos os métodos, podemos apenas fazer desta forma:

```php
class Product_model extends CI_Model
{
    protected $table = 'products';
    
    public function findAll(): array
    {
        return $this->db
            ->get($this->table)
            ->result();
    }

    public function find(int $id): object|null
    {
        return $this->db
            ->where('id', $id)
            ->get($this->table)
            ->row();
    }

    public function store(array $data): bool
    {
        return $this->db
            ->insert($this->table, $data);
    }

    public function update(int $id, array $data): bool
    {
        $this->db
            ->where('id', $id)
            ->update($this->table, $data);
    }

    public function delete($id): bool
    {
        return $this->db
            ->where('id', $id)
            ->delete($this->table);
    }
}
```

### 2.4 Utilize Query Builder para Segurança e Performance

O CodeIgniter oferece o Query Builder, que facilita a escrita de consultas SQL seguras e eficientes.

✅ **Usando Query Builder:**

```php
return $this->db
    ->where('email', $email);
    ->get('usuarios');
    ->row();
```

🚨 **Evite SQL Bruto:**

```php
$query = $this->db->query("SELECT * FROM usuarios WHERE email = '$email'");
```

SQL direto é vulnerável a **SQL Injection** e difícil de manter.

### 2.5 Carregamento de Models da Forma Correta

No CodeIgniter, Models devem ser carregados dentro do Controller utilizando `$this->load->model('Nome_model')`.

✅ **Forma Correta:**

```php
class Produtos extends CI_Controller {
    public function __construct() {
        parent::__construct();
        $this->load->model('Product_model');
    }
}
```

🚨 **Evite carregar Models diretamente dentro dos métodos, pois pode prejudicar a performance.**

---

### 2.6 Evite Retornar Objetos Desnecessários

Se precisar de apenas um resultado, utilize `row()` ou `row_array()` para evitar carregar dados desnecessários. Eu particularmente prefiro o row() pois prefiro trabalhar com objetos na view ao invés de array, para um escrita mais simples `$object→attibute` a invés de `$array[‘key‘]`.

✅ **Forma Correta:**

```php
return $this->db
    ->where('id', $id);
    ->get('usuarios');
    ->row();
```

🚨 **Forma Ineficiente:**

```php
return $this->db
    ->where('id', $id);
    ->get('usuarios')
    ->result(); // Retorna um array de objetos, mesmo que seja apenas um resultado
```

Isso economiza memória e melhora a performance.

## Conclusão

Escrever bons Models no **CodeIgniter 3** melhora a organização do projeto, torna o código mais limpo, facilita a manutenção e aumenta a segurança da aplicação.

**Resumo das melhores práticas:**

✅ Centralize a lógica de negócios no Model.  
✅ Use nomes claros e padronizados para Models.  
✅ Utilize Query Builder para segurança e eficiência.  
✅ Separe as operações CRUD em métodos específicos.  
✅ Evite retornar objetos desnecessários para otimizar memória.

Seguindo essas práticas, seu código será mais limpo, eficiente e fácil de manter!
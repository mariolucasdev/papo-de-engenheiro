---
title: "Implementando Middlewares com Codeigniter 3"
seoTitle: "Implementando Middlewares com Codeigniter 3"
seoDescription: "No CodeIgniter 3, não existe um sistema nativo de middlewares como em frameworks mais modernos, mas é possível implementar uma estratégia eficiente usana..."
datePublished: Tue Feb 11 2025 16:14:23 GMT+0000 (Coordinated Universal Time)
cuid: cm70omue3000g0aju3mv1gbjw
slug: implementando-middlewares-com-codeigniter-3
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1739290291045/639ad41a-51f4-4806-848d-27a2894344da.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1739290396385/4a0e18c4-9b91-4ca0-a995-d5f716eceb70.png
tags: codeigniter, middleware, codeigniter-development, codeigniter3

---

No **CodeIgniter 3**, não existe um sistema nativo de *middlewares* como em frameworks mais modernos, mas é possível implementar uma estratégia eficiente usando ***Hooks***, ***Filters*** ou ***Libraries*** personalizadas. Aqui estão algumas abordagens para implementação desse recurso poderosos que são middlewares.

---

## 1\. **Usando Hooks**

O **CodeIgniter 3** tem um sistema de *hooks* que permite executar código antes e depois de certos eventos. Podemos criar um middleware usando o *hook* `pre_controller`, que é executado antes do carregamento do controlador para o exemplo vamos usar um sistema para verificar se o usuário está logado.

### **Passo 1: Ativar Hooks no Config**

No arquivo `application/config/config.php`, ative os hooks:

```php
$config['enable_hooks'] = TRUE;
```

### **Passo 2: Criar o Middleware como um Hook**

Abra `application/config/hooks.php` e adicione:

```php
$hook['pre_controller'][] = [
    'class'    => 'AuthMiddleware',
    'function' => 'handle',
    'filename' => 'AuthMiddleware.php',
    'filepath' => 'hooks',
    'params'   => []
];
```

### **Passo 3: Criar o Arquivo do Middleware**

Crie o arquivo `application/hooks/AuthMiddleware.php`:

```php
class AuthMiddleware
{
    public function handle(): void
    {
        $ci =& get_instance();

        $ci->load->library('session');

        if (!$ci->session->userdata('logged_in')) {
            redirect('login');
        }
    }
}
```

---

## 2\. **Usando um Controller Base**

Outra opção é criar um controlador base e fazer com que todos os outros herdem dele, essa lógica me parece muito interessante quando temos regras para áreas distintas dentro de um sistema só com papeis diversos, como por exemplo: Admin que gerencia tenants, tenants e clientes do tenant por exemplo.

Então poderia ter um CI\_Tenant\_Controller, CI\_Admin\_Controller e um CI\_Client\_Controller e tem middlewares e validações para cada ambiente com suas necessidades de validação.

### **Passo 1: Criar o Controller Base**

Crie `application/core/CI_Tenant_Controller.php`:

```php
class CI_Tenant_Controller extends CI_Controller
{
    public function __construct()
    {
        parent::__construct();

        // nossas verificações vão aqui para os tenants
        $this->checkAuth();
        $this->checkAccountDataCompleted();
        $this->checkIsDefaulter();
    }

    private function checkAuth(): void
    {
        // ...
    }

    private function checkAccountDataCompleted(): void
    {
        //..
    }

    private function checkIsDefaulter(): void
    {
        //...
    }
}
```

### **Passo 2: Fazer os Controllers Herdarem** CI\_Tenant\_Controller

Agora, qualquer controller que precise do middleware de tenant deve estender CI\_Tenant\_Controller:

```php
class Dashboard extends CI_Tenant_Controller
{
    public function index(): void
    {
        echo "Bem-vindo ao painel!";
    }
}
```

---

## 3\. **Usando Libraries para Middleware Personalizado**

Se quiser uma abordagem modular, você pode criar uma *library* para gerenciar autenticação e outras funções de *middleware*.

### **Passo 1: Criar a Library**

Crie `application/libraries/AuthMiddleware.php`:

```php
class AuthMiddleware
{
    private $ci;

    public function __construct()
    {
        $this->ci =& get_instance();
        $this->ci->load->library('session');
    }

    public function check() {
        if (!$this->ci->session->userdata('logged_in')) {
            
            redirect('login');
        }
    }
}
```

### **Passo 2: Chamar o Middleware nos Controllers**

No controller, basta carregar a *library*:

```php
class Dashboard extends CI_Controller
{    
    public function __construct()
    {
        parent::__construct();

        $this->load->library('AuthMiddleware');

        $this->authmiddleware->check();
    }

    public function index(): void
    {
        echo "Bem-vindo ao painel!";
    }
}
```

---

## **Qual estratégia escolher?**

* **Usar Hooks** → Se precisar de um middleware global sem modificar controllers.
    
* **Usar CI\_Tenant\_Controller** → Se quiser um método direto e organizado e ainda flexível.
    
* **Usar Libraries** → Se precisar de mais flexibilidade e reutilização do código.
    

Dependendo do desafio que você tem pela frente, você ainda pode combinar as abordagens.
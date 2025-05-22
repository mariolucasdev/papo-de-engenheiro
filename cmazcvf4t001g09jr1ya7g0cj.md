---
title: "DRY, KISS e YAGNI: Três Princípios que Evitam que Seu Código PHP Vire um Monstro de Frankenstein"
seoTitle: "DRY, KISS e YAGNI: Três Princípios que Evitam que Seu Código PHP Vire"
seoDescription: "Você já olhou para um código seu de 6 meses atrás e pensou:
“Quem foi o maníaco que escreveu isso?”
Pois é... provavelmente foi você mesmo.

A verdade é que"
datePublished: Thu May 22 2025 12:36:10 GMT+0000 (Coordinated Universal Time)
cuid: cmazcvf4t001g09jr1ya7g0cj
slug: dry-kiss-e-yagni-tres-principios-que-evitam-que-seu-codigo-php-vire-um-monstro-de-frankenstein
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1747917324105/d8a781b7-a1ef-4cf7-8cdb-01e68cc6ca60.png
tags: dry, kiss, yagni

---

Você já olhou para um código seu de 6 meses atrás e pensou:  
**“Quem foi o maníaco que escreveu isso?”**  
Pois é... provavelmente foi você mesmo.

A verdade é que escrever código que **funciona** é fácil. O desafio começa quando ele precisa ser **mantido**, **entendido** e **refatorado sem traumas**. Para isso, existem três princípios sagrados da engenharia de software — quase como os três mosqueteiros do código limpo: **DRY**, **KISS** e **YAGNI**.

Hoje vamos conhecer (ou lembrar) cada um deles com exemplos em PHP. Então segura o café e vem comigo.

---

## 1\. DRY – *Don't Repeat Yourself*

DRY é tipo aquele amigo que não aguenta ver alguém contando a mesma história duas vezes na roda. Ele levanta a sobrancelha e fala:  
**“Já ouvimos isso, amigo.”**

No código, repetir lógica é como contar a mesma história em várias partes do sistema. E se você precisar mudar essa “história”? Vai ter que lembrar **todos os lugares** onde contou ela. Boa sorte com isso...

### 📌 Exemplo ruim (violando DRY):

```php
$discountedPrice1 = $productPrice1 - ($productPrice1 * 0.1);
$discountedPrice2 = $productPrice2 - ($productPrice2 * 0.1);
$discountedPrice3 = $productPrice3 - ($productPrice3 * 0.1);
```

Se você copiar e colar esse cálculo mais uma vez, nasce um gremlin no seu projeto.

### ✅ Exemplo melhor (aplicando DRY):

```php
function applyDiscount($price, $percent = 10) {
    return $price - ($price * ($percent / 100));
}

$discountedPrice1 = applyDiscount($productPrice1);
$discountedPrice2 = applyDiscount($productPrice2);
$discountedPrice3 = applyDiscount($productPrice3);
```

Agora sim! Um único lugar para ajustar a lógica, e a paz reina no reino da manutenção.

---

## 2\. KISS – *Keep It Simple, Stupid*

A tradução mais sincera seria:  
**“Não complica, gênio.”**

Você não está escrevendo um enigma para um concurso da NASA. Código bom é aquele que até o seu **"eu do futuro com sono e pressa"** entende sem precisar de um mapa mental e um psicólogo.

### 📌 Exemplo ruim (overengineering nível hard):

```php
function multiply($a, $b) {
    $result = 0;
    for ($i = 0; $i < $b; $i++) {
        $result += $a;
    }
    return $result;
}
```

Sério? Você reinventou a multiplicação? Parabéns, você desbloqueou o troféu de "Complicador do Ano".

### ✅ Exemplo melhor (aplicando KISS):

```php
function multiply($a, $b) {
    return $a * $b;
}
```

Menos é mais. E mais simples também.

---

## 3\. YAGNI – *You Aren’t Gonna Need It*

Sabe aquele impulso de "deixar pronto pra quando precisarmos"?  
**Spoiler:** **você provavelmente nunca vai precisar.**

YAGNI é o cara pragmático do grupo. Ele olha pro escopo e diz:  
**“Isso aqui não tá no requisito? Então não gasta energia agora.”**

### 📌 Exemplo ruim (violando YAGNI):

```php
class Customer {
    public function save() {
        // Save to database
    }

    public function sendPromotionalSMS() {
        // Might need it someday... maybe
    }

    public function exportToExcel() {
        // Just in case aliens demand it
    }
}
```

Parabéns, agora você tem uma classe inflada com funções inúteis que vão dar erro quando o cliente realmente pedir pra usar — dois anos depois.

### ✅ Exemplo melhor (aplicando YAGNI):

```php
class Customer {
    public function save() {
        // Save to database
    }
}
```

Foca no que importa agora. Quando o cliente quiser SMS e Excel, você cobra mais — e implementa com calma.

---

## PHP moderno ama esses princípios

Esses três caras funcionam tanto em **Laravel**, quanto em **CodeIgniter** e até naquele projeto com PHP espaguete que você herdou do estagiário de 2012.

* No Laravel: use **services**, **helpers**, e **request validation** para evitar repetições.
    
* No CodeIgniter: não enfie dez helpers no autoload achando que vai usar todos.
    
* Em PHP puro: mantenha as funções pequenas, reutilizáveis e evite “futurologia arquitetural”.
    

---

## Conclusão: Código limpo não é só bonito, é sobrevivência

Se você aplicar **DRY**, **KISS** e **YAGNI**, vai escrever código mais:

* Sustentável (não precisa de gambiarra de última hora).
    
* Leve (não carrega complexidade desnecessária).
    
* Elegante (e seu eu do futuro vai te agradecer com um café e uma lágrima de gratidão).
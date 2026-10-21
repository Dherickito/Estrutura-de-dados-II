# Trabalho de Estruturas de Dados — Pilhas e Filas em Python

Neste trabalho eu resolvi três exercícios usando pilha e fila. Em cada um eu explico primeiro como entendi a ideia, depois mostro o código e por fim faço o teste de mesa, para conferir se o resultado bate com o que eu esperava.

---

## Exercício 1 — Botão "Voltar" do navegador (Pilha)

### Enunciado

Implementar uma pilha (LIFO) para simular o botão "Voltar" de um navegador: abrir páginas, voltar para a anterior e mostrar o histórico.

### Como eu entendi

A pilha funciona como uma pilha de pratos: o último prato que eu coloco é o primeiro que eu tiro. LIFO quer dizer *Last In, First Out* (o último a entrar é o primeiro a sair).

Isso combina com o botão "Voltar", porque quando eu clico nele a página que sai é sempre a **última que eu abri**. As páginas mais antigas ficam guardadas embaixo, esperando.

Para fazer isso em Python usei uma lista comum:

- `append()` coloca no final da lista (isso é o **empilhar**);
- `pop()` tira do final da lista (isso é o **desempilhar**).

Então o **final da lista é o topo** da pilha.

Uma coisa que eu percebi é que se eu chamar `pop()` numa lista vazia o programa dá erro (`IndexError`). Por isso, antes de voltar, eu verifico se tem alguma página no histórico.

### Código

```python
class Navegador:
    def __init__(self):
        # começo com a pilha vazia (nenhuma página aberta ainda)
        self.pilha_paginas = []

    def visitar(self, pagina):
        # empilhar: a página nova vai para o topo (final da lista)
        self.pilha_paginas.append(pagina)
        print(f"Página aberta: {pagina}")

    def voltar(self):
        # só dá para voltar se tiver alguma página na pilha
        if len(self.pilha_paginas) > 0:
            # pop() tira e devolve o último, que é o topo da pilha
            pagina_fechada = self.pilha_paginas.pop()
            print(f"Voltando, fechando a página: {pagina_fechada}")
        else:
            print("Não há páginas para voltar!")

    def ver_historico(self):
        # mostra da base (mais antiga) até o topo (mais recente)
        print(f"Histórico atual (base -> topo): {self.pilha_paginas}")


# --- Testando ---
nav = Navegador()

nav.visitar("google.com")
nav.visitar("wikipedia.org")
nav.visitar("youtube.com")

print("\n--- Clicando em 'Voltar' ---")
nav.voltar()
nav.voltar()
nav.ver_historico()
```

### Teste de mesa

| Passo | Comando | Pilha (base → topo) |
|---|---|---|
| 1 | `visitar("google.com")` | google |
| 2 | `visitar("wikipedia.org")` | google, wikipedia |
| 3 | `visitar("youtube.com")` | google, wikipedia, youtube |
| 4 | `voltar()` (sai o youtube) | google, wikipedia |
| 5 | `voltar()` (sai a wikipedia) | google |

### Saída do programa

```
Página aberta: google.com
Página aberta: wikipedia.org
Página aberta: youtube.com

--- Clicando em 'Voltar' ---
Voltando, fechando a página: youtube.com
Voltando, fechando a página: wikipedia.org
Histórico atual (base -> topo): ['google.com']
```

A saída ficou como eu previ no teste de mesa: saíram primeiro o youtube e depois a wikipedia, e só o google ficou no histórico.

---

## Exercício 2 — Pedidos de uma lanchonete (Fila com deque)

### Enunciado

Implementar uma fila (FIFO) usando `deque` para simular os pedidos de uma lanchonete: anotar pedidos, entregar na ordem em que chegaram e ver quais ainda estão pendentes.

### Como eu entendi

A fila é o contrário da pilha. FIFO quer dizer *First In, First Out* (o primeiro a entrar é o primeiro a sair), igual à fila do caixa: quem chegou primeiro é atendido primeiro.

- Pedido novo entra no **final** da fila → `append()`.
- Pedido entregue sai do **início** da fila → `popleft()`.

Por que `deque` e não uma lista normal? Pesquisei e vi que o `pop(0)` da lista é lento: depois de tirar o primeiro elemento, o Python precisa puxar todos os outros uma posição para trás. O `deque` foi feito para tirar do começo de forma rápida, então é a escolha certa para fila.

Um erro que eu quase cometi: se eu usar `pop()` no deque, ele tira do **final**, e aí vira pilha e não fila. Para fila precisa ser `popleft()`.

### Código

```python
# o deque precisa ser importado do módulo collections
from collections import deque


class FilaPedidos:
    def __init__(self):
        # fila começa vazia
        self.pedidos = deque()

    def receber_pedido(self, pedido):
        # pedido novo vai para o final da fila
        self.pedidos.append(pedido)
        print(f"Pedido anotado: '{pedido}' (Em preparo...)")

    def entregar_pedido(self):
        # só entrega se existir algum pedido na fila
        if len(self.pedidos) > 0:
            # popleft() tira o primeiro, ou seja, o pedido mais antigo
            pedido_atual = self.pedidos.popleft()
            print(f"Entregando agora: '{pedido_atual}'")
        else:
            print("Nenhum pedido pendente no momento.")

    def ver_pedidos(self):
        # transformo em lista só para a impressão ficar mais bonita
        print(f"Pedidos pendentes: {list(self.pedidos)}")


# --- Testando ---
lanchonete = FilaPedidos()

lanchonete.receber_pedido("X-Burger + Refrigerante")
lanchonete.receber_pedido("Porção de Batata Frita")
lanchonete.receber_pedido("Milkshake de Morango")

print("\n--- Iniciando as entregas ---")

lanchonete.entregar_pedido()
lanchonete.entregar_pedido()
lanchonete.ver_pedidos()
```

### Teste de mesa

| Passo | Comando | Fila (início → fim) |
|---|---|---|
| 1 | `receber_pedido("X-Burger...")` | X-Burger |
| 2 | `receber_pedido("Porção de Batata...")` | X-Burger, Batata |
| 3 | `receber_pedido("Milkshake...")` | X-Burger, Batata, Milkshake |
| 4 | `entregar_pedido()` (sai o X-Burger) | Batata, Milkshake |
| 5 | `entregar_pedido()` (sai a Batata) | Milkshake |

### Saída do programa

```
Pedido anotado: 'X-Burger + Refrigerante' (Em preparo...)
Pedido anotado: 'Porção de Batata Frita' (Em preparo...)
Pedido anotado: 'Milkshake de Morango' (Em preparo...)

--- Iniciando as entregas ---
Entregando agora: 'X-Burger + Refrigerante'
Entregando agora: 'Porção de Batata Frita'
Pedidos pendentes: ['Milkshake de Morango']
```

Os pedidos foram entregues na mesma ordem em que foram anotados, então a regra FIFO foi respeitada.

---

## Exercício 3 — Check-in do aeroporto com prioridade (Fila encadeada)

### Enunciado

Ampliar o sistema de atendimento de um balcão de check-in para que passageiros preferenciais sejam atendidos antes dos comuns, organizando corretamente os nós e os ponteiros da lista encadeada.

### Como eu entendi

Esse foi o exercício mais difícil. Aqui não usei lista nem deque: construí a fila com **nós ligados entre si** (lista encadeada).

Cada nó guarda duas coisas: o **passageiro** e o **`proximo`**, que aponta para o nó seguinte. O último nó aponta para `None`, que quer dizer "não tem mais ninguém depois".

```
inicio → [Beatriz] → [Marina] → [Rafael] → None
                                   ↑
                                  fim
```

A fila guarda dois ponteiros: o `inicio` (quem vai ser atendido primeiro) e o `fim` (o último). Guardar o `fim` ajuda porque assim eu não preciso andar a fila toda para colocar alguém no final.

Quando chega um passageiro novo, pensei em **três casos**:

1. **A fila está vazia.** O nó novo vira o `inicio` e o `fim` ao mesmo tempo.
2. **O passageiro é preferencial.** Ele passa na frente dos comuns, mas não dos outros preferenciais (quem chegou antes continua na frente). Aqui tem duas situações:
   - se o primeiro da fila **não** é preferencial, o novo vira o primeiro;
   - se o primeiro **já é** preferencial, eu ando pela fila até achar o último preferencial e encaixo o novo logo depois dele.
3. **O passageiro é comum.** Vai para o final, como numa fila normal.

O ponto que mais exigiu atenção foi a **ordem das duas linhas de ponteiro** no caso 2. Primeiro o novo nó precisa apontar para quem vinha depois, e só então o nó de trás aponta para o novo. Se eu fizesse ao contrário, perderia o resto da fila.

### Código

```python
class Passageiro:
    def __init__(self, nome, idade, categoria):
        self.nome = nome
        self.idade = idade
        self.categoria = categoria  # "Comum" ou "Preferencial"


class No:
    # cada nó guarda um passageiro e aponta para o próximo da fila
    def __init__(self, passageiro):
        self.passageiro = passageiro
        self.proximo = None


class FilaCheckin:
    def __init__(self):
        self.inicio = None   # primeiro da fila
        self.fim = None      # último da fila
        self._tamanho = 0

    def esta_vazia(self):
        return self.inicio is None

    def tamanho(self):
        return self._tamanho

    def adicionar(self, passageiro):
        novo_no = No(passageiro)

        # Caso 1: fila vazia, o novo é o primeiro e o último
        if self.esta_vazia():
            self.inicio = novo_no
            self.fim = novo_no

        # Caso 2: passageiro preferencial
        # (uso lower() para não depender de letra maiúscula ou minúscula)
        elif passageiro.categoria.lower() == "preferencial":

            # 2a: o primeiro não é preferencial, então o novo passa para a frente
            if self.inicio.passageiro.categoria.lower() != "preferencial":
                novo_no.proximo = self.inicio
                self.inicio = novo_no

            # 2b: já tem preferencial na frente, então o novo entra depois do último deles
            else:
                atual = self.inicio
                # ando enquanto o próximo também for preferencial
                while (atual.proximo is not None and
                       atual.proximo.passageiro.categoria.lower() == "preferencial"):
                    atual = atual.proximo

                # primeiro o novo aponta para quem vinha depois...
                novo_no.proximo = atual.proximo
                # ...e só então o último preferencial aponta para o novo
                atual.proximo = novo_no

                # se não tinha ninguém depois, o novo virou o último da fila
                if novo_no.proximo is None:
                    self.fim = novo_no

        # Caso 3: passageiro comum, vai para o final
        else:
            self.fim.proximo = novo_no
            self.fim = novo_no

        self._tamanho += 1

    def listar(self):
        if self.esta_vazia():
            print("Fila vazia.")
            return

        atual = self.inicio
        print("\n--- SITUAÇÃO DA FILA DE CHECK-IN ---")
        posicao = 1
        while atual is not None:
            p = atual.passageiro
            print(f"{posicao}. {p.nome} — {p.idade} anos — [{p.categoria}]")
            atual = atual.proximo
            posicao += 1
        print("------------------------------------\n")


# --- Testando ---
fila = FilaCheckin()
fila.adicionar(Passageiro("Marina", 28, "Comum"))
fila.adicionar(Passageiro("Rafael", 61, "Comum"))
fila.adicionar(Passageiro("Beatriz", 39, "Preferencial"))

fila.listar()
```

### Teste de mesa

| Passo | Comando | Caso | Fila (início → fim) |
|---|---|---|---|
| 1 | `adicionar(Marina, Comum)` | 1 (fila vazia) | Marina |
| 2 | `adicionar(Rafael, Comum)` | 3 (comum, vai ao final) | Marina → Rafael |
| 3 | `adicionar(Beatriz, Preferencial)` | 2a (não há preferencial na frente) | Beatriz → Marina → Rafael |

Para testar o caso 2b, que não aparece no exemplo acima, imaginei que chegasse o **Tiago (Preferencial)** depois da Beatriz:

- O primeiro da fila (Beatriz) é preferencial, então caio no 2b.
- O `while` começa na Beatriz. O próximo dela (Marina) é comum, então o laço para.
- Tiago aponta para Marina, e Beatriz passa a apontar para Tiago.
- Resultado: Beatriz → Tiago → Marina → Rafael.

Fui conferir rodando o código e deu exatamente essa ordem.

### Saída do programa

```
--- SITUAÇÃO DA FILA DE CHECK-IN ---
1. Beatriz — 39 anos — [Preferencial]
2. Marina — 28 anos — [Comum]
3. Rafael — 61 anos — [Comum]
------------------------------------
```

---

## Conclusão

Com esses exercícios eu entendi melhor a diferença entre as estruturas:

| | Pilha | Fila | Fila com prioridade |
|---|---|---|---|
| Regra | LIFO | FIFO | FIFO, com preferenciais na frente |
| Exemplo | Botão Voltar | Pedidos da lanchonete | Check-in do aeroporto |
| Como fiz | Lista (`append` e `pop`) | `deque` (`append` e `popleft`) | Nós e ponteiros |

O que achei mais fácil foi a pilha, porque a lista do Python já tem os métodos prontos. O mais difícil foi a fila com prioridade, principalmente a parte dos ponteiros, em que um erro na ordem das atribuições faz a fila "se perder". Desenhar os nós no papel antes de programar ajudou bastante.

Também aprendi que dá para melhorar o último exercício: ele ainda não tem um método para **chamar o próximo** passageiro (remover o primeiro da fila). Para isso bastaria mover o `inicio` para `inicio.proximo` e, se a fila ficasse vazia, colocar o `fim` como `None`.

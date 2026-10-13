# Pilhas e Filas em Python — Versão Explicada

Este material tem três exercícios. Em cada um você encontra:

1. **O conceito** (com uma analogia do dia a dia)
2. **O enunciado**
3. **Como pensar** (a lógica antes do código)
4. **O código comentado**
5. **O passo a passo da execução**
6. **A saída esperada**
7. **Pontos de atenção** (eficiência e erros comuns)

---

## Exercício 1 — Histórico de Navegador com Pilha (botão Voltar)

### O conceito

Uma **pilha** (*stack*) segue a regra **LIFO** (*Last In, First Out*): o **último** que entra é o **primeiro** que sai.

Pense numa pilha de pratos: você coloca um prato novo em cima e, quando precisa de um, pega o de cima. Nunca tira o do meio ou o de baixo.

Só existem duas operações principais:

| Operação | O que faz | Em Python (lista) |
|---|---|---|
| **push** (empilhar) | coloca no topo | `lista.append(x)` |
| **pop** (desempilhar) | remove do topo | `lista.pop()` |

### Enunciado

Implemente uma Pilha (LIFO) aplicada a um navegador de internet simples, simulando o botão "Voltar": o programa deve permitir abrir páginas, voltar para a anterior e exibir o histórico de navegação.

### Como pensar

- Cada página aberta é **empilhada**. A página atual é sempre a do topo.
- Clicar em "Voltar" **desempilha** a página do topo. A que estava embaixo vira a página atual.
- Antes de desempilhar, é preciso checar se a pilha **não está vazia**. Senão o `pop()` gera erro (`IndexError`).

### Código comentado

```python
class Navegador:
    def __init__(self):
        # __init__ é o construtor: roda quando criamos um Navegador.
        # A pilha começa vazia. Usamos uma lista, onde o FIM da lista é o TOPO da pilha.
        self.pilha_paginas = []

    def visitar(self, pagina):
        """Empilha uma nova página no topo do histórico (push)."""
        # append() adiciona no final da lista, ou seja, no topo da pilha.
        self.pilha_paginas.append(pagina)
        print(f"Página aberta: {pagina}")

    def voltar(self):
        """Desempilha a página atual e retorna à anterior (pop, LIFO)."""
        # len(...) > 0 garante que existe algo para remover.
        if len(self.pilha_paginas) > 0:
            # pop() remove E devolve o último elemento (o topo).
            pagina_fechada = self.pilha_paginas.pop()
            print(f"Voltando, fechando a página: {pagina_fechada}")
        else:
            # Pilha vazia: avisamos em vez de deixar o programa quebrar.
            print("Não há páginas para voltar!")

    def ver_historico(self):
        """Mostra a pilha inteira, da base até o topo (a última é a atual)."""
        print(f"Histórico atual (base -> topo): {self.pilha_paginas}")


# --- Exemplo de uso ---
nav = Navegador()                 # cria o navegador (pilha vazia)

nav.visitar("google.com")         # pilha: [google]
nav.visitar("wikipedia.org")      # pilha: [google, wikipedia]
nav.visitar("youtube.com")        # pilha: [google, wikipedia, youtube]

print("\n--- Clicando em 'Voltar' ---")
nav.voltar()                      # remove youtube (o último a entrar)
nav.voltar()                      # remove wikipedia
nav.ver_historico()               # sobra só o google
```

### Passo a passo da execução

| Comando | Pilha depois do comando (base → topo) | Página atual |
|---|---|---|
| `visitar("google.com")` | `[google]` | google |
| `visitar("wikipedia.org")` | `[google, wikipedia]` | wikipedia |
| `visitar("youtube.com")` | `[google, wikipedia, youtube]` | youtube |
| `voltar()` | `[google, wikipedia]` | wikipedia |
| `voltar()` | `[google]` | google |

### Saída esperada

```
Página aberta: google.com
Página aberta: wikipedia.org
Página aberta: youtube.com

--- Clicando em 'Voltar' ---
Voltando, fechando a página: youtube.com
Voltando, fechando a página: wikipedia.org
Histórico atual (base -> topo): ['google.com']
```

### Pontos de atenção

- `append()` e `pop()` no fim da lista são **O(1)**: custam o mesmo tempo, não importa o tamanho da pilha.
- Sem o `if len(...) > 0`, chamar `voltar()` numa pilha vazia gera `IndexError: pop from empty list`.
- Um navegador real também guarda uma pilha de "Avançar". Aqui simplificamos.

---

## Exercício 2 — Pedidos de Lanchonete com Fila (FIFO)

### O conceito

Uma **fila** (*queue*) segue a regra **FIFO** (*First In, First Out*): o **primeiro** que entra é o **primeiro** que sai.

É a fila do caixa: quem chegou antes é atendido antes.

| Operação | O que faz | Em Python (`deque`) |
|---|---|---|
| **enqueue** (enfileirar) | entra no final | `fila.append(x)` |
| **dequeue** (desenfileirar) | sai do início | `fila.popleft()` |

### Por que `deque` e não lista comum?

Numa lista, `lista.pop(0)` remove o primeiro item, mas precisa **deslocar todos os outros** uma posição para a esquerda. Com muitos itens, isso fica lento (**O(n)**). O `deque` (*double-ended queue*, "fila de duas pontas") é feito para inserir e remover nas duas pontas em tempo constante (**O(1)**).

### Enunciado

Implemente uma Fila (FIFO) usando `deque` para simular o controle de pedidos de uma lanchonete, permitindo anotar pedidos, entregá-los na ordem de chegada e consultar os pedidos pendentes.

### Como pensar

- Pedido novo → vai para o **final** (`append`).
- Entrega → sai do **início** (`popleft`), porque é o pedido mais antigo.
- Antes de entregar, conferir se há pedidos. Se não houver, avisar.

### Código comentado

```python
# O deque não vem "de fábrica": é preciso importá-lo do módulo collections.
from collections import deque


class FilaPedidos:
    def __init__(self):
        # Cria a fila vazia. O início é o lado esquerdo e o fim é o lado direito.
        self.pedidos = deque()

    def receber_pedido(self, pedido):
        """Coloca o novo pedido no final da fila (enqueue)."""
        self.pedidos.append(pedido)
        print(f" Pedido anotado: '{pedido}' (Em preparo...)")

    def entregar_pedido(self):
        """Entrega e remove o pedido mais antigo (dequeue, FIFO)."""
        if len(self.pedidos) > 0:
            # popleft() remove E devolve o item do INÍCIO da fila.
            pedido_atual = self.pedidos.popleft()
            print(f" Entregando agora: '{pedido_atual}'")
        else:
            print(" Nenhum pedido pendente no momento.")

    def ver_pedidos(self):
        """Mostra os pedidos que ainda aguardam."""
        # list(...) converte o deque em lista só para imprimir de forma bonita.
        print(f"Pedidos pendentes: {list(self.pedidos)}")


# --- Testando a lanchonete ---
lanchonete = FilaPedidos()

lanchonete.receber_pedido("X-Burger + Refrigerante")   # fila: [X-Burger]
lanchonete.receber_pedido("Porção de Batata Frita")    # fila: [X-Burger, Batata]
lanchonete.receber_pedido("Milkshake de Morango")      # fila: [X-Burger, Batata, Milkshake]

print("\n--- Iniciando as entregas ---")

lanchonete.entregar_pedido()   # sai o X-Burger (chegou primeiro)
lanchonete.entregar_pedido()   # sai a Batata Frita
lanchonete.ver_pedidos()       # sobra só o Milkshake
```

### Passo a passo da execução

| Comando | Fila depois do comando (início → fim) |
|---|---|
| `receber_pedido("X-Burger...")` | `[X-Burger]` |
| `receber_pedido("Porção de Batata...")` | `[X-Burger, Batata]` |
| `receber_pedido("Milkshake...")` | `[X-Burger, Batata, Milkshake]` |
| `entregar_pedido()` | `[Batata, Milkshake]` |
| `entregar_pedido()` | `[Milkshake]` |

### Saída esperada

```
 Pedido anotado: 'X-Burger + Refrigerante' (Em preparo...)
 Pedido anotado: 'Porção de Batata Frita' (Em preparo...)
 Pedido anotado: 'Milkshake de Morango' (Em preparo...)

--- Iniciando as entregas ---
 Entregando agora: 'X-Burger + Refrigerante'
 Entregando agora: 'Porção de Batata Frita'
Pedidos pendentes: ['Milkshake de Morango']
```

### Pontos de atenção

- **Pilha × Fila:** na pilha, entrega e remoção acontecem no mesmo lado (topo). Na fila, entram por um lado e saem pelo outro.
- Se você usasse `pop()` (sem o `left`) no `deque`, entregaria o pedido **mais novo**, que é o comportamento de pilha. É um erro comum.

---

## Exercício 3 — Fila de Check-in com Prioridade (Aeroporto)

### O conceito

Aqui a fila é construída **à mão**, com uma **lista encadeada**. Cada elemento é um **nó** que guarda duas coisas:

1. o **dado** (o passageiro);
2. um **ponteiro** (`proximo`) que aponta para o nó seguinte.

```
inicio → [Beatriz | •] → [Marina | •] → [Rafael | None]
                                              ↑
                                             fim
```

A fila guarda dois ponteiros: `inicio` (quem será atendido primeiro) e `fim` (último da fila). Guardar o `fim` evita percorrer a fila inteira só para adicionar alguém no final.

### Enunciado

Amplie o sistema de atendimento de um balcão de check-in para que passageiros preferenciais sejam atendidos antes dos passageiros comuns, organizando corretamente os nós e os ponteiros da lista encadeada.

### Como pensar

O novo passageiro cai em um destes três casos:

1. **Fila vazia:** ele vira `inicio` e `fim` ao mesmo tempo.
2. **Ele é preferencial:** precisa furar os comuns, mas **não** os outros preferenciais (quem chegou antes continua na frente). Ele entra **depois do último preferencial**. Se não há nenhum preferencial, vai para o início.
3. **Ele é comum:** vai para o final, como em qualquer fila.

### Código comentado

```python
class Passageiro:
    def __init__(self, nome, idade, categoria):
        self.nome = nome
        self.idade = idade
        self.categoria = categoria   # "Comum" ou "Preferencial"


class No:
    """Um nó da lista encadeada: guarda um passageiro e aponta para o próximo."""
    def __init__(self, passageiro):
        self.passageiro = passageiro
        self.proximo = None          # None = ainda não aponta para ninguém


class FilaCheckin:
    def __init__(self):
        self.inicio = None           # primeiro da fila (próximo a ser atendido)
        self.fim = None              # último da fila
        self._tamanho = 0            # contador de passageiros

    def esta_vazia(self):
        # A fila está vazia quando não existe um primeiro nó.
        return self.inicio is None

    def tamanho(self):
        return self._tamanho

    def adicionar(self, passageiro):
        novo_no = No(passageiro)     # embrulha o passageiro em um nó

        # CASO 1: fila vazia -> o novo nó é, ao mesmo tempo, início e fim.
        if self.esta_vazia():
            self.inicio = novo_no
            self.fim = novo_no

        # CASO 2: passageiro PREFERENCIAL
        # .lower() deixa tudo minúsculo, assim "Preferencial" e "preferencial" valem igual.
        elif passageiro.categoria.lower() == "preferencial":

            # 2a) O primeiro da fila NÃO é preferencial: ninguém preferencial na fila.
            #     O novo passa para a frente de todos.
            if self.inicio.passageiro.categoria.lower() != "preferencial":
                novo_no.proximo = self.inicio   # o novo aponta para o antigo primeiro
                self.inicio = novo_no           # e vira o novo primeiro

            # 2b) Já há preferenciais na frente: entra DEPOIS do último deles.
            else:
                atual = self.inicio
                # Anda pela fila enquanto o PRÓXIMO nó também for preferencial.
                while (atual.proximo is not None and
                       atual.proximo.passageiro.categoria.lower() == "preferencial"):
                    atual = atual.proximo
                # Agora 'atual' é o último preferencial. Encaixamos o novo logo depois:
                novo_no.proximo = atual.proximo   # 1º: novo aponta para quem vinha depois
                atual.proximo = novo_no           # 2º: o último preferencial aponta para o novo

                # Se não havia ninguém depois, o novo é o último da fila: atualiza o fim.
                if novo_no.proximo is None:
                    self.fim = novo_no

        # CASO 3: passageiro COMUM -> vai para o final.
        else:
            self.fim.proximo = novo_no   # o último atual aponta para o novo
            self.fim = novo_no           # e o novo passa a ser o último

        self._tamanho += 1               # em qualquer caso, a fila cresceu em 1

    def listar(self):
        if self.esta_vazia():
            print("Fila vazia.")
            return

        atual = self.inicio              # começa no primeiro nó
        print("\n--- SITUAÇÃO DA FILA DE CHECK-IN ---")
        posicao = 1
        while atual is not None:         # percorre até chegar ao fim (None)
            p = atual.passageiro
            print(f"{posicao}. {p.nome} — {p.idade} anos — [{p.categoria}]")
            atual = atual.proximo        # avança para o próximo nó
            posicao += 1
        print("------------------------------------\n")


# --- Testando a fila com prioridade ---
fila = FilaCheckin()
fila.adicionar(Passageiro("Marina", 28, "Comum"))
fila.adicionar(Passageiro("Rafael", 61, "Comum"))
fila.adicionar(Passageiro("Beatriz", 39, "Preferencial"))

fila.listar()
```

### Passo a passo da execução

| Comando | Caso usado | Fila depois (início → fim) |
|---|---|---|
| `adicionar(Marina, Comum)` | 1 (fila vazia) | `Marina` |
| `adicionar(Rafael, Comum)` | 3 (comum, vai ao final) | `Marina → Rafael` |
| `adicionar(Beatriz, Preferencial)` | 2a (não há preferencial na frente) | `Beatriz → Marina → Rafael` |

### Exemplo extra: o caso 2b em ação

Se agora chegasse **Tiago (Preferencial)**:

- O primeiro da fila (Beatriz) **é** preferencial, então cai no caso 2b.
- O `while` começa em Beatriz. O próximo dela (Marina) **não** é preferencial, então o laço para com `atual = Beatriz`.
- Tiago aponta para Marina e Beatriz aponta para Tiago.
- Resultado: `Beatriz → Tiago → Marina → Rafael`.

Quem chegou antes entre os preferenciais continua na frente. Isso é o que chamamos de prioridade **estável**.

### Saída esperada

```
--- SITUAÇÃO DA FILA DE CHECK-IN ---
1. Beatriz — 39 anos — [Preferencial]
2. Marina — 28 anos — [Comum]
3. Rafael — 61 anos — [Comum]
------------------------------------
```

### Pontos de atenção

- **A ordem das duas linhas de ponteiro importa** no caso 2b. Se você fizesse `atual.proximo = novo_no` primeiro, perderia a referência ao resto da fila.
- Guardar o `fim` torna a inserção de passageiros comuns **O(1)**. Inserir um preferencial pode precisar percorrer os preferenciais já existentes (**O(k)**, sendo k a quantidade deles).
- Este código ainda não tem método para **chamar** o próximo (remover o `inicio`). Se precisar, a lógica é: guardar `self.inicio`, mover `self.inicio` para `self.inicio.proximo` e, se a fila ficar vazia, colocar `self.fim = None`.

---

## Resumo comparativo

| | Pilha | Fila | Fila com prioridade |
|---|---|---|---|
| **Regra** | LIFO (último entra, primeiro sai) | FIFO (primeiro entra, primeiro sai) | FIFO, mas preferenciais furam os comuns |
| **Exemplo** | Botão Voltar | Pedidos da lanchonete | Check-in do aeroporto |
| **Estrutura** | Lista (`append`/`pop`) | `deque` (`append`/`popleft`) | Lista encadeada (nós e ponteiros) |
| **Entrada** | Topo | Final | Depende da categoria |
| **Saída** | Topo | Início | Início |

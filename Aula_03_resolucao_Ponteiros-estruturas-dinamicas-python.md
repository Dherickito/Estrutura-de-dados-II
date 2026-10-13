# Exercícios e Respostas — Referências, Ponteiros e Estruturas Dinâmicas

## Exercício 1 — Referências e identidade de objetos

**Enunciado:** Rode o código abaixo.

```python
a = [10, 20, 30]
b = a

b.append(40)

print("a:", a)
print("b:", b)
print("id(a):", id(a))
print("id(b):", id(b))
```

**Perguntas:**

**1. Qual será a saída?**

R: As duas variáveis, `a` e `b`, vão exibir a mesma lista com os valores 10, 20, 30 e 40. Os identificadores também serão iguais, já que `id()` devolve a identidade do objeto na memória e `b` passou a apontar para o mesmo objeto que `a`.

```
a: [10, 20, 30, 40]
b: [10, 20, 30, 40]
id(a): 139926715091264   (valor de exemplo)
id(b): 139926715091264   (igual ao de 'a')
```

**2. `a` e `b` representam o mesmo objeto?**

R: Sim, os dois nomes apontam para um único objeto.

**3. Por quê?**

R: Porque a instrução `b = a` não cria uma lista nova. Ela apenas faz `b` receber a referência ao objeto que `a` já aponta.

**4. O que aconteceria com `b = a.copy()`?**

R: `b` receberia uma cópia independente de `a`, ou seja, um objeto diferente com o mesmo conteúdo. Com isso, o `append(40)` alteraria apenas `b`, que terminaria com `[10, 20, 30, 40]`, enquanto `a` continuaria como `[10, 20, 30]`.

---

## Exercício 2 — Montando uma cadeia de nós

**Enunciado:** Considere os nós abaixo.

```python
n1 = Node("A")
n2 = Node("B")
n3 = Node("C")
```

Ligue-os na ordem A → B → C → None e depois percorra a estrutura, imprimindo A, B e C.

**Resposta:**

```python
class Node:
    def __init__(self, valor):
        self.valor = valor
        self.proximo = None

n1 = Node("A")
n2 = Node("B")
n3 = Node("C")

# Ligando os nós em sequência
n1.proximo = n2
n2.proximo = n3

# Percorrendo a cadeia a partir do primeiro nó
atual = n1

while atual != None:
    print(f"{atual.valor} -->")
    atual = atual.proximo

print(atual)   # ao final, 'atual' vale None
```

**Saída:**

```
A -->
B -->
C -->
None
```

---

## Exercício 3 — Depuração

**Enunciado:** Analise o código abaixo.

```python
n1 = Node(10)
n2 = Node(20)
n3 = Node(30)

n1.proximo = n2
n2.proximo = n3

atual = n1

while atual is not None:
    print(atual.valor)
```

**Perguntas:**

**1. Qual é o problema?**

R: O programa imprime o número 10 sem parar, em um laço infinito.

**2. Por que o programa não termina?**

R: Porque a variável `atual` nunca é atualizada dentro do laço. Ela continua apontando para o primeiro nó, jamais passa para o `proximo` e, por isso, nunca chega ao `None` que encerraria o `while`.

**3. Qual linha precisa ser acrescentada?**

R: Uma linha dentro do laço, depois do `print`, para avançar ao nó seguinte:

```python
atual = atual.proximo
```

Código corrigido:

```python
class Node:
    def __init__(self, valor):
        self.valor = valor
        self.proximo = None

n1 = Node(10)
n2 = Node(20)
n3 = Node(30)

n1.proximo = n2
n2.proximo = n3

atual = n1

while atual is not None:
    print(atual.valor)
    atual = atual.proximo   # avança para o próximo nó
```

**4. Qual será a saída depois da correção?**

R: Os valores de cada nó, na ordem:

```
10
20
30
```

> **Observação:** o `None` só aparece se for acrescentado um `print(atual)` **depois** do laço (como no Exercício 2). Dentro do laço, esse `print` mostraria o objeto do nó, e não `None`.

---

## Problema real — Sistema de atendimento de uma clínica

**Enunciado:** Uma clínica atende pacientes ao longo do dia. Cada paciente tem nome, idade e prioridade. O sistema deve permitir:

- adicionar um paciente;
- listar os pacientes;
- atender o primeiro da fila;
- verificar se a fila está vazia;
- informar quantos pacientes estão aguardando.

**Exemplo de uso:**

```python
fila = FilaAtendimento()

fila.adicionar(Paciente("Ana", 32, "Normal"))
fila.adicionar(Paciente("Bruno", 70, "Prioridade"))
fila.adicionar(Paciente("Carlos", 45, "Normal"))

fila.listar()

paciente = fila.atender()
print("Atendendo:", paciente.nome)
```

**Resposta:**

```python
class Paciente:
    def __init__(self, nome, idade, prioridade):
        self.nome = nome
        self.idade = idade
        self.prioridade = prioridade


class Node:
    def __init__(self, paciente):
        self.paciente = paciente
        self.proximo = None


class FilaAtendimento:
    def __init__(self):
        self.inicio = None
        self.fim = None
        self._tamanho = 0

    def esta_vazia(self):
        return self.inicio is None

    def tamanho(self):
        return self._tamanho

    def adicionar(self, paciente):
        novo_no = Node(paciente)

        # Fila vazia: o novo nó é, ao mesmo tempo, o primeiro e o último
        if self.esta_vazia():
            self.inicio = novo_no
            self.fim = novo_no
        else:
            # O último nó atual passa a apontar para o novo
            self.fim.proximo = novo_no
            # E o novo nó se torna o último da fila
            self.fim = novo_no

        self._tamanho += 1

    def atender(self):
        if self.esta_vazia():
            print("A fila está vazia!")
            return None

        # Guarda o paciente do início para devolvê-lo no final
        paciente_atendido = self.inicio.paciente

        # O início avança para o segundo da fila
        self.inicio = self.inicio.proximo
        self._tamanho -= 1

        # Se a fila esvaziou, o ponteiro do fim também precisa ser zerado
        if self.inicio is None:
            self.fim = None

        return paciente_atendido

    def listar(self):
        if self.esta_vazia():
            print("Fila vazia.")
            return

        atual = self.inicio
        print("\n--- ESTADO DA FILA ---")
        while atual is not None:
            p = atual.paciente
            print(f"{p.nome} — {p.idade} anos — {p.prioridade}")
            atual = atual.proximo
        print("----------------------\n")


# Exemplo de utilização (conforme o enunciado)
fila = FilaAtendimento()

fila.adicionar(Paciente("Ana", 32, "Normal"))
fila.adicionar(Paciente("Bruno", 70, "Prioridade"))
fila.adicionar(Paciente("Carlos", 45, "Normal"))

fila.listar()

print(f"Quantidade de pacientes na fila: {fila.tamanho()}")

paciente = fila.atender()
print("Atendendo:", paciente.nome)

print(f"Quantidade de pacientes após o atendimento: {fila.tamanho()}")

fila.listar()
```

**Saída:**

```
--- ESTADO DA FILA ---
Ana — 32 anos — Normal
Bruno — 70 anos — Prioridade
Carlos — 45 anos — Normal
----------------------

Quantidade de pacientes na fila: 3
Atendendo: Ana
Quantidade de pacientes após o atendimento: 2

--- ESTADO DA FILA ---
Bruno — 70 anos — Prioridade
Carlos — 45 anos — Normal
----------------------
```

---

## Desafio — Atendimento prioritário

**Enunciado:** Amplie o sistema para que os pacientes com prioridade sejam atendidos antes dos pacientes normais.

**Resposta:**

```python
class Paciente:
    def __init__(self, nome, idade, prioridade):
        self.nome = nome
        self.idade = idade
        self.prioridade = prioridade  # "Normal" ou "Prioridade"


class Node:
    def __init__(self, paciente):
        self.paciente = paciente
        self.proximo = None


class FilaAtendimentoPrioritaria:
    def __init__(self):
        self.inicio = None
        self.fim = None
        self._tamanho = 0

    def esta_vazia(self):
        return self.inicio is None

    def tamanho(self):
        return self._tamanho

    def adicionar(self, paciente):
        novo_no = Node(paciente)

        # Caso 1: a fila está vazia
        if self.esta_vazia():
            self.inicio = novo_no
            self.fim = novo_no

        # Caso 2: o novo paciente tem PRIORIDADE
        elif paciente.prioridade.lower() == "prioridade":
            # Subcaso A: o primeiro da fila não é prioritário,
            # então o novo paciente passa a ser o primeiro.
            if self.inicio.paciente.prioridade.lower() != "prioridade":
                novo_no.proximo = self.inicio
                self.inicio = novo_no
            else:
                # Subcaso B: localizar o último paciente prioritário da fila
                atual = self.inicio
                while (atual.proximo is not None and
                       atual.proximo.paciente.prioridade.lower() == "prioridade"):
                    atual = atual.proximo

                # Encaixar o novo nó entre 'atual' e 'atual.proximo'
                novo_no.proximo = atual.proximo
                atual.proximo = novo_no

                # Se ele ficou no final, o ponteiro 'fim' precisa ser atualizado
                if novo_no.proximo is None:
                    self.fim = novo_no

        # Caso 3: o novo paciente é NORMAL e vai para o final da fila
        else:
            self.fim.proximo = novo_no
            self.fim = novo_no

        self._tamanho += 1

    def atender(self):
        if self.esta_vazia():
            print("A fila está vazia!")
            return None

        paciente_atendido = self.inicio.paciente
        self.inicio = self.inicio.proximo
        self._tamanho -= 1

        if self.inicio is None:
            self.fim = None

        return paciente_atendido

    def listar(self):
        if self.esta_vazia():
            print("Fila vazia.")
            return

        atual = self.inicio
        print("\n--- ESTADO DA FILA (COM PRIORIDADE) ---")
        posicao = 1
        while atual is not None:
            p = atual.paciente
            print(f"{posicao}. {p.nome} — {p.idade} anos — [{p.prioridade}]")
            atual = atual.proximo
            posicao += 1
        print("---------------------------------------\n")


# Teste com os mesmos pacientes do enunciado anterior
fila = FilaAtendimentoPrioritaria()
fila.adicionar(Paciente("Ana", 32, "Normal"))
fila.adicionar(Paciente("Bruno", 70, "Prioridade"))
fila.adicionar(Paciente("Carlos", 45, "Normal"))

fila.listar()
```

**Saída** (adicionando Ana, Bruno e Carlos, como no enunciado):

```
--- ESTADO DA FILA (COM PRIORIDADE) ---
1. Bruno — 70 anos — [Prioridade]
2. Ana — 32 anos — [Normal]
3. Carlos — 45 anos — [Normal]
---------------------------------------
```

---

## Trabalho colaborativo

**Enunciado:** Forme um grupo de 2 a 4 estudantes. Modelem, implementem, testem e depurem o sistema. Encontrem e corrijam pelo menos um erro proposital.

**Resposta:**

```python
class Paciente:
    def __init__(self, nome, idade, prioridade):
        self.nome = nome
        self.idade = idade
        self.prioridade = prioridade  # "Normal" ou "Prioridade"


class Node:
    def __init__(self, paciente):
        self.paciente = paciente
        self.proximo = None


class FilaAtendimento:
    def __init__(self):
        self.inicio = None
        self.fim = None
        self._tamanho = 0

    def esta_vazia(self):
        return self.inicio is None

    def tamanho(self):
        return self._tamanho

    def adicionar(self, paciente):
        novo_no = Node(paciente)

        # 1. Fila totalmente vazia
        if self.esta_vazia():
            self.inicio = novo_no
            self.fim = novo_no

        # 2. Paciente com PRIORIDADE
        elif paciente.prioridade.lower() == "prioridade":
            # Se o primeiro não é prioritário, o novo vai para a frente de todos
            if self.inicio.paciente.prioridade.lower() != "prioridade":
                novo_no.proximo = self.inicio
                self.inicio = novo_no
            else:
                # Avança até o último prioritário (ou até o fim da fila)
                atual = self.inicio
                while (atual.proximo is not None and
                       atual.proximo.paciente.prioridade.lower() == "prioridade"):
                    atual = atual.proximo

                novo_no.proximo = atual.proximo
                atual.proximo = novo_no

                # Correção do erro proposital (ver explicação abaixo)
                if novo_no.proximo is None:
                    self.fim = novo_no

        # 3. Paciente NORMAL: vai para o final
        else:
            self.fim.proximo = novo_no
            self.fim = novo_no

        self._tamanho += 1

    def atender(self):
        if self.esta_vazia():
            return None

        paciente_atendido = self.inicio.paciente
        self.inicio = self.inicio.proximo
        self._tamanho -= 1

        # Se a fila ficou vazia, o 'fim' também deve voltar a ser None
        if self.inicio is None:
            self.fim = None

        return paciente_atendido

    def listar(self):
        if self.esta_vazia():
            print("Fila vazia.")
            return

        atual = self.inicio
        while atual is not None:
            p = atual.paciente
            print(f"- {p.nome} [{p.prioridade}]")
            atual = atual.proximo


# Teste com cinco pacientes variados
fila = FilaAtendimento()
fila.adicionar(Paciente("Ana", 32, "Normal"))
fila.adicionar(Paciente("Bruno", 70, "Normal"))
fila.adicionar(Paciente("Carlos", 45, "Prioridade"))
fila.adicionar(Paciente("Joao", 25, "Normal"))
fila.adicionar(Paciente("Maria", 68, "Prioridade"))

fila.listar()
```

**Saída** (listando ao final, depois de adicionar os cinco pacientes):

```
- Carlos [Prioridade]
- Maria [Prioridade]
- Ana [Normal]
- Bruno [Normal]
- Joao [Normal]
```

**Erro encontrado e correção:** em duas situações o ponteiro `fim` pode ficar desatualizado, e isso faz o próximo paciente normal ser ligado ao nó errado.

- Em `adicionar()`: quando um paciente prioritário é inserido e fica na última posição da fila (todos os pacientes eram prioritários), é preciso fazer `self.fim = novo_no`.
- Em `atender()`: quando o último paciente é atendido e a fila esvazia, é preciso fazer `self.fim = None`.

Sem essas duas linhas, o `fim` continuaria apontando para um nó que já não pertence à fila.

---

## Exercício de revisão — Sistema de atendimento

**Enunciado:** Desenvolva, individualmente, uma versão funcional do sistema. Lista de verificação:

- [ ] Criar a classe `Paciente`
- [ ] Criar a classe `Node`
- [ ] Criar a classe `FilaAtendimento`
- [ ] Implementar `adicionar()`
- [ ] Implementar `atender()`
- [ ] Implementar `listar()`
- [ ] Implementar `esta_vazia()`
- [ ] Implementar `tamanho()`
- [ ] Testar com pelo menos cinco pacientes
- [ ] Registrar um erro encontrado e como foi corrigido

---

## Reflexão final

**1. O que é uma referência em Python?**

R: É o vínculo entre uma variável e um objeto. Quando uma variável recebe outra, as duas passam a apontar para o mesmo objeto, sem que ele seja duplicado.

**2. Qual a diferença entre `b = a` e `b = a.copy()`?**

R: `b = a` cria uma segunda referência ao mesmo objeto, enquanto `b = a.copy()` cria um objeto novo e independente, com o mesmo conteúdo.

**3. Por que uma estrutura encadeada precisa de referências?**

R: Porque cada nó precisa saber onde está o nó seguinte. Sem essa referência, o restante da estrutura se perderia e não haveria como percorrê-la.

**4. Qual erro você encontrou durante a depuração?**

R: Erros ligados a referências incorretas ou desatualizadas, como o `atual` que nunca avançava no laço e o ponteiro `fim` que não era atualizado.

**5. Como as referências ajudam a compreender estruturas dinâmicas?**

R: Elas mostram que o "nó atual" vai mudando a cada passo, seguindo as referências de um nó ao outro, até chegar ao `None`, que marca o fim da estrutura.

from collections import deque


# ===== Exercício 1 — Pilha: Histórico do navegador (botão Voltar) =====
class Navegador:
    def __init__(self):
        # A pilha que guardará as páginas visitadas
        self.pilha_paginas = []

    def visitar(self, pagina):
        """Empilha uma nova página no topo do histórico."""
        self.pilha_paginas.append(pagina)
        print(f"Página aberta: {pagina}")

    def voltar(self):
        """Desempilha a página atual e retorna à anterior (LIFO)."""
        if len(self.pilha_paginas) > 0:
            pagina_fechada = self.pilha_paginas.pop()
            print(f"Voltando, fechando a página: {pagina_fechada}")
        else:
            print("Não há páginas para voltar!")

    def ver_historico(self):
        print(f"Histórico atual (base -> topo): {self.pilha_paginas}")


# ===== Exercício 2 — Fila (deque): Pedidos de uma lanchonete =====
class FilaPedidos:
    def __init__(self):
        # deque é ideal para filas: remover do início é rápido
        self.pedidos = deque()

    def receber_pedido(self, pedido):
        """Coloca o novo pedido no final da fila."""
        self.pedidos.append(pedido)
        print(f"🧾 Pedido anotado: '{pedido}' (Em preparo...)")

    def entregar_pedido(self):
        """Entrega e remove o pedido mais antigo (FIFO)."""
        if len(self.pedidos) > 0:
            pedido_atual = self.pedidos.popleft()
            print(f"🍔 Entregando agora: '{pedido_atual}'")
        else:
            print("🕐 Nenhum pedido pendente no momento.")

    def ver_pedidos(self):
        """Mostra os pedidos que ainda aguardam."""
        print(f"Pedidos pendentes: {list(self.pedidos)}")


# ===== Exercício 3 — Fila com prioridade (lista encadeada): Check-in =====
class Passageiro:
    def __init__(self, nome, idade, categoria):
        self.nome = nome
        self.idade = idade
        self.categoria = categoria  # Ex: "Comum" ou "Preferencial"


class No:
    def __init__(self, passageiro):
        self.passageiro = passageiro
        self.proximo = None


class FilaCheckin:
    def __init__(self):
        self.inicio = None
        self.fim = None
        self._tamanho = 0

    def esta_vazia(self):
        return self.inicio is None

    def tamanho(self):
        return self._tamanho

    def adicionar(self, passageiro):
        novo_no = No(passageiro)

        # Caso 1: fila vazia
        if self.esta_vazia():
            self.inicio = novo_no
            self.fim = novo_no

        # Caso 2: passageiro PREFERENCIAL
        elif passageiro.categoria.lower() == "preferencial":
            if self.inicio.passageiro.categoria.lower() != "preferencial":
                # Ninguém preferencial na fila: passa para a frente
                novo_no.proximo = self.inicio
                self.inicio = novo_no
            else:
                # Entra depois do último preferencial
                atual = self.inicio
                while (atual.proximo is not None and
                       atual.proximo.passageiro.categoria.lower() == "preferencial"):
                    atual = atual.proximo

                novo_no.proximo = atual.proximo
                atual.proximo = novo_no

                if novo_no.proximo is None:
                    self.fim = novo_no

        # Caso 3: passageiro COMUM (vai para o final)
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


if __name__ == "__main__":
    print("=== Exercício 1 ===")
    nav = Navegador()
    nav.visitar("google.com")
    nav.visitar("wikipedia.org")
    nav.visitar("youtube.com")
    print("\n--- Clicando em 'Voltar' ---")
    nav.voltar()
    nav.voltar()
    nav.ver_historico()

    print("\n=== Exercício 2 ===")
    lanchonete = FilaPedidos()
    lanchonete.receber_pedido("X-Burger + Refrigerante")
    lanchonete.receber_pedido("Porção de Batata Frita")
    lanchonete.receber_pedido("Milkshake de Morango")
    print("\n--- Iniciando as entregas ---")
    lanchonete.entregar_pedido()
    lanchonete.entregar_pedido()
    lanchonete.ver_pedidos()

    print("\n=== Exercício 3 ===")
    fila = FilaCheckin()
    fila.adicionar(Passageiro("Marina", 28, "Comum"))
    fila.adicionar(Passageiro("Rafael", 61, "Comum"))
    fila.adicionar(Passageiro("Beatriz", 39, "Preferencial"))
    fila.listar()

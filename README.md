# Título
Trabalho Prático 5

# Autor
Nome: Rodrigo Ferreira Leite<br>
ID: PG60423<br>
Foto:<br>
<img src="https://github.com/user-attachments/assets/b2078170-049c-47ca-96c3-6c5565a3aae8" width="150">

# Resumo
Neste trabalho criei um simulador de uma Vending Machine, onde os dados dos produtos estão guardados num ficheiro JSON e através de um código Python, consigo acessá-lo e executar ações comuns em Máquinas de Vending (Introduzir Moedas, Selecionar Produtos...), alterando o prórpio ficheiro quando necessário, isto foi feito através de um analisador léxico. O processo de resolução foi o seguinte:

- Identificar os possíveis Tokens;
- Dar um Id para cada Token;
- Pensar o que preciso de Capturar para obter o Token;
- Escrever a Expressão Regular para cada Token;
- Ir Desenvolvendo cada Comando um a um e ir Verificando se estava a sair tudo direitinho, através de prints;
- Colocar tudo de Acordo com o que foi pedido no PDF fornecido pelo Professor.
# Resultados

### JSON

<i>stock.json<i>
```
[
    {
        "Cod": "A01",
        "Prod": "Água 0.5L",
        "Quant": "8",
        "Preco": "0.7"
    },
    {
        "Cod": "A02",
        "Prod": "Água 1.5L",
        "Quant": "15",
        "Preco": "1.2"
    },
    {
        "Cod": "A03",
        "Prod": "Refrigerante Lata",
        "Quant": "20",
        "Preco": "1.5"
    },
    {
        "Cod": "A04",
        "Prod": "Refrigerante 600ml",
        "Quant": "10",
        "Preco": "2.5"
    },
    {
        "Cod": "A05",
        "Prod": "Suco 1L",
        "Quant": "5",
        "Preco": "3.0"
    },
    {
        "Cod": "A06",
        "Prod": "Cerveja Lata",
        "Quant": "25",
        "Preco": "2.0"
    },
    {
        "Cod": "A07",
        "Prod": "Cerveja 600ml",
        "Quant": "12",
        "Preco": "4.0"
    },
    {
        "Cod": "A08",
        "Prod": "Energético 250ml",
        "Quant": "18",
        "Preco": "3.5"
    },
    {
        "Cod": "A09",
        "Prod": "Chá Gelado 500ml",
        "Quant": "14",
        "Preco": "2.8"
    },
    {
        "Cod": "A10",
        "Prod": "Água de Coco 1L",
        "Quant": "7",
        "Preco": "4.5"
    }
]
```

### Código Python

<i>TP5.py<i>
``` 
import ply.lex as lex
import json
import math
from datetime import datetime

with open('stock.json', 'r',encoding="utf-8") as file:
    data = json.load(file)

tokens = (
    'LISTAR',
    'MOEDA',
    'SELECIONAR',
    'SAIR',

    'NUMERO',
    'EURO',
    'CENTIMO',

    'VIRGULA',

    'IDENTIFICADOR'
)

t_LISTAR = r'\bLISTAR\b'
t_MOEDA = r'\bMOEDA\b'
t_SELECIONAR = r'\bSELECIONAR\b'
t_SAIR = r'\bSAIR\b'

def t_NUMERO(t):
    r'\d+'
    t.value = int(t.value)
    return t
t_EURO = r'e'
t_CENTIMO = r'c'

t_VIRGULA = r','

t_IDENTIFICADOR = r'\bA\d{2}\b'

t_ignore = ' \t'

def t_error(t):
    print(f"Carácter ilegal {t.value[0]}")
    t.lexer.skip(1)

lexer = lex.lex()

agora = datetime.now()
data_atual = agora.strftime("%d-%m-%Y")

print(f"{data_atual}, Stock Carregado, Estado Atualizado.")
print("Bom Dia. Estou disponível para atender o seu pedido.")
print("Escreve 'SAIR' para terminar.\n")

saldo_euro = 0
saldo_cent = 0
troco = 0

def Saldo(S_euro, S_cent):
    if S_euro == 0: ##caso em que só há centimos no saldo
        print(f"maq: Saldo = {S_cent}c")

    elif S_cent == 0: ##caso em que só há euros no saldo
        print(f"maq: Saldo = {S_euro}e")

    else: ##caso em que há euros e centimos no saldo
        print(f"maq: Saldo = {S_euro}e{S_cent}c")

def Transforma(Dinheiro_Total):
    Dinheiro_Euro = math.floor(Dinheiro_Total) ##transformação de XX.YY em XXe
    Dinheiro_Cent = int(round((Dinheiro_Total - Dinheiro_Euro) * 100)) ##transformação de XX.YY em YYc
    return Dinheiro_Euro, Dinheiro_Cent

moedas = [200, 100, 50, 20, 10, 5]

while True:
    tokens_list = []
    lexer.input(input(">>> "))

    while tok := lexer.token():
        tokens_list.append(tok)

    if tokens_list[0].type == 'LISTAR' and len(tokens_list) == 1:
        print("Cod |      Produto      | Quantidade | Preço")
        print("--------------------------------------------")
        for produto in data:
            cod = produto["Cod"]
            prod = produto["Prod"]
            quant = produto["Quant"]
            preco = produto["Preco"]
            print(f"{cod:<5} {prod:<18} {quant:>7} {preco:>10}")

    elif tokens_list[0].type == 'MOEDA' and len(tokens_list) >= 2:
        for tok in tokens_list[1:]:
            if tok.type == 'NUMERO':
                prox_tok = tokens_list[tokens_list.index(tok) + 1]
                if prox_tok.type == 'EURO':
                    saldo_euro += tok.value
                    troco += tok.value
                elif prox_tok.type == 'CENTIMO':
                    saldo_cent += tok.value
                    troco += tok.value / 100
        Saldo(saldo_euro, saldo_cent)

    elif tokens_list[0].type == 'SELECIONAR' and len(tokens_list) == 2:
        cod_selecionado = tokens_list[1].value
        produto = next((p for p in data if p["Cod"] == cod_selecionado), None)
        if produto is None: ##verificação de código inválido
            print("maq: Código inválido.")

        elif int(produto["Quant"]) <= 0: ##verificação de stock
            print("maq: Produto esgotado.")
        
        elif troco < float(produto["Preco"]): ##verificação de saldo suficiente
            preco_euro, preco_cent = Transforma(float(produto["Preco"]))
            print("maq: Saldo insuficiente para satisfazer o seu pedido.")
            if saldo_euro == 0: ##caso em que só há centimos no saldo
                if preco_euro == 0:
                    print(f"maq: Saldo = {saldo_cent}c; Pedido = {preco_cent}c")
                elif preco_cent == 0:
                    print(f"maq: Saldo = {saldo_cent}c; Pedido = {preco_euro}e")
                else:
                    print(f"maq: Saldo = {saldo_cent}c; Pedido = {preco_euro}e{preco_cent}c")

            elif saldo_cent == 0: ##caso em que só há euros no saldo
                if preco_euro == 0:
                    print(f"maq: Saldo = {saldo_euro}e; Pedido = {preco_cent}c")
                elif preco_cent == 0:
                    print(f"maq: Saldo = {saldo_euro}e; Pedido = {preco_euro}e")
                else:
                    print(f"maq: Saldo = {saldo_euro}e; Pedido = {preco_euro}e{preco_cent}c")

            else: ##caso em que há euros e centimos no saldo
                if preco_euro == 0:
                    print(f"maq: Saldo = {saldo_euro}e{saldo_cent}c; Pedido = {preco_cent}c")
                elif preco_cent == 0:
                    print(f"maq: Saldo = {saldo_euro}e{saldo_cent}c; Pedido = {preco_euro}e")
                else:
                    print(f"maq: Saldo = {saldo_euro}e{saldo_cent}c; Pedido = {preco_euro}e{preco_cent}c")

        else: ##processamento da compra
            troco -= float(produto["Preco"])
            saldo_euro, saldo_cent = Transforma(troco)
            produto["Quant"] = str(int(produto["Quant"]) - 1)
            with open('stock.json', 'w', encoding='utf-8') as f: ##Envia a atualização do stock para o ficheiro json
                json.dump(data, f, indent=4, ensure_ascii=False)

            print(f'maq: Pode retirar o produto dispensado "{produto['Prod']}".')
            Saldo(saldo_euro, saldo_cent)
    elif tokens_list[0].type == 'SAIR' and len(tokens_list) == 1:
        lista_troco = []
        total_cent = troco * 100
        for moeda in moedas:
            contador = 0
            while total_cent >= moeda:
                total_cent -= moeda
                contador += 1
            if moeda == 200 or moeda == 100:
                moeda_str = f"{moeda // 100}e"
            else:
                moeda_str = f"{moeda}c"
            if contador > 0:
                lista_troco.append((contador, moeda_str))

        combo = [f"{x} x {y}" for x, y in lista_troco]

        if len(combo) > 1:
            resultado = ', '.join(combo[:-1]) + ' e ' + combo[-1]
        else:
            resultado = combo[0]
        print("maq: Pode retirar o seu troco:", resultado)
        print("maq: Até à próxima!")
        break
    else:
        print("maq: Comando inválido.")
```

### Exemplo de Output

<img width="388" height="479" alt="image" src="https://github.com/user-attachments/assets/c55d90d0-528c-4c29-b73c-b309ab3fa22e" />

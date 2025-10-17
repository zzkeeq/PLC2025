# Título
Trabalho Prático 4

# Autor
Nome: Rodrigo Ferreira Leite<br>
ID: PG60423<br>
Foto:<br>
<img src="https://github.com/user-attachments/assets/b2078170-049c-47ca-96c3-6c5565a3aae8" width="150">

# Resumo

Neste trabalho criei um analisador léxico para a linguagem SPARQL. O processo de resolução do problema foi o seguinte:

- Identificar os possíveis Tokens;
- Dar um Id para cada Token;
- Pensar o que preciso de Capturar para obter o Token;
- Escrever a Expressão Regular para cada Token;
- Testar cada linha do Input no CMD e Verificar se está tudo bem;
- Corrigir problemas mais profundos.

# Resultados

### JSON

```
[
    {
        "id": "KEYWORDS",
        "expreg": "\\b(select|where|LIMIT)\\b"
    },
    {
        "id": "IDENTIFIERS",
        "expreg": "\\b\\w+\\:\\w+\\b"
    },
    {
        "id": "COMMENTS",
        "expreg": "#.+"
    },
    {
        "id": "STRINGS",
        "expreg": "\"[\\w+\\s]+\""
    },
    {
        "id": "LANGUAGES",
        "expreg": "@\\w+"
    },
    {
        "id": "SYMBOLS",
        "expreg": "[{}\\.]"
    },
    {
        "id": "NUMBERS",
        "expreg": "\\b\\d+\\b"
    },
    {
        "id": "VARIABLES",
        "expreg": "\\?\\w+"
    },
    {
        "id": "RESERVED",
        "expreg": "(?<=\\?\\w\\s)\\w+"
    },
    {
        "id": "SKIP",
        "expreg": "[ \\t]"
    },
    {
        "id": "NEWLINE",
        "expreg": "\\n"
    },
    {
        "id": "ERRO",
        "expreg": "."
    }
]
```
### Output

<img width="836" height="941" alt="image" src="https://github.com/user-attachments/assets/cdb7b5b9-1a80-45e8-9e87-d0c228a07f15" />


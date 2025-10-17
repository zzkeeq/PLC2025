# Título
Trabalho Prático 3

# Autor
Nome: Rodrigo Ferreira Leite<br>
ID: PG60423<br>
Foto:<br>
<img src="https://github.com/user-attachments/assets/b2078170-049c-47ca-96c3-6c5565a3aae8" width="150">

# Resumo

Neste trabalho resolvi todos os tipos de Regex propostos pelo professor. O processo de resolução do problema foi o seguinte:

- Verificar o que tinha de reconhecer, por exemplo no caso do cabeçalho, reconhecer os #'s;
- Escrever as Expressões Regulares;
- Verificar se está a capturar o que pretendo;
- Ir ajustando e testando ao longo de algumas regras, por exemplo no caso do link "http://www.(...).com.pr" não poderia capturar "http://www.(...).com";
- Depois de conseguir capturar tudo o que quero para cada caso, fiz print do texto de output usando a terminologia de HTML;
- Testar e Verificar se está tudo direitinho.

# Resultados

## Código

```
import re

def cabeçalho(texto):
    m = re.findall(r'##?#?(\w+)', texto)
    if m:
        for obj in m:
            print("<h1>",obj,"</h1>")
    else:
        return

def bold(texto):
    m = re.findall(r'\*\*(\w+?)\*\*', texto)
    if m:
        for obj in m:
            print("<b>",obj,"</b>")
    else:
        return
    
def italic(texto):
    m = re.findall(r'(?<!\*)\*(\w+?)\*(?!\*)', texto) ## Negative Lookbehind and Lookahead to avoid matching **
    if m:
        for obj in m:
            print("<i>",obj,"</i>")
    else:
        return
    
def list(texto):
    m = re.findall(r'\d+\. (\w+)', texto)
    if m:
        print("<ol>")
        for obj in m:
            print("<li>",obj,"</li>")
        print("</ol>")
    else:
        return
    
def link(texto):
    m1 = re.findall(r'(?:\w+| )*?(?=\[)', texto) # Antes do []
    m2 = re.findall(r'http://www\.\w+(?:\.(?:pt|com|net))+(?=\s|$)', texto) # Link
    m3 = re.findall(r'\[([\w ]+)\]', texto) # Dentro do []
    if m1 and m2 and m3:
        for bfr, lnk, pgn in zip(m1,m2,m3):
            print(bfr,"<a href = \"" + lnk + "\">" + pgn + "</a>")
    else:
        return
    
def image(texto):
    m1 = re.findall(r'http://www\.\w+(?:\.(?:pt|com|net))+(?=\s|$)', texto) # Link
    m2 = re.findall(r'\!\[([\w ]+)\]', texto) # Dentro do ![]
    if m1 and m2:
        for lnk, img in zip(m1,m2):
            print("Como se vê na imagem seguinte: <img src=\"" + lnk + "\" alt=\"" + img + "\"/>")
    else:
        return

entrada = """1. Primeira Linha
2. Segunda Linha
3. Terceira Linha

###texto
**negrito**
*itálico*

Como pode ser consultado em [página da UC]http://www.exemple.com.pt.net
![imagem de exemplo](http://www.exemple.com/imagem.png)
"""

cabeçalho(entrada)
print("\n")
bold(entrada)
print("\n")
italic(entrada)
print("\n")
list(entrada)
print("\n")
link(entrada)
print("\n")
image(entrada)
```
## Output

```
<h1> texto </h1>


<b> negrito </b>


<i> itálico </i>


<ol>
<li> Primeira </li>
<li> Segunda </li>
<li> Terceira </li>
</ol>


Como pode ser consultado em  <a href = "http://www.exemple.com.pt.net">página da UC</a>


Como se vê na imagem seguinte: <img src="http://www.exemple.com.pt.net" alt="imagem de exemplo"/>

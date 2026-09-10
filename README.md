# login-forms

Página de login interativa, com animações de entrada e layout responsivo, pensada para ser hospedada em uma VPS na OCI (Oracle Cloud Infrastructure).

## Tecnologias utilizadas

- **HTML5** puro, sem uso de frameworks.
- **CSS3** puro, com variáveis (Custom Properties) para cores, fontes e tamanhos.
- **JavaScript** puro (vanilla), sem frameworks.
- **GSAP 3.14** (GreenSock Animation Platform), carregado via CDN, responsável pelas animações de entrada.
- **Remixicon**, carregado via CDN, para os ícones (e-mail, cadeado, enviar).
- **Google Fonts (Syne)**, carregado via CDN, como fonte tipográfica.
- **SVG** próprio para a ilustração ao lado do formulário.

Não há backend, ferramenta de build (Webpack, Vite etc.) nem framework de front-end (React, Vue etc.). É um site estático simples.

## Estrutura de arquivos

```
index.html
assets/
  css/styles.css
  js/main.js
  img/login-illustration.svg
```

## Estrutura do HTML

O `<main class="login">` contém um bloco `.login__content`, dividido em duas partes:

1. `.login__form-wrapper` — título de boas-vindas e o formulário (campos de e-mail e senha, link "Esqueceu a senha?", botão "Entrar" e link de cadastro).
2. `.login__image` — a ilustração em SVG.

O formulário ainda não possui lógica de envio real (não há `action` nem backend integrado) — por enquanto é apenas a interface visual.

Um detalhe de usabilidade: os campos usam `placeholder=" "` (um espaço) combinado com a regra CSS `:not(:placeholder-shown)`. Isso faz o `<label>` se comportar como um "rótulo flutuante", que fica sobreposto ao campo e some apenas quando ele recebe foco ou já contém texto — tudo resolvido em CSS, sem necessidade de JavaScript.

## Estrutura do CSS

- **Variáveis** definidas em `:root` (cores em HSL, fontes e tamanhos), o que facilita ajustar o tema alterando um único valor.
- **Nomenclatura no estilo BEM** (`login__title`, `login__box`, `login__button` etc.), deixando claro a qual bloco cada elemento pertence.
- **Abordagem mobile-first**: o estilo base já é o layout de celular, com o formulário centralizado ocupando a tela e sem a ilustração. A partir de `min-width: 540px`, o card passa a ter largura fixa e centralizada; a partir de `min-width: 1150px`, o layout muda para duas colunas lado a lado (formulário e ilustração).
- A responsividade é resolvida inteiramente com CSS Grid e media queries, sem depender de JavaScript.

## Lógica das animações (GSAP)

O arquivo `main.js` monta uma timeline do GSAP que roda automaticamente assim que a página carrega, sem depender de nenhuma interação do usuário:

1. O card principal (`.login__content`) surge vindo de cima, achatado e invisível, e desliza até sua posição final.
2. Em seguida, ele se expande primeiro na vertical e depois na horizontal, criando um efeito de "caixa se abrindo" em duas etapas.
3. A ilustração recebe um zoom sutil e contínuo, que alterna para frente e para trás indefinidamente, dando um efeito de respiração ao fundo.
4. Com atrasos escalonados, o título, os campos do formulário (um após o outro) e a ilustração (com um efeito elástico) surgem por cima do card já montado, reforçando a sensação de camadas aparecendo em sequência.

Toda essa sequência acontece uma única vez, como introdução da página.

## Sobre a ilustração

A ilustração em `assets/img/login-illustration.svg` é uma arte vetorial própria, composta por formas geométricas simples (elipses recortadas, curvas) que reproduzem uma paisagem estilizada com céu em gradiente, sol, montanhas e terraços.

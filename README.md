# login-forms

Página de login interativa, com animações de entrada e layout responsivo, com um backend simples de autenticação para fins de estudo e teste de vulnerabilidades web.

> **Aviso**: este projeto foi construído de propósito com falhas de segurança (autenticação vulnerável a SQL injection, senhas em texto puro, sem HTTPS). Ele existe para servir de alvo de testes em um ambiente controlado — não é um exemplo de como autenticação deve ser implementada em produção.

## Tecnologias utilizadas

**Front-end**

- **HTML5** puro, sem uso de frameworks.
- **CSS3** puro, com variáveis (Custom Properties) para cores, fontes e tamanhos.
- **JavaScript** puro (vanilla), sem frameworks.
- **GSAP 3.14** (GreenSock Animation Platform), carregado via CDN, responsável pelas animações de entrada.
- **Remixicon**, carregado via CDN, para os ícones (usuário, cadeado, enviar).
- **Google Fonts (Syne)**, carregado via CDN, como fonte tipográfica.
- **SVG** próprio para a ilustração ao lado do formulário.

**Back-end**

- **Node.js** com **Express**, servindo tanto os arquivos estáticos quanto a rota de login.
- **MySQL/MariaDB**, através do driver `mysql2`, para armazenar os usuários.
- **dotenv**, para carregar as credenciais do banco a partir de variáveis de ambiente.

Não há ferramenta de build (Webpack, Vite etc.) nem framework de front-end (React, Vue etc.) — o HTML/CSS/JS do formulário continua sendo servido como arquivos estáticos.

## Estrutura de arquivos

```
index.html
welcome.html
assets/
  css/styles.css
  js/main.js
  img/login-illustration.svg
server/
  server.js
  db.js
  package.json
  .env.example
  db/
    schema.sql
    seed.sql.example
```

## Estrutura do HTML

O `<main class="login">` contém um bloco `.login__content`, dividido em duas partes:

1. `.login__form-wrapper` — título de boas-vindas, uma área de mensagem de erro (`.login__error`, oculta por padrão) e o formulário (campos de usuário e senha, link "Esqueceu a senha?", botão "Entrar" e link de cadastro).
2. `.login__image` — a ilustração em SVG.

O formulário envia os dados via `POST` para a rota `/login` do backend. Se a autenticação falhar, o backend redireciona de volta para a página inicial com `?error=1` na URL, e o `main.js` lê esse parâmetro para exibir a mensagem de erro.

Um detalhe de usabilidade: os campos usam `placeholder=" "` (um espaço) combinado com a regra CSS `:not(:placeholder-shown)`. Isso faz o `<label>` se comportar como um "rótulo flutuante", que fica sobreposto ao campo e some apenas quando ele recebe foco ou já contém texto — tudo resolvido em CSS, sem necessidade de JavaScript.

Após um login bem-sucedido, o usuário é redirecionado para `welcome.html`, uma página estática simples que reaproveita o mesmo tema visual.

## Estrutura do CSS

- **Variáveis** definidas em `:root` (cores em HSL, fontes e tamanhos), o que facilita ajustar o tema alterando um único valor.
- **Nomenclatura no estilo BEM** (`login__title`, `login__box`, `login__button`, `welcome__title` etc.), deixando claro a qual bloco cada elemento pertence.
- **Abordagem mobile-first**: o estilo base já é o layout de celular, com o formulário centralizado ocupando a tela e sem a ilustração. A partir de `min-width: 540px`, o card passa a ter largura fixa e centralizada; a partir de `min-width: 1150px`, o layout muda para duas colunas lado a lado (formulário e ilustração).
- A responsividade é resolvida inteiramente com CSS Grid e media queries, sem depender de JavaScript.

## Lógica das animações (GSAP)

O arquivo `main.js` monta uma timeline do GSAP que roda automaticamente assim que a página carrega, sem depender de nenhuma interação do usuário:

1. O card principal (`.login__content`) surge vindo de cima, achatado e invisível, e desliza até sua posição final.
2. Em seguida, ele se expande primeiro na vertical e depois na horizontal, criando um efeito de "caixa se abrindo" em duas etapas.
3. A ilustração recebe um zoom sutil e contínuo, que alterna para frente e para trás indefinidamente, dando um efeito de respiração ao fundo.
4. Com atrasos escalonados, o título, os campos do formulário (um após o outro) e a ilustração (com um efeito elástico) surgem por cima do card já montado, reforçando a sensação de camadas aparecendo em sequência.

Antes de iniciar essa timeline, o script também verifica se a URL contém `?error=1` (vindo de uma tentativa de login mal-sucedida) e, se for o caso, revela a mensagem de erro acima do formulário.

Toda essa sequência acontece uma única vez, como introdução da página.

## Sobre a ilustração

A ilustração em `assets/img/login-illustration.svg` é uma arte vetorial própria, composta por formas geométricas simples (elipses recortadas, curvas) que reproduzem uma paisagem estilizada com céu em gradiente, sol, montanhas e terraços.

## Estrutura do backend

- **`server/server.js`** — ponto de entrada da aplicação. Cria o servidor Express, serve os arquivos estáticos da raiz do projeto (`index.html`, `welcome.html`, `assets/`) e define a rota `POST /login`.
- **`server/db.js`** — abre a conexão com o banco de dados MySQL/MariaDB, lendo host, porta, usuário, senha e nome do banco a partir de variáveis de ambiente (via `dotenv`).
- **`server/db/schema.sql`** — cria o banco `login_forms_db` e a tabela `users` (`id`, `username`, `password`). Não contém nenhum dado, apenas a estrutura.
- **`server/db/seed.sql.example`** — modelo de script para popular a tabela com usuários de teste. O arquivo real usado (`seed.sql`, com credenciais de fato) não é versionado — fica de fora do Git propositalmente.
- **`server/.env.example`** — modelo das variáveis de ambiente necessárias (`PORT`, `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD`, `DB_NAME`). O `.env` real, assim como `node_modules/`, não é versionado.

## Lógica de autenticação

Quando o formulário é enviado, o servidor recebe `username` e `password` no corpo da requisição e monta a consulta diretamente por concatenação de texto:

```js
const query = `SELECT * FROM users WHERE username = '${username}' AND password = '${password}'`;
```

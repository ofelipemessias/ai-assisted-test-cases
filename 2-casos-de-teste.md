# Casos de teste gerados com IA e revisados contra o código

## O que é este documento

Estes casos de teste do **Advoga+** (meu app de geração de posts para advogados) foram gerados com ajuda de IA e depois revisados por mim, um a um, contra o código real do app, antes de entrar no projeto. Documento aqui o processo: o prompt que usei, os casos que ficaram, o que a IA errou e o que aprendi.

Uso a IA para acelerar a exploração de cenários; a decisão do que vira teste é minha. Os fluxos principais estão automatizados com **Playwright** (46 testes, rodando no GitHub Actions a cada alteração).

## O prompt usado

> Atue como um QA sênior. O sistema é um app web multi-tenant de geração de posts para escritórios de advocacia: cadastro de usuários, login com limite de tentativas, criação e edição de posts, convites para membros do escritório, uso em celular, rotas protegidas e isolamento de dados entre clientes. Gere casos de teste em português, no formato: ID, área, título, pré-condições, passos, resultado esperado, prioridade. Cubra caminho feliz, validações, segurança e mobile.

Depois do primeiro resultado, refinei pedindo variações de segurança (acesso sem login, acesso cruzado entre clientes) e de borda (campos vazios, caracteres especiais).

## Casos de teste

### Cadastro

| ID | Título | Prioridade | Automação |
|----|--------|-----------|-----------|
| CT-01 | Cadastro com dados válidos cria conta e entra no sistema | Alta | Playwright |
| CT-02 | E-mail já cadastrado mostra erro e não duplica conta | Alta | Playwright |
| CT-03 | Senha fraca é recusada com mensagem clara | Média | Playwright |
| CT-04 | Campos obrigatórios vazios bloqueiam o envio | Média | Playwright |

### Login e segurança de acesso

| ID | Título | Prioridade | Automação |
|----|--------|-----------|-----------|
| CT-05 | Login válido leva ao painel | Alta | Playwright |
| CT-06 | Senha errada mostra erro genérico (sem dizer se o e-mail existe) | Alta | Playwright |
| CT-07 | Após várias tentativas erradas, o login é bloqueado temporariamente | Alta | Playwright |
| CT-08 | Sessão expirada redireciona para o login | Média | Playwright |

### Posts (criação e edição)

| ID | Título | Prioridade | Automação |
|----|--------|-----------|-----------|
| CT-09 | Criar post com título e conteúdo válidos salva e lista o post | Alta | Playwright |
| CT-10 | Editar post altera o conteúdo e mantém o histórico correto | Alta | Playwright |
| CT-11 | Excluir post pede confirmação e remove da lista | Média | Playwright |
| CT-12 | Caracteres especiais e emojis no texto não quebram o post | Média | Playwright |
| CT-13 | Post respeita as regras de publicidade da OAB antes de ser gerado | Alta | Testes automatizados (camada de regras) |

### Convites

| ID | Título | Prioridade | Automação |
|----|--------|-----------|-----------|
| CT-14 | Convidar membro por e-mail envia o convite | Alta | Playwright |
| CT-15 | Convite aceito dá acesso ao espaço do escritório | Alta | Playwright |
| CT-16 | Convite para e-mail inválido mostra erro | Média | Playwright |
| CT-17 | Link de convite já usado ou inválido não funciona | Média | Playwright |

### Rotas protegidas

| ID | Título | Prioridade | Automação |
|----|--------|-----------|-----------|
| CT-18 | Acessar o painel sem login redireciona para a página de login | Alta | Playwright |
| CT-19 | Chamar a API sem token retorna 401 | Alta | Playwright |
| CT-20 | Usuário comum não acessa rotas de administração (acesso negado) | Média | Playwright |

### Isolamento entre clientes (multi-tenant)

| ID | Título | Prioridade | Automação |
|----|--------|-----------|-----------|
| CT-21 | Usuário do escritório A não vê posts do escritório B | Alta | Playwright |
| CT-22 | Acesso direto pela URL a um post de outro cliente retorna erro | Alta | Playwright |
| CT-23 | Listagens e buscas só retornam dados do próprio cliente | Alta | Playwright |

### Mobile

| ID | Título | Prioridade | Automação |
|----|--------|-----------|-----------|
| CT-24 | Fluxo de login e criação de post funciona em tela de celular | Alta | Playwright |

## O que a IA errou e eu corrigi

- **Sugeriu limites arbitrários** (tamanho de senha, limite de upload) que não eram os do app. Ajustei cada um conferindo as regras reais no código.
- **Repetiu casos com nomes diferentes.** Consolidei duplicados para não inflar a contagem.
- **Ignorou o isolamento entre clientes na primeira rodada.** Pedi uma rodada específica de segurança; dela saíram CT-18 a CT-23, que viraram os testes mais importantes da suíte.

A revisão contra o código também mostrou pontos da automação para ampliar, que viraram tarefas no backlog do projeto.

## O que aprendi

A IA é boa para varrer cenários óbvios rápido e para lembrar categorias (segurança, borda, mobile). Ela não conhece o produto: sem revisão, o resultado mistura casos reais com suposições erradas sobre regras e limites. Meu trabalho como QA é exatamente esse filtro — conferir cada caso contra o código, corrigir o que a IA chutou e transformar os casos certos em testes automatizados.

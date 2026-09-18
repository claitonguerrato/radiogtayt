# Rádio (estilo GTA V) — deploy na Vercel

Projeto estático (um único `index.html` com HTML + CSS + JS, sem dependências
de build). A Vercel detecta isso automaticamente como um site estático —
não é necessário framework, `package.json` nem etapa de build.

## Estrutura

```
.
├── index.html     -> aplicação inteira
├── vercel.json     -> configuração mínima da Vercel
└── README.md
```

## Deploy — via painel da Vercel (mais simples)

1. Suba esta pasta para um repositório no GitHub/GitLab/Bitbucket.
2. Em https://vercel.com, clique em "Add New… → Project".
3. Importe o repositório.
4. Em "Framework Preset", deixe como "Other".
5. Clique em "Deploy".
6. Ao terminar, você recebe uma URL do tipo `https://seu-projeto.vercel.app`.

## Deploy — via Vercel CLI

```bash
npm i -g vercel
cd gta-radio-vercel
vercel
vercel --prod
```

## IMPORTANTE — depois do primeiro deploy

### 1. Configure a chave da YouTube Data API
As faixas "de fábrica" de cada estação tocam pelo YouTube, sem precisar de
login em nenhuma conta — só uma chave gratuita:

1. https://console.cloud.google.com/ → crie/abra um projeto.
2. "APIs e Serviços" → "Biblioteca" → ative "YouTube Data API v3".
3. "APIs e Serviços" → "Credenciais" → "Criar credenciais" → "Chave de API".
4. (Recomendado) Restrinja a chave por "referenciador HTTP" ao domínio de
   produção (`https://seu-projeto.vercel.app/*`), para evitar uso indevido
   caso ela vaze.
5. Na aplicação em produção: `/` → "admin" → login (`rolamentos` / `gta5radio`)
   → seção "Integração com YouTube" → cole a chave → "Salvar chave".

Não há Redirect URI nem OAuth para configurar — diferente de integrações
como a do Spotify, aqui não é necessário conectar nenhuma conta pessoal.

### 2. Aviso de segurança sobre o login do painel admin
O login/senha do painel (`rolamentos` / `gta5radio`) é verificado inteiramente
no navegador (JavaScript do próprio `index.html`). Isso significa que:

- Qualquer pessoa pode ver o usuário/senha olhando o código-fonte da página.
- Não é uma autenticação segura para proteger conteúdo sensível numa URL pública.

Para um projeto pessoal/hobby isso costuma ser suficiente. Para proteção real:
- Ative "Password Protection" da própria Vercel (planos Pro/Enterprise).
- Ou reescreva a autenticação com uma função serverless (Vercel Functions).

### 3. Onde ficam as músicas adicionadas pelo painel admin
Os MP3s e capas anexados pelo painel ficam salvos no IndexedDB do navegador de
quem os adicionou — cada visitante tem sua própria lista local, não uma lista
global compartilhada. O mesmo vale para a chave do YouTube (salva por
navegador). Um backend real (banco de dados + storage de arquivos) seria
necessário para tornar isso compartilhado entre todos os visitantes.

# DimDim V2 — Documentação Completa de Hospedagem

Versão refinada: identidade visual unificada em todas as telas, bot da IA com fidelidade total à logo, responsividade real no mobile, banco de dados por usuário e agente de IA (Google Gemini) integrado com chave já embutida.

---

## 1. O que mudou na V2

### Correções de identidade visual
- **Bot da IA sem corte**: removido o recorte circular que cortava partes da logo no ícone flutuante e no cabeçalho do chat.
- **Sombra falsa removida**: a logo original vinha com uma sombra desenhada dentro do próprio arquivo de imagem (não era CSS) — identificada e apagada nos três estados (normal, sucesso, erro).
- **Ponto de destaque corrigido**: um dos círculos da logo estava "vazado" (transparente) por engano na extração — hoje é um preenchimento sólido, fiel ao design original.
- **Animação mais perceptível**: flutuação e balanço do bot com amplitude maior.
- **Cabeçalho do chat unificado**: usa o mesmo degradê verde do resto do app.
- **Marca d'água gigante no chat**: mesma logo em baixa opacidade atrás das mensagens, como no Início.
- **Botão de fechar com contraste corrigido**: fundo claro sólido + ícone verde escuro (antes o ícone branco sumia sobre fundo branco translúcido).

### Correções de responsividade (mobile)
- Fonte mínima de 16px em todos os campos de formulário (evita zoom automático do iOS).
- Unidades `dvh` no lugar de `vh` em telas cheias.
- Breakpoint para telas estreitas (`≤380px`).

### Correções de lógica do chat (bugs reais, não visuais)
- Mensagem do usuário não duplica mais na tela quando a chave ainda não estava configurada.
- Histórico enviado pra IA não duplica mais mensagens em caso de erro de autenticação.
- **Chave de API do Gemini já vem embutida no código** — o usuário não precisa colar nenhuma chave pra usar o assistente. Se a chave embutida falhar por algum motivo, o app avisa por mensagem, sem interromper pra pedir uma nova.

### Funcionalidades já existentes (mantidas)
- Lista de compras com memória, leitura de nota fiscal por OCR, histórico por dia, painel com regra 50-30-20.
- Cores do app mudam conforme a saúde financeira (verde/amarelo/vermelho conforme o saldo livre).
- Cada usuário conecta a própria planilha do Google Sheets (isolamento de dados sem precisar de login).
- Agente de IA (Google Gemini) com chat por texto e voz, que lê os dados financeiros reais, dá dicas, guia a configuração inicial e pesquisa rentabilidade de investimentos na internet.

---

## 2. Arquitetura

```
┌─────────────────┐      HTTPS       ┌──────────────────────┐
│  GitHub Pages    │ ───────────────▶ │  Google Apps Script   │
│  (index.html)    │ ◀─────────────── │  (Code.gs)            │
│  estático, PWA   │                  │  API sobre sua        │
└────────┬─────────┘                  │  planilha Google      │
         │                            └──────────────────────┘
         │ HTTPS direto do navegador
         ▼
┌──────────────────────┐
│  Google Gemini API    │
│  (chat + busca web)   │
└──────────────────────┘
```

- **Frontend**: um único arquivo `index.html` (HTML+CSS+JS puro, sem build), hospedado gratuitamente no GitHub Pages.
- **Banco de dados**: sua própria planilha do Google Sheets, exposta como API HTTPS via Google Apps Script (`Code.gs`). Cada pessoa usa a própria planilha — nada é compartilhado entre usuários.
- **IA**: chamadas diretas do navegador da pessoa pra API do Google Gemini, usando a chave de API dela (guardada só no aparelho dela, nunca passa por nenhum servidor intermediário).
- **Sem backend próprio**: não há servidor rodando 24h. Isso significa que a IA só age quando o app está aberto — não há monitoramento automático em segundo plano (ver seção "Limitações conhecidas").

---

## 3. Passo a passo de hospedagem

### 3.1 GitHub Pages (o app em si)

1. Crie um repositório público no GitHub chamado `seu-usuario.github.io`.
2. Envie o conteúdo da pasta `webapp/` pra raiz do repositório (`index.html`, `manifest.json`, `sw.js`, `icons/`, `assets/`).
3. Aguarde ~30 segundos e acesse `https://seu-usuario.github.io`.

```bash
cd seu-repositorio-local
git init
git add .
git commit -m "DimDim V2"
git branch -M main
git remote add origin https://github.com/seu-usuario/seu-usuario.github.io.git
git push -u origin main
```

### 3.2 Planilha do Google Sheets (banco de dados)

1. Importe `dimdim-planilha.xlsx` no Google Sheets (Arquivo → Importar → Substituir planilha), ou crie do zero com as abas: `Dashboard`, `Planilha1`, `Categorias_Config`, `Compras`, `Custos_Fixos_App`, `Proventos_App`, `Investimentos_App`.
2. Na planilha: **Extensões → Apps Script**.
3. Apague o conteúdo padrão e cole o `Code.gs` inteiro.
4. Rode a função `configurarPlanilhaInicial` uma vez (menu suspenso no topo → Executar) pra criar as abas que faltarem.
5. **Deploy → New deployment**:
   - Tipo: **Web app**
   - Executar como: **Eu**
   - Quem pode acessar: **Qualquer pessoa**
6. Copie a URL (termina em `/exec`).

> Toda vez que você editar o `Code.gs`, precisa gerar uma **nova versão** em "Gerenciar implantações" pra valer.

### 3.3 Chave de API do Google Gemini (o agente de IA)

**Já vem embutida no código** — não precisa fazer nada aqui. Se um dia precisar trocar (por exemplo, se a chave expirar ou você quiser usar a sua própria):

1. Acesse **aistudio.google.com/apikey** e gere uma nova.
2. No app: ⚙️ → Status da Conexão → "Salvar chave de IA".

### 3.4 Conectando a planilha

Na primeira vez que você abrir o app, ele vai pedir só a **URL do Apps Script** (passo 3.2) — a chave de IA já está pronta, não é solicitada.

Essa URL fica salva só no navegador daquele aparelho (localStorage). Pode trocar depois em ⚙️ → Status da Conexão.

---

## 4. Estrutura de arquivos

```
webapp/
  index.html          → o app inteiro (HTML+CSS+JS)
  manifest.json        → configuração do PWA (nome, ícones, cores)
  sw.js                → service worker (funcionamento offline)
  icons/
    icon-192.png       → ícone do PWA (192x192)
    icon-512.png       → ícone do PWA (512x512)
    mark.png           → logo (estado normal)
    mark-success.png   → logo recolorida (estado de sucesso)
    mark-error.png     → logo recolorida (estado de erro)
  assets/
    dimdim-logo.svg    → logo vetorial original

apps-script/
  Code.gs              → backend (API sobre a planilha)

dimdim-planilha.xlsx   → planilha modelo pra importar
README.md              → visão geral do projeto
SETUP_GITHUB_PAGES.md  → guia de setup anterior (mantido por compatibilidade)
```

---

## 5. Testando antes de divulgar

Checklist rápido depois de hospedar:

- [ ] Abre no celular e a splash toca a animação completa
- [ ] O saldo aparece corretamente na tela Início (não fica em R$ 0,00 pra sempre)
- [ ] O app muda de cor se você simular um saldo negativo (edite um valor de custo fixo pra testar)
- [ ] Toca no botão flutuante da IA — o bot aparece inteiro, sem corte
- [ ] Manda uma mensagem pro assistente e recebe resposta
- [ ] Testa o microfone (ícone 🎙️) — funciona no Chrome; em navegadores sem suporte, o botão de microfone some sozinho (é esperado)
- [ ] Instala na tela inicial do celular e reabre — deve abrir em tela cheia, sem barra de navegador
- [ ] Testa em uma tela pequena (celular mais antigo/compacto) — nenhum campo deve cortar ou dar zoom sozinho ao tocar

---

## 6. Limitações conhecidas (por design, não são bugs)

- **Ícone da tela inicial é estático.** Nenhuma plataforma (iOS, Android ou PWA) permite ícone animado na tela de início — isso é uma regra do sistema operacional. A animação acontece ao abrir o app (splash), não no ícone parado.
- **Sem monitoramento automático em segundo plano.** O agente de IA só age quando o app está aberto. Ele não fica checando taxas de investimento ou te avisando sozinho com o app fechado — isso exigiria um servidor rodando 24h, que este projeto não tem.
- **A chave de Gemini embutida é compartilhada por quem usar este código.** Pra uso pessoal (só você testando) isso é o esperado. Se decidir distribuir pra outras pessoas usarem, either troque pra elas colarem a própria chave (o campo em ⚙️ → Status da Conexão já existe pra isso), ou monitore o consumo da sua chave em aistudio.google.com pra não estourar o limite gratuito. Cada pessoa continua precisando da própria planilha, isso não muda.
- **Modelos de IA mudam com o tempo.** O Google eventualmente aposenta modelos antigos (já aconteceu uma vez nesta conversa, do `gemini-2.5-flash` pro `gemini-3.5-flash`). Se o chat parar de responder com um erro citando o nome do modelo, é sinal de que precisa trocar a versão no código.

---

## 7. Solução de problemas comuns

| Sintoma | Causa provável | Solução |
|---|---|---|
| Logo/visual não atualiza depois de subir uma versão nova | Service Worker antigo em cache | Desinstale o app da tela inicial e reinstale, ou F12 → Application → Service Workers → Unregister |
| Chat da IA diz "chave não aceita" | Chave colada errada ou expirada | Gere uma nova em aistudio.google.com/apikey |
| Chat da IA cita erro com nome de modelo (ex: "model X no longer available") | Google aposentou o modelo | Avise pra trocar o nome do modelo no código (`gemini-X-flash`) |
| Saldo sempre R$ 0,00 | Planilha não conectada ou Apps Script não implantado | Confira ⚙️ → Status da Conexão |
| Campo de texto dá zoom sozinho no iPhone | Fonte do campo menor que 16px | Já corrigido na V2 — se acontecer de novo, verificar se algum campo novo ficou de fora |

---

*DimDim V2 — seu dinheiro, sem enrolação.*

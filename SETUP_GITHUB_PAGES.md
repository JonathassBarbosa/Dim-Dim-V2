# DimDim — Deploy no GitHub Pages + Google Apps Script

Siga os passos abaixo para colocar o app online e conectar à sua planilha do Google Sheets.

## 1️⃣ Crie um repositório no GitHub

- Vá para [github.com/new](https://github.com/new)
- **Repository name**: `seu-usuario.github.io` (substitua `seu-usuario` pelo seu login)
- Deixe **Public**
- Clique em **Create repository**

## 2️⃣ Suba os arquivos do app

Na pasta `webapp/` do seu projeto (ou clonado), você tem:
- `index.html`
- `manifest.json`
- `sw.js`
- `icons/` (pasta com icon-192.png e icon-512.png)

**Opção A: Via Git (recomendado)**
```bash
cd seu-repositorio-local
git init
git add .
git commit -m "DimDim v1"
git branch -M main
git remote add origin https://github.com/seu-usuario/seu-usuario.github.io.git
git push -u origin main
```

**Opção B: Via GitHub Web**
- Faça upload dos arquivos direto no repo (botão "Add file")

## 3️⃣ Confirme a hospedagem

- Aguarde 30 segundos
- Acesse: `https://seu-usuario.github.io`
- Você verá a splash screen com a logo DimDim animada

## 4️⃣ Configure o Apps Script na sua planilha

### A. Crie a planilha

1. Importe `dimdim-planilha.xlsx` no Google Sheets:
   - [sheets.google.com/create](https://sheets.google.com/create) → Arquivo → Importar → Substituir planilha
   - Ou crie uma nova planilha manualmente com as abas: `Dashboard`, `Planilha1`, `Categorias_Config`, `Compras`, `Custos_Fixos_App`, `Proventos_App`

### B. Adicione o Apps Script

1. Na sua planilha, vá em: **Extensões → Apps Script**
2. Delete o código padrão, cole o conteúdo de `apps-script/Code.gs`
3. Clique em **Deploy → New deployment**
   - Tipo: **Web app**
   - Executar como: **sua conta Google**
   - Quem tem acesso: **Anyone**
4. Copie a URL da implantação (exemplo: `https://script.google.com/macros/s/AKfycbz...`)

## 5️⃣ Conecte o app à planilha

1. Abra `https://seu-usuario.github.io` no celular ou navegador
2. Toque na engrenagem ⚙️ (canto superior direito)
3. Cole a URL do Apps Script em **Conexão com a planilha**
4. Clique em **Testar** (deve dizer "Conectado")
5. Clique em **Salvar**
6. Volte à tela inicial e veja o saldo aparecer

## 6️⃣ Use o app

### Na tela **Início**
- Vê o saldo livre do mês (toque no 👁 pra esconder)
- Dica automática sobre qual grupo está perto de estourar o orçamento

### Na tela **Lista**
- Cole itens no formato: `Nome, Categoria, Preço` (uma por linha)
- O app lembra de todos, marca como comprado quando finaliza

### Na tela **Nota**
- Fotografe a nota fiscal (OCR automático)
- Confirma os itens, escolhe a forma de pagamento
- Registra na planilha

### Na tela **Histórico**
- Compras agrupadas por dia
- "Hoje" fica aberta — marque itens da Lista, finalize

### Na tela **Painel**
- Saldo livre, receita, gastos por período (dia/mês/ano)
- Regra 50-30-20 visual (barras de cada grupo vs meta)

### Na tela **Registro** (⚙️)
- Edite categorias, custos fixos e proventos
- Tudo sai atualizado na planilha quando toca **Salvar tudo**

## ✅ Pronto!

O app agora está online, funcionando offline (PWA), e sincronizado com sua planilha do Google Sheets.

**Dicas:**
- Instale na tela inicial do celular (menu → "Instalar app")
- Os dados salvos localmente sincronizam com a planilha quando você clica em "Finalizar" ou "Confirmar"
- A marca d'água gigante no fundo é só visual, não atrapalha a navegação

---
*Feito com ❤️ — DimDim, seu dinheiro, sem enrolação.*

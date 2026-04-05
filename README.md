# Don QuixAr 🌬️
**Atendente virtual 24h da Don Ar Climatizações**

Chat com agente de IA para atendimento, orçamentos, agendamentos e captação de parceiros instaladores.

---

## Visão Geral

O projeto é dividido em dois componentes independentes:

| Componente | Tecnologia | Hospedagem |
|---|---|---|
| **Frontend** (`index.html`) | HTML + CSS + JS puro | Hostinger / qualquer host estático |
| **Backend** (`server.js`) | Node.js + Express | Hostinger Business (Node.js app) |

O frontend **nunca** acessa a DeepSeek diretamente. Toda requisição passa pelo backend, que guarda a API Key com segurança.

```
Usuário → index.html → server.js (Hostinger) → DeepSeek API
```

---

## Estrutura do Projeto

```
donar/
├── frontend/
│   └── index.html          # Site completo — upload direto na Hostinger
│
└── backend/
    ├── server.js           # Servidor proxy Express
    ├── package.json
    ├── .env                # ⚠️ NÃO versionar — criar localmente
    └── .env.example        # Modelo das variáveis de ambiente
```

---

## Backend — Configuração

### Variáveis de ambiente

Crie o arquivo `.env` na pasta `backend/` baseado no `.env.example`:

```env
DEEPSEEK_API_KEY=sua_chave_deepseek_aqui
CLIENT_SECRET=token_secreto_que_o_frontend_usa
PORT=3000
ALLOWED_ORIGIN=https://donar.chat
```

> **CLIENT_SECRET**: invente qualquer string longa e aleatória. Ex: `donar-2025-xK9#mP2`  
> Esse mesmo valor deve estar no `index.html` do frontend.

### Deploy na Hostinger (Business Plan)

1. Compacte a pasta `backend/` em `.zip`
2. No hPanel → **Sites → arthurmorato.com → Hospedagem**
3. Role até **"Detalhes do Node.js"** → faça upload do `.zip`
4. Configure:
   - **Framework**: Express
   - **Node version**: 20.x
   - **Entry file**: `server.js`
5. Adicione as variáveis de ambiente pelo painel antes de implantar
6. Clique em **Implantar**

### Verificar se está no ar

```
GET https://donar.chat/health
```
Resposta esperada:
```json
{ "status": "ok", "timestamp": "..." }
```

---

## Frontend — Configuração

Abra o `index.html` e edite as duas linhas no topo do `<script>`:

```js
const SERVER_URL = "https://donar.chat";       // URL do seu backend
const CLIENT_SECRET = "token_secreto_aqui";    // Mesmo valor do .env
```

### Deploy na Hostinger

1. Acesse o **Gerenciador de Arquivos** no hPanel
2. Navegue até `public_html/`
3. Faça upload do `index.html`
4. Acesse `https://donar.chat` — pronto

---

## Como funciona o Agente Don QuixAr

### Tela inicial
- Saudação centralizada com 6 cards de sugestão clicáveis
- Cada card preenche e envia uma pergunta automaticamente

### Durante o chat
- Histórico completo enviado a cada mensagem (memória de contexto)
- Don identifica se o visitante é **cliente final** ou **parceiro instalador**
- Respostas incluem botões de ação contextuais

### Botões de ação disponíveis
| Botão | Ação |
|---|---|
| 📲 Falar com atendente | Abre WhatsApp |
| 📅 Agendar visita | Redireciona para agendamento |
| 🔧 Falar com técnico | Abre WhatsApp (fila técnica) |
| ✉️ Enviar e-mail | Abre cliente de e-mail |
| 🤝 Quero ser parceiro | Exibe formulário de cadastro inline |
| 📞 Ligar agora | Inicia chamada telefônica |

---

## Segurança

- ✅ API Key da DeepSeek armazenada apenas no servidor
- ✅ Frontend autenticado via `CLIENT_SECRET` no header `x-client-secret`
- ✅ CORS restrito ao domínio `donar.chat`
- ✅ Rate limit: 20 requisições por IP a cada 10 minutos
- ✅ API Key nunca aparece em logs

---

## Contatos e Links

| | |
|---|---|
| Site | https://donar.chat |
| WhatsApp | (11) 99999-9999 |
| E-mail | contato@donar.chat |
| Empresa | Don Ar Climatizações |

---

## Roadmap

- [ ] Integração com banco de dados (preços e agenda em tempo real)
- [ ] Painel administrativo para gerenciar conversas
- [ ] Sistema de agendamento integrado
- [ ] Onboarding de parceiros com área logada
- [ ] Expansão para múltiplas cidades com roteamento por CEP

---

*Don QuixAr — Atendimento inteligente, climatização com padrão garantido.*

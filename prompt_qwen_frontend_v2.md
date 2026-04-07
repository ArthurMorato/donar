# PROMPT — Frontend Site Chat Don QuixAr (v2)

Crie um único arquivo `index.html` com CSS e JS embutidos para o atendente AI **Don QuixAr** da **Don Ar Climatizações**.

---

## REFERÊNCIA VISUAL
Base: imagem de referência de chat centralizado com cards de sugestão, input fixo no rodapé, fundo branco, tipografia limpa e espaçosa, sem sidebar.
Referências adicionais: https://chat.z.ai e https://deepai.org/chat

---

## DOIS ESTADOS DA TELA

### ESTADO 1 — Tela de Boas-vindas (antes do chat iniciar)
Exibido ao carregar a página. Desaparece com animação suave (fade + slide up) ao enviar a primeira mensagem.

Layout centralizado vertical, tudo alinhado ao centro da viewport:

```
[ícone SVG de floco de neve — 48px, cor #3B82F6]

"Olá, bem-vindo à Don Ar."
→ fonte: 2.2rem, font-weight 700, cor #111827

"Sou o Don QuixAr, como posso ajudar?"
→ fonte: 1.1rem, font-weight 400, cor #6B7280

[6 cards de sugestão em grid 2x3]

[barra de input fixa no rodapé]
```

#### 6 CARDS DE SUGESTÃO
Grid 2 colunas em desktop, 1 coluna em mobile. Cada card tem:
- Título em negrito (a pergunta)
- Subtítulo menor em cinza (exemplo de como perguntar / o que esperar)
- Clique: preenche o input e envia automaticamente

| # | Título do card | Subtítulo |
|---|---|---|
| 1 | 💰 Quero um orçamento | "Ex: Preciso instalar um split 12.000 BTU em São Paulo, qual o valor?" |
| 2 | 🔧 Preciso de manutenção | "Ex: Meu ar está gelando pouco, quero agendar uma visita técnica" |
| 3 | 📅 Agendar uma visita | "Ex: Tenho disponibilidade na quinta à tarde, conseguem atender?" |
| 4 | 📍 Cidades atendidas | "Ex: Vocês atendem em Campinas ou somente São Paulo?" |
| 5 | 🤝 Quero ser parceiro | "Ex: Sou técnico instalador e quero me cadastrar como parceiro Don Ar" |
| 6 | ❓ Dúvidas sobre o serviço | "Ex: Qual a diferença entre higienização e manutenção preventiva?" |

---

### ESTADO 2 — Chat ativo (após primeira mensagem)
Transição suave: tela de boas-vindas faz fade out, área de chat faz fade in ocupando 100% da viewport.

Layout:
```
[header fixo no topo — logo Don Ar + "Don QuixAr · Atendente Virtual" + botão "Nova conversa"]
[área de mensagens com scroll — ocupa todo o espaço entre header e input]
[barra de input fixa no rodapé]
```

#### BALÕES DE MENSAGEM
- **Usuário**: alinhado à direita, fundo #3B82F6, texto branco, border-radius 18px 18px 4px 18px
- **Don QuixAr**: alinhado à esquerda, fundo #F3F4F6, texto #111827, border-radius 18px 18px 18px 4px
- Avatar do Don: círculo pequeno azul com ícone de floco de neve, aparece à esquerda de cada resposta
- Loading: balão cinza com três pontos animados (keyframe bounce) enquanto aguarda resposta

---

## BARRA DE INPUT (fixa, presente nos dois estados)
- Fundo branco, borda #E5E7EB, border-radius 24px, sombra suave
- Ícone "+" à esquerda (futuro anexo — não funcional, apenas visual)
- Placeholder: "Escreva sua mensagem..."
- Botão enviar: círculo #111827 com ícone de seta para cima (Lucide `arrow-up`)
- Envio por Enter (sem Shift) e por clique no botão
- Desabilitado enquanto aguarda resposta da API

---

## CONEXÃO COM SERVIDOR
```js
// ======= CONFIGURAÇÃO — alterar antes do deploy =======
const SERVER_URL = "https://donar.chat";
const CLIENT_SECRET = "token_secreto_aqui";
// =====================================================

async function sendMessage(userMessage) {
  const response = await fetch(`${SERVER_URL}/api/chat`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "x-client-secret": CLIENT_SECRET
    },
    body: JSON.stringify({ messages: conversationHistory })
  });
  const data = await response.json();
  return data.content; // string com resposta do Don
}
```

---

## SYSTEM PROMPT DO DON QUIXAR
```
Você é Don QuixAr, atendente virtual da Don Ar Climatizações — empresa especializada em instalação e manutenção de ar-condicionado que atua como garantidora de padrão de qualidade em múltiplas cidades, operando via rede de parceiros instaladores certificados. 

Identifique se o visitante é cliente final ou instalador parceiro e adapte a linguagem. 

Para clientes: informe serviços, oriente sobre tipos de aparelho, colete informações necessárias para orçamento (BTUs, cidade, tipo de instalação) e direcione para agendamento.

Para parceiros: explique o modelo de negócio (Don Ar padroniza e garante, parceiro executa e recebe repasse), colete dados de cadastro.

Dados precisos de preços e agenda são consultados internamente — diga "deixa eu verificar" quando perguntado e simule breve pausa.

Seja cordial, objetivo e humano. Máximo 3 parágrafos por resposta. Emojis com moderação.

IMPORTANTE: Sempre que o usuário demonstrar intenção de fechar negócio, agendar, precisar de suporte técnico urgente ou querer falar com humano, inclua ao final da resposta os marcadores de ação no formato exato: [BTN:texto|ação]
```

---

## BOTÕES DE AÇÃO INLINE [BTN]

O JS deve parsear toda resposta do Don e converter marcadores `[BTN:texto|ação]` em botões renderizados dentro do balão, abaixo do texto:

```js
// Parser de botões — executar em toda resposta recebida
function parseButtons(text) {
  // Encontra todos os [BTN:texto|ação] e substitui por <button>
}
```

Estilo dos botões: borda #3B82F6, texto #3B82F6, fundo branco, border-radius 8px, hover inverte cores. Múltiplos botões ficam em linha flexível com quebra.

| ação | comportamento |
|---|---|
| `whatsapp` | `window.open("https://wa.me/5511999999999?text=Olá, vim pelo site Don Ar!")` |
| `email` | `window.open("mailto:contato@donar.chat")` |
| `agendar` | Exibe no chat mensagem do sistema: "📅 Abrindo agenda de agendamentos..." (placeholder) |
| `tecnico` | `window.open("https://wa.me/5511999999999?text=Preciso falar com um técnico urgente")` |
| `parceiro` | Renderiza formulário inline no chat (ver abaixo) |
| `ligar` | `window.open("tel:+5511999999999")` |

Exemplos de botões que o Don deve sugerir contextualmente:
- `[BTN:📲 Falar com atendente|whatsapp]`
- `[BTN:📅 Agendar visita|agendar]`
- `[BTN:🔧 Falar com técnico|tecnico]`
- `[BTN:✉️ Enviar e-mail|email]`
- `[BTN:🤝 Quero ser parceiro|parceiro]`
- `[BTN:📞 Ligar agora|ligar]`

---

## FORMULÁRIO DE PARCEIRO (inline no chat)

Renderizado como card dentro da área de mensagens quando ação `parceiro` é acionada:

```
Card branco, borda #E5E7EB, border-radius 12px, padding 20px, sombra suave
Título: "Cadastro de Parceiro Instalador 🤝"
Campos:
  - Nome completo *
  - Cidade de atuação *
  - Telefone / WhatsApp *
  - CREA ou certificação (opcional)
  - Mensagem (opcional)
Botão "Enviar candidatura" → fundo #3B82F6, texto branco
Ao enviar: valida campos obrigatórios, exibe mensagem de sucesso inline
```

---

## DESIGN SYSTEM

```css
--white: #FFFFFF
--gray-50: #F9FAFB      /* fundo geral */
--gray-100: #F3F4F6     /* balão Don */
--gray-300: #D1D5DB     /* bordas */
--gray-500: #6B7280     /* texto secundário */
--gray-900: #111827     /* texto principal */
--blue-500: #3B82F6     /* acento principal */
--blue-600: #2563EB     /* hover */
--red-100: #FEE2E2      /* balão de erro */
--red-700: #B91C1C      /* texto de erro */

font-family: 'Nunito', sans-serif (Google Fonts CDN)
font-weights usados: 400, 600, 700
border-radius padrão: 12px (cards), 24px (input), 18px (balões)
sombra padrão: 0 1px 3px rgba(0,0,0,0.08), 0 4px 16px rgba(0,0,0,0.04)
```

---

## ANIMAÇÕES

- Tela de boas-vindas → chat: `opacity 0→1` + `translateY 20px→0`, duration 300ms ease
- Cards de sugestão: staggered fade-in ao carregar (delay 100ms entre cada)
- Balões de mensagem: slide-in suave ao aparecer
- Loading dots: keyframe bounce com delay entre os 3 pontos
- Botões de ação: hover com transition 150ms

---

## MOBILE
- Input sempre visível acima do teclado virtual (`position: fixed; bottom: 0`)
- Cards em coluna única
- Header compacto com apenas logo e botão nova conversa
- Touch targets mínimos de 44px

---

## TÉCNICO
- Zero frameworks JS, zero dependências externas além de Google Fonts e Lucide CDN
- Lucide: `<script src="https://unpkg.com/lucide@latest/dist/umd/lucide.min.js"></script>`
- `conversationHistory[]` mantém histórico completo enviado a cada request
- Primeira mensagem sempre inclui o system prompt como `{ role: "system", content: "..." }`
- Comentários obrigatórios nos blocos: configuração, fetch, parser BTN, system prompt, animações
- Erro de rede: balão vermelho "❌ Não consegui me conectar. Verifique sua conexão e tente novamente."
- Arquivo único, pronto para upload direto na Hostinger ou qualquer host estático

---

Entregue apenas o código. Sem explicações fora do código.

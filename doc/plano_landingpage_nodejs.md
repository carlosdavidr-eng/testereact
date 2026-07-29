# Plano de Arquitetura — Landing Page de Alta Conversão em React

> **Idioma obrigatório:** 100% do código (variáveis, funções, tabelas, colunas, chaves JSON, rotas **e comentários**) em **português brasileiro**. Comentários devem descrever a lógica em PT-BR em toda função e bloco relevante.

## 1. Estrutura de Pastas

```
testenodejs/
├── api/                          # Backend Node.js
│   ├── .env                      # Variáveis de ambiente (porta, banco)
│   ├── package.json
│   ├── src/
│   │   ├── app.js                # Config Express (CORS, JSON, rotas, arquivos estáticos React)
│   │   ├── server.js             # Inicialização do servidor
│   │   ├── config/
│   │   │   └── conexaoBanco.js   # Conexão SQLite (better-sqlite3)
│   │   ├── controladores/
│   │   │   └── leadControlador.js
│   │   ├── rotas/
│   │   │   └── leadRotas.js
│   │   └── utilitarios/
│   │       └── validadores.js    # Sanitização e validação
│   │
│   ├── db/                       # Banco de dados SQLite (criado em runtime)
│   │   └── landing.db            # Arquivo do banco SQLite (gitignore)
│   │
│   └── iniciarBanco.js           # Criação da tabela leads na inicialização
│
├── frontend/                     # Interface do Usuário (React 18 + Vite + Tailwind CSS)
│   ├── package.json              # Dependências React e scripts (dev, build)
│   ├── vite.config.js            # Configuração Vite e proxy /api -> http://localhost:3000
│   ├── tailwind.config.js        # Configuração do Tailwind CSS
│   ├── postcss.config.js         # Plugins PostCSS
│   ├── index.html                # HTML Base com div #root
│   └── src/
│       ├── main.jsx              # Ponto de entrada do React
│       ├── App.jsx               # Componente principal
│       ├── index.css             # Estilos globais + Tailwind CSS
│       └── components/
│           ├── Header.jsx        # Navbar fixa
│           ├── Hero.jsx          # Seção Hero com CTA
│           ├── Beneficios.jsx    # Cards de diferenciais
│           ├── FormularioLead.jsx# Form de captura com máscara e validação
│           ├── Toast.jsx         # Notificações visuais
│           └── Footer.jsx        # Rodapé
│
├── doc/
│   └── plano_landingpage_nodejs.md   # ← Este documento
│
├── .gitignore                    # ignora node_modules, .env, api/db/, frontend/dist/
│
└── README.md
```

---

## 2. Schema SQLite — Tabela `leads`

O banco SQLite (`api/db/landing.db`) é criado automaticamente pelo `better-sqlite3` na primeira execução. A tabela é criada via `iniciarBanco.js`:

```sql
CREATE TABLE IF NOT EXISTS leads (
    id                  INTEGER PRIMARY KEY AUTOINCREMENT,
    nome_completo       TEXT    NOT NULL,
    email               TEXT    NOT NULL,
    telefone_whatsapp   TEXT    NOT NULL,
    mensagem            TEXT    DEFAULT NULL,
    data_cadastro       TEXT    DEFAULT (datetime('now','localtime')),
    status_atendimento  TEXT    DEFAULT 'novo'
                                CHECK(status_atendimento IN ('novo','contatado','convertido','perdido'))
);

CREATE INDEX IF NOT EXISTS idx_leads_email ON leads(email);
CREATE INDEX IF NOT EXISTS idx_leads_status ON leads(status_atendimento);
```

---

## 3. Arquitetura dos Endpoints da API

### 3.1 `POST /api/leads` — Cadastro de lead

| Campo | Tipo | Validação |
|---|---|---|
| `nome_completo` | string (3-150) | Obrigatório, sanitizado |
| `email` | string | Obrigatório, regex de e-mail |
| `telefone_whatsapp` | string (14-15) | Obrigatório, apenas dígitos após limpeza |
| `mensagem` | string (0-500) | Opcional, sanitizado |

**Resposta sucesso (201):**
```json
{"sucesso": true, "mensagem": "Lead cadastrado com sucesso!"}
```

**Resposta erro (422):**
```json
{"sucesso": false, "mensagem": "E-mail inválido.", "erros": [...]}
```

### 3.2 `GET /api/leads` — Listagem (uso interno)

Retorna array de leads.

### 3.3 Segurança

- `express.json({ limit: '10kb' })` — proteção contra payload excessivo
- Sanitização com `validator` (trim, escape, stripLow)
- **Prepared statements** obrigatórios (`better-sqlite3` usa `?` posicionais — sem concatenação)
- CORS configurado com lista de origens permitidas
- `helmet` para headers de segurança
- `api/db/` listado no `.gitignore`

---

## 4. Especificações Visuais do Front-end em React

### 4.1 Stack

- **React 18** — Componentes desacoplados e estado reativo
- **Vite** — HMR e compilação rápida
- **Tailwind CSS** — Classes utilitárias para responsividade e design moderno
- **JavaScript JSX** — Fetch API, máscaras dinâmicas no estado do React

### 4.2 Componentes da Landing Page

1. **Header.jsx** — Navbar fixa no topo, logo + CTA "Quero saber mais"
2. **Hero.jsx** — Headline forte, subheadline, ilustração, botão CTA âncora para o form
3. **Beneficios.jsx** — 3 cards com ícones e destaques
4. **FormularioLead.jsx** — Campos controlados pelo estado, máscara (xx) xxxxx-xxxx, validação, botão com loading spinner
5. **Toast.jsx** — Notificação flutuante de sucesso ou erro (desaparece após 4 segundos)
6. **Footer.jsx** — Direitos reservados

---

## 5. Fluxo de Desenvolvimento

1. ✅ **Fase 1:** Planejamento e arquitetura
2. ✅ **Fase 2:** Backend API em Node.js + Express + SQLite
3. ✅ **Fase 3:** Migração do frontend para React + Vite + Tailwind CSS
4. ✅ **Fase 4:** Atualização da documentação e execução dos testes de integração

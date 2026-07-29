# Landing Page de Captura de Leads — Sistema Full Stack (Node.js + Express + React + SQLite)

> **Projeto Acadêmico:** Aplicação Full Stack moderna para captura, validação e armazenamento de *leads* em tempo real.

---

## 📋 Sobre o Projeto

Este projeto consiste em uma **Landing Page de Alta Conversão** desenvolvida em **React 18** com **Vite** e **Tailwind CSS**, integrada a uma **API RESTful** desenvolvida com Node.js e Express, utilizando o banco de dados leve **SQLite** para persistência de dados.

A aplicação foi desenvolvida seguindo boas práticas de arquitetura de software, componentes modulares em React, validação e sanitização de dados no backend, segurança HTTP com Helmet e navegação responsiva.

---

## 🛠️ Tecnologias Utilizadas

### **Frontend (Interface do Usuário com React)**
- **React 18** — Biblioteca para construção de interfaces baseadas em componentes.
- **Vite** — Build tool ultra-rápida para desenvolvimento frontend moderno.
- **Tailwind CSS** — Framework CSS utilitário para design responsivo, elegante e moderno.
- **JavaScript ES6+ (JSX)** — Lógica do cliente, manipulação de estado (`useState`, `useEffect`), máscaras de formulário e consumo da API via `fetch`.

### **Backend (API RESTful)**
- **Node.js** — Ambiente de execução JavaScript no servidor.
- **Express.js** — Framework web para rotas, middlewares e servir os arquivos de produção do React.
- **better-sqlite3** — Driver síncrono e de alta performance para o banco SQLite.
- **Helmet** — Middleware para configuração de cabeçalhos de segurança HTTP.
- **CORS** — Habilitação de Cross-Origin Resource Sharing.
- **Validator** — Lib para sanitização e validação avançada de e-mails e textos.
- **Dotenv** — Gerenciamento de variáveis de ambiente.

---

## 📁 Estrutura do Projeto

```text
testenodejs/
├── api/                          # Servidor Backend em Node.js
│   ├── db/                       # Banco de dados SQLite (criado em runtime)
│   │   └── landing.db            # Arquivo da base de dados local
│   ├── src/
│   │   ├── config/
│   │   │   └── conexaoBanco.js   # Inicialização e conexão do SQLite
│   │   ├── controladores/
│   │   │   └── leadControlador.js# Regras de negócio da API
│   │   ├── rotas/
│   │   │   └── leadRotas.js      # Endpoints da aplicação
│   │   ├── utilitarios/
│   │   │   └── validadores.js    # Sanitização e validação dos inputs
│   │   ├── app.js                # Configuração do Express, CORS e estáticos do React
│   │   └── server.js             # Inicialização da porta e servidor
│   ├── .env                      # Variáveis de ambiente
│   ├── iniciarBanco.js           # DDL de criação da tabela de leads
│   └── package.json              # Dependências e scripts do Node.js
│
├── frontend/                     # Interface Web em React + Vite
│   ├── public/                   # Recursos estáticos públicos
│   ├── src/
│   │   ├── components/           # Componentes modulares React
│   │   │   ├── Header.jsx        # Cabeçalho fixo com CTA
│   │   │   ├── Hero.jsx          # Seção principal com banner e destaque
│   │   │   ├── Beneficios.jsx    # Cards de benefícios
│   │   │   ├── FormularioLead.jsx# Formulário com máscara de WhatsApp e validação
│   │   │   ├── Toast.jsx         # Feedback visual (mensagens temporárias)
│   │   │   └── Footer.jsx        # Rodapé da página
│   │   ├── App.jsx               # Componente raiz da aplicação
│   │   ├── main.jsx              # Ponto de entrada do React (ReactDOM.createRoot)
│   │   └── index.css             # Estilos globais e importação do Tailwind CSS
│   ├── index.html                # HTML base do Vite
│   ├── vite.config.js            # Configuração do Vite e proxy da API
│   ├── tailwind.config.js        # Configuração das rotas de scan do Tailwind CSS
│   ├── postcss.config.js         # Configuração do PostCSS
│   └── package.json              # Dependências do React, Vite e Tailwind
│
├── doc/                          # Documentação técnica do projeto
│   └── plano_landingpage_nodejs.md
│
├── .gitignore                    # Arquivos ignorados pelo Git
└── README.md                     # Documentação oficial do repositório
```

---

## 🗄️ Modelagem do Banco de Dados (SQLite)

O banco de dados SQLite é inicializado automaticamente na subida da aplicação através do script `iniciarBanco.js`.

### **Tabela `leads`**

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

## 🚀 Endpoints da API

| Método | Endpoint | Descrição | Payload (Body) |
|---|---|---|---|
| `GET` | `/` | Servidor estático da Landing Page em React (`frontend/dist`) | — |
| `GET` | `/api/health` | Health Check da API | — |
| `POST` | `/api/leads` | Cadastra um novo lead | JSON (nome, email, telefone, mensagem) |
| `GET` | `/api/leads` | Lista todos os leads cadastrados | — |

---

## 🔧 Como Executar o Projeto

### **Pré-requisitos**
- **Node.js** (v18 ou superior) e **npm** instalados.
- **Git** instalado.

---

### 🚀 **Como Iniciar no Ambiente de Desenvolvimento**

1. **Instalar as dependências da API (Backend):**
   ```bash
   cd api
   npm install
   ```

2. **Instalar as dependências do Frontend (React):**
   ```bash
   cd ../frontend
   npm install
   ```

3. **Executar o Backend (API na porta 3000):**
   ```bash
   cd ../api
   npm run dev
   ```

4. **Executar o Frontend (React Vite na porta 5173):**
   Em um segundo terminal:
   ```bash
   cd frontend
   npm run dev
   ```

5. **Acessar a aplicação no navegador:**
   - **Frontend React (Modo Dev com Hot Reload):** [http://localhost:5173](http://localhost:5173)
   - **Health Check da API:** [http://localhost:3000/api/health](http://localhost:3000/api/health)

---

### 📦 **Como Gerar o Build de Produção do React**

Para gerar a versão otimizada do React e servir diretamente pelo servidor Express (Porta 3000):

1. **Gerar o build do React:**
   ```bash
   cd frontend
   npm run build
   ```

2. **Subir o servidor backend Express:**
   ```bash
   cd ../api
   npm start
   ```

3. **Acessar a aplicação de produção:**
   - [http://localhost:3000](http://localhost:3000)

---

## 🛡️ Segurança e Boas Práticas

- **Arquitetura de Componentes:** Código React desacoplado, modular e reativo.
- **Prepared Statements:** Consulta SQL via `better-sqlite3` prevenindo **SQL Injection**.
- **Sanitização de Entradas:** Tratamento no backend com `validator` prevenindo **XSS**.
- **Proteção contra Payload Abusivo:** Express configurado com limite de `10kb`.

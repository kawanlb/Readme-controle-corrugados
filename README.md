
# Controle Corrugados

Sistema web para controle operacional de gaiolas/corrugados, com apoio a Produção, Logística e Administração.

O aplicativo foi construído em React + Vite e usa Firebase para autenticação, persistência em tempo real e auditoria das ações executadas pelos usuários.

## Visão geral

O sistema centraliza o acompanhamento das gaiolas em um fluxo único:

- Produção cadastra e atualiza registros de gaiolas.
- Logística altera o status de bipado e acompanha movimentações.
- Administração visualiza, corrige, comenta, limpa registros e consulta o histórico de atividades.

Toda a interface conversa diretamente com o Firestore em tempo real. Não existe backend próprio no projeto; o Firebase faz a camada de dados e autenticação.

## Objetivo do sistema

O objetivo é dar rastreabilidade ao ciclo de vida de cada gaiola, registrando:

- status atual da gaiola;
- dados operacionais de produção;
- marcação de bipado pela logística;
- comentários e observações;
- histórico de alterações para auditoria.

## Perfis de acesso

O sistema trabalha com três perfis principais, identificados por e-mail no Firebase Auth:

- Admin: `admin.............`
- Produção: `producao.............`
- Logística: `logistica.........`

Cada perfil enxerga rotas e ações diferentes:

- Produção: consulta registros, cria/edita gaiolas e salva dados operacionais.
- Logística: atualiza o campo `bipado` e altera o status permitido para sua área.
- Admin: acessa o painel completo, ajusta status, adiciona/remove comentários e limpa logs de atividade.

## Funcionalidades principais

### Área inicial
A tela inicial oferece três acessos diretos:

- Produção
- Logística
- Admin

Também existe a página de aviso legal.

### Produção
- Lista registros por gaiola.
- Abre o formulário de edição/cadastro.
- Salva dados operacionais no Firestore.
- Registra atividade de criação e edição.
- Permite limpar uma gaiola quando o status for `VAZIA`.

### Logística
- Lista registros disponíveis.
- Atualiza o campo `bipado`.
- Pode alterar status de acordo com o fluxo operacional.
- Mantém trilha de auditoria em `activities`.

### Admin
- Visualiza tabela operacional e visão de planta.
- Consulta atividades recentes.
- Inclui comentários nos registros.
- Remove comentários.
- Atualiza status para `ANÁLISE`, `VAZIA`, `RETRABALHO` e `LIBERADA`.
- Exporta históricos em planilha `.xlsx`.
- Limpa log de atividades com registro de auditoria da limpeza.

## Rotas da aplicação

- `/` -> tela de acesso inicial
- `/aviso-legal` -> aviso legal do sistema
- `/producao` -> área principal da produção
- `/producao/form` -> formulário de cadastro/edição
- `/producao/login` -> login da produção
- `/logistica` -> área principal da logística
- `/logistica/login` -> login da logística
- `/admin` -> painel administrativo
- `/admin/login` -> login administrativo
- `/admin/atividades` -> histórico de atividades administrativas

As rotas protegidas redirecionam para login quando o usuário não está autenticado para aquele perfil.

## Modelo de dados

### Coleção `records`
Armazena o estado atual de cada gaiola.

Campos mais usados:

- `gaiola`
- `produto`
- `turno`
- `data`
- `inicio`
- `fim`
- `pecas`
- `pesagem`
- `bipado`
- `status`
- `operador`
- `timestamp`
- `comments`
- `updatedAt`

### Coleção `activities`
Registra auditoria das ações executadas no sistema.

Exemplos de eventos gravados:

- `SAVE`
- `EDIT`
- `STATUS_CHANGE`
- `COMMENT_ADD`
- `COMMENT_REMOVE`
- `CLEAR`
- `CLEAR_LOG`
- outros eventos operacionais

### Estrutura de comentário
Cada comentário possui:

- `text`
- `author`
- `timestamp`

## Tecnologias

- React 18
- Vite
- React Router DOM
- Firebase Auth
- Cloud Firestore
- Tailwind CSS 4
- shadcn/ui e Radix UI
- Lucide React
- XLSX para exportação de histórico

## Variáveis de ambiente

O projeto usa variáveis `VITE_` para configurar o Firebase no frontend.

Configure no ambiente local ou no Vercel:

- `VITE_FIREBASE_API_KEY`
- `VITE_FIREBASE_AUTH_DOMAIN`
- `VITE_FIREBASE_PROJECT_ID`
- `VITE_FIREBASE_STORAGE_BUCKET`
- `VITE_FIREBASE_MESSAGING_SENDER_ID`
- `VITE_FIREBASE_APP_ID`

## Execução local

1. Instale as dependências:

   ```bash
   npm install
   ```

2. Inicie o ambiente de desenvolvimento:

   ```bash
   npm run dev
   ```

3. Gere o build de produção, se necessário:

   ```bash
   npm run build
   ```

## Deploy no Vercel

O projeto está preparado para SPA. O arquivo `vercel.json` faz rewrite de todas as rotas para `index.html`, permitindo refresh e acesso direto por URL.

Antes de publicar, valide:

- as variáveis `VITE_` cadastradas no projeto Vercel;
- o domínio autorizado no Firebase Authentication, se o login estiver ativo;
- a execução bem-sucedida do build (`npm run build`).

## Estrutura do projeto

- `src/main.tsx` -> ponto de entrada da aplicação
- `src/firebase.ts` -> configuração do Firebase
- `src/app/App.tsx` -> rotas, regras de acesso e fluxo principal
- `src/app/components/` -> telas, views e componentes da aplicação
- `src/app/types/` -> tipos do domínio
- `src/app/utils/` -> formatadores e utilitários
- `src/styles/` -> estilos globais

## Observações para a equipe de desenvolvimento

- O sistema depende de Firestore em tempo real para refletir mudanças imediatamente na interface.
- As alterações de status e comentários sempre geram registros em `activities` para auditoria.
- O acesso aos módulos é controlado por perfil autenticado, não apenas por rota visível.
- O app funciona como uma SPA e precisa do rewrite configurado para evitar erro em refresh de página.

## Melhorias futuras sugeridas

- formalizar o arquivo `.env.example` com as variáveis obrigatórias;
- documentar as regras do Firestore e os índices usados em produção;
- incluir fluxos de teste manual por perfil;
- detalhar a modelagem dos status e regras de negócio por área.
  

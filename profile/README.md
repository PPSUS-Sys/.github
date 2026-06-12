# PPSUS-SysAI

## Inovacao em gestao publica da tuberculose no SUS

<img width="2752" height="1536" alt="PPSUS-SysAI" src="https://github.com/user-attachments/assets/537cbebe-523e-4850-b832-a6f826e58834" />

O **PPSUS-SysAI** e uma plataforma digital para apoiar a prevencao, o monitoramento, o acompanhamento clinico e a gestao publica da tuberculose no Sistema Unico de Saude. A solucao combina dashboards epidemiologicos, gestao de pacientes e prontuarios, recuperacao de casos similares, assistente de IA, cache de literatura cientifica e extensao de navegador para apoio contextual ao uso do sistema.

O projeto foi desenvolvido para integrar ciencia de dados, inteligencia artificial e visualizacao territorial em um fluxo operacional voltado a equipes de saude, pesquisadores e gestores publicos.

## Objetivos

- Apoiar secretarias e equipes de saude no acompanhamento de casos de tuberculose.
- Centralizar dados clinicos, territoriais e operacionais em interfaces de uso diario.
- Oferecer dashboards para analise epidemiologica, distribuicao territorial e indicadores de acompanhamento.
- Apoiar decisao clinica com busca de casos similares baseada em representacoes vetoriais.
- Disponibilizar um agente de chat com suporte contextual, referencias cientificas e captura de tela autorizada pelo usuario.
- Reduzir friccao operacional por meio de extensao de navegador integrada ao sistema.

## Arquitetura

A plataforma e organizada em quatro frentes principais:

| Repositorio | Papel | Stack principal |
| --- | --- | --- |
| [`app-web`](https://github.com/PPSUS-Sys/app-web) | Front-end web, landing page, login, dashboard, gestao de pacientes, prontuarios, acompanhamento, comparacao e administracao | Next.js, React, TypeScript, Tailwind CSS, shadcn/ui, Recharts, Leaflet |
| [`api-gateway`](https://github.com/PPSUS-Sys/api-gateway) | API principal/BFF, autenticacao, sessoes, regras de acesso, CRUD clinico, relatorios e encaminhamento para servicos especializados | NestJS, Node.js, Sequelize, MySQL, JWT |
| [`api-especializada`](https://github.com/PPSUS-Sys/api-especializada) | Processamento especializado, IA, embeddings, busca vetorial, casos similares, chat, cache cientifico e integracoes externas | FastAPI, Python, Redis/RediSearch, MySQL, OpenRouter/Ollama, PubMed, Cloudflare R2 |
| [`extension`](https://github.com/PPSUS-Sys/extension) | Extensao Chrome com painel lateral de chat, captura de tela autorizada, historico local e integracao com o gateway | Chrome Extension Manifest V3, JavaScript modular |

Fluxo resumido:

```text
Usuario
  -> app-web ou extension
  -> api-gateway
  -> MySQL para dados transacionais
  -> api-especializada para IA, chat, casos similares e CAG
  -> Redis/RediSearch para vetores, cache e sessoes especializadas
  -> PubMed/OpenRouter/Ollama/R2 conforme o recurso acionado
```

## Componentes do sistema

### Aplicacao web

O `app-web` concentra a experiencia principal do usuario:

- pagina institucional e login;
- dashboard epidemiologico;
- gestao de pacientes e prontuarios;
- acompanhamento individual de pacientes;
- comparacao entre paciente de referencia e casos similares;
- area administrativa;
- gestao do cache cientifico;
- paginas de apoio para uso da extensao.

### API Gateway

O `api-gateway` centraliza as chamadas do front-end e da extensao:

- autenticacao JWT;
- sessao separada por canal (`web` e `extension`);
- controle de acesso por perfil;
- endpoints de pacientes, prontuarios e relatorios;
- roteamento para chat, casos similares e cache cientifico na API especializada;
- Swagger em `/api/docs`.

### API especializada

A `api-especializada` isola os recursos computacionais e de IA:

- busca vetorial de casos similares em Redis/RediSearch;
- indexacao e reindexacao de perfis clinicos;
- agente de chat contextual;
- analise de captura de tela quando autorizada pelo usuario;
- cache de literatura cientifica com PubMed;
- integracao com modelos via OpenRouter/Ollama;
- armazenamento de imagens do chat via Cloudflare R2 quando configurado.

### Extensao de navegador

A extensao `extension` adiciona um painel lateral no Chrome para suporte contextual:

- login via `api-gateway`;
- envio de mensagens ao agente especializado;
- historico local por usuario e sessao;
- captura de tela somente com confirmacao explicita;
- fluxo modular em `pages`, `services`, `components` e `utils`.

## Principais funcionalidades

- Monitoramento de indicadores de tuberculose.
- Visualizacao territorial e mapas de casos.
- Cadastro e gestao de pacientes.
- Gestao de prontuarios e exames.
- Acompanhamento clinico com indicadores de evolucao.
- Busca de casos similares por perfil clinico.
- Comparacao entre pacientes.
- Chat assistivo com contexto de sessao.
- Cache-Augmented Generation (CAG) para referencias cientificas.
- Gestao administrativa de usuarios e recursos internos.
- Extensao Chrome para suporte dentro do fluxo de trabalho.

## Dados e integracoes

O sistema foi pensado para trabalhar com dados clinicos e epidemiologicos relacionados a tuberculose, incluindo:

- dados de pacientes;
- notificacoes e identificadores como SINAN quando disponiveis;
- dados clinicos, comorbidades, tratamentos, exames, consultas e reacoes adversas;
- localizacao territorial e unidade de acompanhamento;
- referencias cientificas recuperadas em bases como PubMed.

Fontes e integracoes citadas no escopo do projeto incluem SINAN, e-SUS AB, SIVEP, TBweb e bases institucionais como HUJBB/UFPA/Ebserh, conforme disponibilidade, autorizacao e governanca de dados.

## Execucao local

Cada repositorio possui seu proprio README e arquivo `.env.example`. Em geral, o ambiente de desenvolvimento exige:

- Node.js 20+ para o front-end;
- Node.js/NestJS para o gateway;
- Python 3.11+ para a API especializada;
- MySQL ou MariaDB para dados transacionais;
- Redis com RediSearch para vetores e cache;
- Docker/Docker Compose para execucao containerizada;
- chaves externas apenas quando recursos como OpenRouter, PubMed API ou R2 forem usados.

Comandos base por servico:

```bash
# app-web
npm install
npm run dev

# api-gateway
npm install
npm run start:dev

# api-especializada
pip install -r requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8001
```

A extensao e carregada localmente pelo Chrome em `chrome://extensions`, usando `Load unpacked` na pasta do projeto.

## Configuracao e seguranca

- Credenciais reais nao devem ser versionadas no GitHub.
- Use `.env.example` como referencia e mantenha `.env` apenas em ambiente local/seguro.
- Tokens JWT, credenciais de banco, Redis, OpenRouter, R2 e APIs externas devem ser tratados como segredos.
- Dados clinicos e credenciais operacionais devem ficar em repositorios ou pastas privadas com controle de acesso.
- A captura de tela pela extensao deve ocorrer somente com consentimento explicito do usuario.
- Qualquer uso assistencial das sugestoes de IA deve ser validado por profissional responsavel.

## Repositorios

- [`PPSUS-Sys/app-web`](https://github.com/PPSUS-Sys/app-web)
- [`PPSUS-Sys/api-gateway`](https://github.com/PPSUS-Sys/api-gateway)
- [`PPSUS-Sys/api-especializada`](https://github.com/PPSUS-Sys/api-especializada)
- [`PPSUS-Sys/extension`](https://github.com/PPSUS-Sys/extension)
- [`PPSUS-Sys/.github`](https://github.com/PPSUS-Sys/.github)

## Roadmap operacional

Alguns pontos de evolucao ja mapeados para o projeto:

- melhorar fluxo de cadastro de pacientes, evitando campos redundantes e sincronizando novos registros com Redis;
- aprimorar continuidade de contexto no chat da extensao;
- padronizar dominios e ambientes entre web, gateway e extensao;
- fortalecer CORS e tratamento de erros do gateway;
- melhorar a gestao visual do CAG em `/dashboard/cache-cientifico`;
- organizar dados e credenciais em ambiente privado compartilhado;
- atualizar continuamente a documentacao tecnica dos servicos.

## Governanca

O projeto e academico-cientifico e voltado a inovacao em saude publica. O desenvolvimento deve priorizar:

- rastreabilidade de decisoes tecnicas;
- protecao de dados sensiveis;
- revisao de codigo;
- documentacao clara;
- validacao clinica e institucional dos recursos de apoio a decisao.

## Licenca e uso

Projeto academico-cientifico voltado a inovacao em gestao publica da tuberculose no Brasil. O uso de dados clinicos, credenciais e integracoes externas deve respeitar autorizacoes institucionais, politicas de privacidade e normas aplicaveis ao SUS e a pesquisa em saude.

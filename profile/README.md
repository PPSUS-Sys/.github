# PPSUS-SysAI

## Plataforma de apoio à gestão e ao acompanhamento da tuberculose no SUS

<img width="2752" height="1536" alt="PPSUS-SysAI" src="https://github.com/user-attachments/assets/537cbebe-523e-4850-b832-a6f826e58834" />

O **PPSUS-SysAI** é uma plataforma digital voltada ao apoio da gestão pública, do acompanhamento clínico e da análise epidemiológica da tuberculose no contexto do Sistema Único de Saúde. O projeto reúne aplicação web, API de negócio, serviços especializados de inteligência artificial e uma extensão de navegador para apoiar fluxos operacionais com dados clínicos, evidências científicas e análise contextual.

A proposta do sistema não é substituir a avaliação profissional, mas organizar informações, reduzir fricções de uso, apoiar a interpretação de dados e oferecer ferramentas computacionais para acompanhamento, comparação e priorização de casos.

## Escopo do projeto

A plataforma cobre quatro frentes principais:

- **Gestão e acompanhamento de pacientes:** cadastro, prontuários, exames, consultas, indicadores clínicos e histórico de acompanhamento.
- **Análise epidemiológica e territorial:** dashboards, gráficos, filtros temporais e visualização de distribuição de casos.
- **Apoio computacional à decisão:** busca de casos similares, geração de resumos, cache de literatura científica e integração com modelos de linguagem.
- **Suporte operacional via extensão:** chat contextual no navegador, histórico por sessão e captura de tela somente mediante confirmação explícita do usuário.

## Arquitetura atual

A solução está organizada em repositórios independentes, cada um com responsabilidade definida.

| Repositório | Responsabilidade | Tecnologias principais |
| --- | --- | --- |
| [`app-web`](https://github.com/PPSUS-Sys/app-web) | Interface web, landing page, autenticação visual, dashboards, gestão de pacientes, prontuários, acompanhamento, comparação de casos e telas administrativas. | Next.js, React, TypeScript, Tailwind CSS, shadcn/ui, Recharts, Leaflet |
| [`api-gateway`](https://github.com/PPSUS-Sys/api-gateway) | API principal do sistema. Centraliza autenticação, sessões, permissões, CRUD clínico, relatórios e encaminhamento para a API especializada. | NestJS, Node.js, Sequelize, MySQL, JWT |
| [`api-especializada`](https://github.com/PPSUS-Sys/api-especializada) | Serviços de IA e processamento especializado: casos similares, embeddings, chat, análise de captura de tela, cache científico e integrações externas. | FastAPI, Python, Redis/RediSearch, MySQL, OpenRouter/Ollama, PubMed, Cloudflare R2 |
| [`extension`](https://github.com/PPSUS-Sys/extension) | Extensão Chrome com painel lateral de chat, sessão local, histórico por usuário e captura de tela autorizada. | Chrome Extension Manifest V3, JavaScript modular |
| [`.github`](https://github.com/PPSUS-Sys/.github) | Perfil público da organização e documentação geral do projeto. | Markdown |

## Fluxo operacional

```text
Usuário
  -> app-web ou extension
  -> api-gateway
  -> MySQL para dados transacionais
  -> api-especializada para IA, chat, CAG e casos similares
  -> Redis/RediSearch para vetores, cache e contexto especializado
  -> provedores externos quando configurados: PubMed, OpenRouter/Ollama e Cloudflare R2
```

O `api-gateway` é o ponto central de entrada para clientes web e extensão. A `api-especializada` concentra operações de maior custo computacional ou dependentes de IA, preservando separação entre regras de negócio, autenticação e processamento semântico.

## Componentes em detalhe

### Aplicação web

O `app-web` é a interface principal do PPSUS-SysAI. Ele oferece:

- página institucional e fluxo de login;
- dashboard epidemiológico;
- gestão de pacientes;
- gestão de prontuários e exames;
- acompanhamento clínico individual;
- visualização de casos similares;
- comparação entre paciente de referência e caso recuperado;
- gestão administrativa de usuários;
- gestão do cache científico usado pelo CAG;
- páginas de apoio à extensão do navegador.

### API Gateway

O `api-gateway` atua como API principal/BFF da plataforma. Suas responsabilidades incluem:

- autenticação via JWT;
- validação de sessão ativa;
- separação de sessão por canal (`web` e `extension`);
- controle de permissões por perfil;
- endpoints de pacientes, prontuários e relatórios;
- encaminhamento de chat, casos similares e cache científico para a API especializada;
- documentação Swagger em `/api/docs`.

### API especializada

A `api-especializada` concentra os módulos de IA e recuperação semântica. Atualmente cobre:

- indexação vetorial de perfis clínicos;
- busca de casos similares em Redis/RediSearch;
- cálculo de atributos em comum entre pacientes;
- agente de chat com contexto de sessão;
- análise de screenshots quando o usuário autoriza a captura;
- cache de literatura científica com busca em PubMed;
- integração com modelos via OpenRouter/Ollama;
- armazenamento de imagens do chat em Cloudflare R2 quando configurado.

### Extensão de navegador

A extensão adiciona um painel lateral ao Chrome para apoiar o uso do sistema durante o fluxo de trabalho. Ela realiza:

- autenticação via `api-gateway`;
- envio de mensagens ao agente especializado;
- persistência local de sessão e histórico por usuário;
- captura da aba ativa somente após confirmação explícita;
- renderização de respostas, referências, gráficos e ações rápidas.

## Dados e integrações

O sistema trabalha com dados relacionados ao acompanhamento da tuberculose, incluindo:

- dados cadastrais de pacientes;
- identificadores e notificações, como SINAN quando disponíveis;
- informações clínicas, comorbidades, tratamentos, exames, consultas e reações adversas;
- localização territorial e unidade de acompanhamento;
- referências científicas recuperadas em fontes externas como PubMed.

Fontes e integrações citadas no escopo do projeto incluem SINAN, e-SUS AB, SIVEP, TBweb e bases institucionais como HUJBB/UFPA/Ebserh, sempre condicionadas à disponibilidade, autorização institucional e governança de dados.

## Recursos principais

- Cadastro e gestão de pacientes.
- Gestão de prontuários, exames e informações clínicas.
- Dashboards epidemiológicos e territoriais.
- Acompanhamento individual de pacientes.
- Indicadores de evolução e pontos de atenção.
- Busca vetorial de casos similares.
- Comparação estruturada entre pacientes.
- Chat assistivo com contexto de sessão.
- Cache-Augmented Generation (CAG) para apoio com referências científicas.
- Extensão Chrome para suporte contextual durante o uso do sistema.

## Execução local

Cada repositório possui seu próprio README e arquivo `.env.example`. A configuração completa depende do conjunto de serviços que será executado.

Requisitos comuns:

- Node.js 20+ para a aplicação web;
- Node.js/NestJS para o API Gateway;
- Python 3.11+ para a API especializada;
- MySQL ou MariaDB para dados transacionais;
- Redis com RediSearch para vetores, cache e sessões especializadas;
- Docker/Docker Compose para execução containerizada;
- credenciais externas apenas quando recursos como OpenRouter, PubMed API ou R2 forem utilizados.

Comandos base por serviço:

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

A extensão é carregada localmente pelo Chrome em `chrome://extensions`, usando a opção `Load unpacked` na pasta do projeto.

## Configuração e segurança

- Credenciais reais não devem ser versionadas no GitHub.
- Arquivos `.env.example` devem ser usados apenas como referência de configuração.
- Arquivos `.env` reais devem permanecer fora do versionamento e em ambiente controlado.
- Tokens JWT, credenciais de banco, Redis, OpenRouter, R2 e APIs externas devem ser tratados como segredos.
- Dados clínicos, bases operacionais e credenciais devem ser acessados apenas por pessoas autorizadas.
- A captura de tela pela extensão deve ocorrer somente mediante consentimento explícito do usuário.
- Resultados produzidos por IA são apoio informacional e exigem validação profissional e institucional.

## Acesso a credenciais

As credenciais, documentos de acesso e dados operacionais do projeto devem ser concentrados no Google Drive privado do PPSUS-SysAI:

- [Drive privado de credenciais e acessos](https://drive.google.com/drive/folders/1WfB8KC8jRw8ROjRzxvLUn8hAVn9WafZe?usp=sharing)

Apenas pessoas com os devidos acessos e autorização do projeto podem acessar esse Drive. O link não deve ser tornado público, e qualquer compartilhamento deve ser feito com controle por conta autorizada.

## Organização dos repositórios

- [`PPSUS-Sys/app-web`](https://github.com/PPSUS-Sys/app-web)
- [`PPSUS-Sys/api-gateway`](https://github.com/PPSUS-Sys/api-gateway)
- [`PPSUS-Sys/api-especializada`](https://github.com/PPSUS-Sys/api-especializada)
- [`PPSUS-Sys/extension`](https://github.com/PPSUS-Sys/extension)
- [`PPSUS-Sys/.github`](https://github.com/PPSUS-Sys/.github)

## Melhorias em andamento

Alguns pontos já mapeados para evolução do sistema:

- melhoria do fluxo de cadastro de pacientes, incluindo redução de campos redundantes, endereço assistido e sincronização com Redis;
- melhoria da continuidade de contexto no chat da extensão;
- padronização de domínios e ambientes entre web, gateway e extensão;
- fortalecimento do CORS e do tratamento de erros do gateway;
- aprimoramento da interface de gestão do CAG em `/dashboard/cache-cientifico`;
- centralização operacional de dados e credenciais em ambiente privado;
- atualização contínua dos READMEs técnicos dos serviços.

## Governança técnica

O desenvolvimento do PPSUS-SysAI deve priorizar:

- rastreabilidade de decisões técnicas;
- revisão de código e issues bem delimitadas;
- documentação objetiva e atualizada;
- proteção de dados sensíveis;
- separação clara entre código, configuração e credenciais;
- validação clínica e institucional dos recursos de apoio à decisão;
- manutenção de histórico e contexto suficiente para operação segura.

## Natureza do projeto

O PPSUS-SysAI é um projeto acadêmico-científico aplicado à inovação em saúde pública. Seu uso em ambientes reais deve respeitar autorizações institucionais, governança de dados, políticas de privacidade e normas aplicáveis ao SUS e à pesquisa em saúde.

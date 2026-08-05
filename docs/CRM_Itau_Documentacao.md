# CRM Comercial Itaú — Documentação de Planejamento

> Documento vivo, construído a partir das discussões de planejamento (board Figma + conversas de detalhamento). Objetivo: dar suporte à decisão de escopo e priorização pela gestão do Itaú. Cada seção lista o que precisa ser feito na respectiva etapa/frente.

Board de referência: [Projeto CRM (Figma)](https://www.figma.com/board/CcMrGpFvfkUzSKrKQjXLEF/Projeto-CRM)

---

## 1. Visão Geral

CRM comercial para relacionamento com clientes do Itaú, com modelo de navegação inspirado no **Salesforce**: o usuário busca uma **conta** (ou subconta) e a partir dela acessa todo o histórico, oportunidades, propostas e tarefas relacionadas ao cliente.

---

## 2. Modelo de Contas e Hierarquia de Clientes

*(em elaboração — detalhado em 2026-08-04)*

Estrutura hierárquica de contas, do nível mais alto para o mais granular:

1. **Grupo Econômico** — nível mais alto da hierarquia; representa as grandes empresas/holdings que agrupam múltiplos CNPJs sob um mesmo controle econômico.
2. **Cliente Contraparte** — dentro de um Grupo Econômico, separado em:
   - **Raiz** — CNPJ raiz (matriz), identifica a empresa "mãe" do agrupamento.
   - **CNPJ** — CNPJs específicos (filiais) vinculados à raiz.
3. **Segmento** — todo cliente é distribuído em um segmento (ex.: Corporate, Empresarial, Middle Market — segmentação exata a confirmar).
4. **Usuário ↔ Segmento** — cada usuário (gerente/relacionamento) atende um ou mais segmentos. Relação muitos-para-muitos entre `USUARIO` e `SEGMENTO`.

**Impacto no modelo de dados atual (board Figma):**
A tabela `CLIENTE` hoje é flat (`id, nome, cpf_cnpj, segmento, telefone, email, gerente_responsavel_id`) e não representa a hierarquia Grupo Econômico → Raiz → CNPJ, nem a relação N:N entre usuário e segmento (hoje é 1 gerente por cliente). Precisa evoluir para algo como:

- Nova entidade `GRUPO_ECONOMICO` (id, nome).
- `CLIENTE` (ou nova entidade `CONTRAPARTE`) ganha `grupo_economico_id`, `cnpj_raiz`, `cnpj` (matriz/filial), `tipo_conta` (raiz | filial).
- Nova entidade de junção `USUARIO_SEGMENTO` (usuario_id, segmento_id) para suportar N:N.
- `SEGMENTO` pode virar entidade própria em vez de campo string livre em `CLIENTE`.

**Pontos em aberto (a confirmar com a gestão/negócio):**
- Quais são os segmentos oficiais e suas regras de elegibilidade?
- Uma conta (CNPJ) pode pertencer a mais de um Grupo Econômico? (assumido: não)
- A atribuição de usuário responsável é por conta individual, por Grupo Econômico inteiro, ou derivada do segmento?
- Como tratar cliente Pessoa Física (CPF) nessa hierarquia — ela se aplica só a PJ?

---

## 3. Arquitetura de Alto Nível *(já mapeada no board)*

- **Usuários do Sistema**: Time Comercial, Gestores e Supervisores, Backoffice.
- **Canais de Atendimento**: WhatsApp, Telefone, E-mail, Chat Online, Atendimento Presencial.
- **Núcleo do CRM**: Gestão de Leads, Pipeline de Vendas, Histórico de Interações, Gestão de Carteira de Clientes.
- **Integrações**: Core Bancário Itaú, SSO/Autenticação Corporativa, Plataforma de Dados do Cliente, BI e Dashboards, Compliance e Auditoria.
- **Atendimento ao Cliente**: Histórico Unificado Omnichannel, Central de Tarefas e Follow-up, Agenda Comercial.
- **Gestão Comercial**: Pipeline de Vendas, Gestão de Carteira de Clientes, Segmentação e Priorização.
- **Gestão e Performance**: Dashboards e Relatórios, Metas e Comissionamento.
- **Governança**: Workflows de Aprovação e Alçadas, Compliance e Trilha de Auditoria.

*(Detalhamento do que precisa ser feito em cada bloco — pendente, a preencher conforme o usuário for descrevendo.)*

---

## 4. Roadmap de Entregas *(já mapeado no board)*

| Fase | Período | Entregas |
|---|---|---|
| Fase 1 — MVP | Ago–Set/2026 | Cadastro e Segmentação de Clientes; Integração Core Bancário Itaú; Pipeline de Vendas Básico |
| Fase 2 — Omnichannel | Set–Out/2026 | Histórico Unificado de Atendimento; Integração WhatsApp e Chat |
| Fase 3 — Automação | Out–Nov/2026 | Workflows de Aprovação e Alçadas; Agenda e Central de Tarefas |
| Fase 4 — Analytics | Nov/2026–Jan/2027 | Dashboards de Performance; Metas e Comissionamento |
| Fase 5 — Compliance | Jan/2027 | Trilha de Auditoria e Compliance |

*(Detalhamento do que precisa ser feito em cada fase — pendente.)*

---

## 5. Funcionalidade Prioritária: Registro de Visitas (Call Reports)

*(em elaboração — detalhado em 2026-08-04)*

Esta é a **funcionalidade de maior valor** a ser entregue primeiro: o registro de **visitas/call reports** — o histórico de contato entre a mesa (especialistas, traders) e o cliente.

### Canais de contato (4 formas principais)

| Canal | Ferramenta de integração | Situação |
|---|---|---|
| Microsoft Teams | Teams (nativo Microsoft) | Cliente pode entrar em contato via Teams — integração a definir |
| WhatsApp | **Tuvis** — plataforma global de segurança, conformidade e produtividade para comunicações corporativas em apps de mensagens (WhatsApp, Telegram). Faz arquivamento de conversas, DLP (prevenção contra vazamento de dados) e tem integração nativa com CRMs | A confirmar plano de integração técnica com este CRM |
| Telefone | Ainda sem ferramenta definida | Nenhuma solução de captura de ligação identificada até o momento |
| E-mail | Outlook | Integração com Outlook |

### Fluxo de uso

1. Usuário entra na plataforma e **pesquisa o Grupo Econômico ou o Cliente** (busca, ver seção 2).
2. A partir da conta encontrada, o usuário pode **registrar manualmente** uma Visita/Call Report.
3. Alternativamente, a plataforma **captura automaticamente** a visita a partir dos canais integrados (Teams, Tuvis/WhatsApp, Outlook, telefone — ver tabela de canais acima), sem exigir que o usuário a crie do zero.

Ou seja: o registro de visita tem **duas origens possíveis** — manual (iniciada pelo usuário a partir da busca de conta) ou automática (iniciada pela integração/captura de canal). Isso reforça a necessidade de um campo `origem` na entidade `VISITA` (ex.: `manual` | `teams` | `tuvis_whatsapp` | `outlook` | `telefone`) e de uma tela de busca de conta (Grupo Econômico/Cliente) como ponto de entrada principal do app.

### Objetivo funcional

Durante a conversa (em qualquer um dos 4 canais), o CRM deve:
1. **Identificar automaticamente o cliente** envolvido na conversa (a partir do número, e-mail, ou identidade do contato).
2. **Criar automaticamente um registro de visita** (call report) vinculado àquele cliente, sem exigir que o usuário cadastre manualmente do zero.

### O que precisa ser feito (alto nível)

- Definir estrutura de dados da **Visita/Call Report** (provavelmente nova entidade `VISITA` ou extensão de `INTERACAO` já existente no modelo ER — ver seção 5) — canal, data/hora, cliente identificado, participantes, resumo/notas, próximos passos.
- Integração com **Outlook** (Microsoft Graph API) para capturar e-mails trocados com o cliente.
- Integração com **Microsoft Teams** (Microsoft Graph API / Teams webhook) para identificar chamadas/mensagens com clientes.
- Integração com **Tuvis** (arquivamento/DLP de WhatsApp e Telegram) via a API/integração nativa de CRM que a plataforma oferece.
- Definir solução de captura de **telefone/ligações** — hoje não existe ferramenta; precisa ser avaliada/contratada.
- Lógica de **identificação automática do cliente** (matching por telefone, e-mail, ou conta do Teams) a partir do CLIENTE/CONTA cadastrado.
- Fluxo de criação automática da visita + tela de revisão/edição manual pelo usuário (para complementar com notas, corrigir cliente identificado incorretamente, etc.).

### Pontos em aberto (a confirmar)

- Como será resolvida a captura de ligações telefônicas (nenhuma ferramenta definida ainda)?
- O registro da visita é 100% automático ou sempre passa por confirmação/edição do usuário antes de salvar?
- Uma visita pode ter múltiplos participantes (do lado do cliente e do banco)? Como fica o vínculo com Grupo Econômico/Contraparte (seção 2) quando o contato é feito por alguém do grupo mas não necessariamente o CNPJ certo?

---

## 6. Arquitetura Técnica — Motor de Captura de Visitas

*(em elaboração — detalhado em 2026-08-04)*

### Contexto e restrições dadas

- Aplicação roda **dentro da rede corporativa do Itaú** (não é um SaaS público exposto).
- Há **acesso a tabelas/serviços AWS** já disponíveis no banco.
- **Visita é um objeto mutável**: pode ser incluída e alterada em tempo real (não é só um log de escrita única).
- O sistema precisa **trocar informações com outros sistemas de forma automática e em tempo real** (não em lote/batch).

### Padrão proposto: arquitetura orientada a eventos, com um adapter por canal

**1. Camada de Ingestão (Channel Adapters)** — um adapter por canal de captura:
- **Teams + Outlook**: Microsoft Graph API, usando *subscriptions* (webhooks) para eventos de chamada/chat (Teams) e e-mail (Outlook). Requer app registrado no Azure AD do Itaú, com permissões delegadas ou de aplicação, e certificado.
- **WhatsApp/Telegram**: webhook/API do **Tuvis** — a ferramenta já arquiva as conversas; falta confirmar o modelo de integração exposto (webhook, API de consulta, ou exportação de arquivo) para o CRM ser notificado de novas mensagens.
- **Telefone**: sem solução definida (ver pendência já registrada na seção 5) — bloqueia este adapter até haver uma ferramenta de captura/gravação de ligação.
- **Manual**: não precisa de adapter externo — é a própria tela do CRM (API interna) usada pelo usuário na busca de conta (ver fluxo de uso, seção 5).

**2. Camada de Eventos** — barramento de mensagens (ex.: Amazon EventBridge ou SQS/SNS) para desacoplar a ingestão do processamento. Cada canal publica um evento em um formato padronizado, independente da origem.

**3. Serviço de Identificação do Cliente (Matching Engine)** — recebe telefone, e-mail ou identidade do Teams contida no evento e resolve para `CLIENTE` / `CONTA` / `GRUPO_ECONOMICO` (usa a hierarquia da seção 2). Quando não é possível resolver automaticamente, marca a visita como "pendente de vinculação" para confirmação manual do usuário.

**4. Serviço de Visitas (Visita API)** — serviço único (API Gateway + Lambda, ou serviço containerizado) que expõe o CRUD da entidade `VISITA`. É o único ponto de escrita/leitura do objeto — tanto o registro manual quanto os 4 adapters automáticos passam por aqui, garantindo consistência.

**5. Armazenamento** — como o restante do modelo (`CLIENTE`, `CONTA`, `OPORTUNIDADE` etc., ver seção 7) é relacional, a recomendação é manter `VISITA` na mesma base relacional (ex.: Aurora PostgreSQL) para preservar integridade referencial com o resto do CRM. Para o requisito de tempo real, usar *Change Data Capture* (CDC — ex.: AWS DMS ou Debezium) lendo a tabela e publicando eventos de alteração no barramento. Alternativa avaliável: armazenar `VISITA` em DynamoDB (mais nativo para "objeto flexível e mutável em tempo real"), mas com perda de integridade referencial forte com o restante do modelo — a decidir com a equipe de arquitetura de dados do banco.

**6. Camada de Tempo Real (push para a tela)** — para a tela do CRM refletir instantaneamente uma visita capturada automaticamente: WebSockets (API Gateway WebSocket) ou AWS AppSync (GraphQL Subscriptions) notificando o usuário conectado sem precisar recarregar a página.

**7. Integração com sistemas downstream** — os mesmos eventos de "visita criada/atualizada" alimentam BI/Dashboards e Compliance/Auditoria (blocos já existentes no board de arquitetura), reaproveitando o mesmo barramento de eventos.

### Requisitos não funcionais / segurança

- Tráfego dentro da rede privada do Itaú — uso de VPC e VPC Endpoints para os serviços AWS, evitando saída para a internet pública.
- Autenticação/autorização via SSO corporativo (integração já mapeada no board).
- Trilha de auditoria de toda alteração no objeto Visita (quem alterou, quando, e de qual canal veio) — alimenta o bloco de Compliance já existente.
- Criptografia e retenção adequada de dados de conversas (LGPD), especialmente para os canais WhatsApp/Telegram (Tuvis) e ligações telefônicas.

### O que precisa ser feito (alto nível)

- Registrar aplicação no Azure AD do Itaú com permissões de Graph API (Teams + Outlook).
- Confirmar com o Tuvis o modelo de integração disponível para notificar o CRM de novas conversas.
- Definir/priorizar solução de captura de telefone (pendência em aberto).
- Desenhar e construir o Serviço de Identificação do Cliente (matching engine).
- Desenhar e construir o Serviço de Visitas (API + armazenamento) como núcleo do sistema.
- Desenhar a camada de eventos em tempo real (push para a tela do usuário).
- Validar com o time de infraestrutura/arquitetura do Itaú quais desses componentes AWS (Lambda, EventBridge, AppSync, Aurora, DMS etc.) já estão disponíveis e aprovados para uso dentro do ambiente do banco.

### Pontos em aberto (a confirmar)

- O ambiente AWS do Itaú permite os serviços citados (Lambda, EventBridge, AppSync), ou há restrições que exigem outra abordagem (ex.: apenas VMs/containers geridos)?
- Armazenamento relacional (Aurora) ou NoSQL (DynamoDB) para o objeto Visita — decisão da equipe de arquitetura de dados do banco.
- Já existe um barramento de eventos corporativo (ESB / Kafka interno) que deva ser reaproveitado, em vez de um novo com EventBridge/SQS?

---

## 7. Modelo de Dados (ER) *(já mapeado no board — base inicial)*

Entidades atuais: `CLIENTE`, `CONTA`, `LEAD`, `OPORTUNIDADE`, `PROPOSTA`, `PRODUTO`, `INTERACAO`, `USUARIO`, `EQUIPE`, `TAREFA`, `META`.

> Ver seção 2 para as extensões necessárias (Grupo Econômico, hierarquia de conta, segmento como entidade, usuário↔segmento).
> Ver seção 5 para a extensão de `INTERACAO`/nova entidade `VISITA` (call reports) e seção 6 para a arquitetura técnica de captura.

---

## 8. Ferramenta de Acompanhamento — Roteiro de Entregas

Página interativa e editável com o planejamento de entregas até janeiro/2027, em duas visões: **por entrega** (hierarquia Fase → Grupo → Entrega, estilo Gantt) e **por responsável** (uma linha por pessoa, com as entregas atribuídas na linha do tempo).

- Arquivo: [docs/planejamento_entregas.html](planejamento_entregas.html)
- Publicado como artifact: https://claude.ai/code/artifact/cfd304a1-155e-4ce4-81fc-22027a90b229
- Pré-carregado com o roteiro já definido (5 fases, Ago/2026–Jan/2027) e com o detalhamento do Registro de Visitas (Call Reports) por canal.
- Totalmente editável na própria página: clique em qualquer item para editar nome, datas, responsável e status; adicionar fase/grupo/entrega; excluir. Os responsáveis ainda estão como "A definir" — atualize conforme a equipe for definida.
- Edições ficam salvas no navegador (localStorage). Use os botões **Exportar/Importar** para guardar um backup em `.json` ou levar o planejamento para outro navegador/computador.

---

## Histórico de Atualizações

- **2026-08-04**: Documento criado. Adicionada seção 2 (Modelo de Contas e Hierarquia) a partir da descrição do usuário sobre navegação estilo Salesforce, Grupo Econômico, Cliente Contraparte (raiz/CNPJ) e Segmentação.
- **2026-08-04**: Adicionada seção 5 (Registro de Visitas / Call Reports) como funcionalidade prioritária — 4 canais (Teams, WhatsApp, Telefone, E-mail/Outlook) com identificação automática de cliente.
- **2026-08-04**: Confirmado nome da ferramenta de WhatsApp — **Tuvis** (arquivamento, DLP e integração com CRMs para WhatsApp/Telegram).
- **2026-08-04**: Adicionado fluxo de uso (busca de conta → registro manual ou captura automática) na seção 5.
- **2026-08-04**: Adicionada seção 6 (Arquitetura Técnica — Motor de Captura de Visitas): ingestão por canal, barramento de eventos, matching de cliente, serviço de Visitas, armazenamento e camada de tempo real, dentro das restrições de rede/AWS do Itaú.
- **2026-08-04**: Diagrama da arquitetura de captura de visitas adicionado ao board Figma.
- **2026-08-04**: Adicionada seção 8 — ferramenta interativa de Roteiro de Entregas (visão por entrega e por responsável), publicada como página editável.

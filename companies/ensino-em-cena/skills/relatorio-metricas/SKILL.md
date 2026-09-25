---
name: relatorio-metricas
description: Produz relatórios periódicos consolidando métricas de marketing, vendas, operações e impacto pedagógico
---

# Relatório de Métricas

Use ao gerar relatórios mensais, trimestrais e anuais para a CEO e parceiros.

## Métricas por área

Legenda: **[auto]** = número obtido por ferramenta conectada · **[manual]** = precisa ser levantado e digitado por pessoa (integração ainda pendente ou dado que não vive em sistema).

### Marketing
- Tráfego do site por página de espetáculo/curso — **[auto]** (`mcp__Vercel__aggregate_pageviews`, `mcp__Vercel__count_pageviews`)
- Leads gerados (origem, qualificação) — **[auto]** para leads do site, via evento de formulário (`mcp__Vercel__aggregate_events` agrupando por `eventName` + `utmCampaign`); **[manual]** para leads que chegam por WhatsApp ou indicação
- Alcance e engajamento em redes sociais (Instagram, TikTok) — **[manual]**, não há MCP de redes sociais conectado
- Custo por lead em campanhas pagas — **[manual]**, não há MCP de Meta Ads / Google Ads / LinkedIn Ads conectado; o investimento é digitado à mão e cruzado com os leads **[auto]** do Vercel
- Conversão lead → reunião → proposta — **[manual/auto]**: contagem de propostas vem do Gmail e das issues do Paperclip, a etapa de reunião é registrada manualmente

### Comercial
- Propostas enviadas / fechadas — **[auto]** (labels de funil no Gmail + `mcp__Gmail__search_threads`, cruzado com issues do Paperclip)
- Volume de threads comerciais e tempo de resposta — **[auto]** (`mcp__Gmail__search_threads` + `mcp__Gmail__get_thread`)
- Taxa de conversão por origem e por segmento (público/privado) — **[manual/auto]**: o segmento da escola é classificação manual aplicada sobre os dados **[auto]** do funil
- Ticket médio — **[manual]**, valores vêm das propostas/contratos (podem ser lidos com `mcp__Google_Drive__read_file_content`, mas a consolidação é manual)
- Pipeline (valor em negociação) — **[auto]** a partir das issues do Paperclip, com os valores conferidos manualmente

### Operações
- Apresentações realizadas — **[auto]** (`mcp__Google_Calendar__search_events`, busca por nome da escola / espetáculo / cidade)
- Público total atendido — **[manual]**, o número de alunos por sessão é informado pela escola e registrado na issue
- Incidentes e atrasos — **[manual]**, registrados no relato pós-apresentação
- Custo logístico médio por apresentação — **[manual]**, a partir dos custos lançados pela produção

### Pedagógico
- Escolas com projeto de leitura ativo — **[auto]** a partir das issues do Paperclip
- Materiais entregues vs. previstos — **[auto]** (`mcp__Google_Drive__search_files` na pasta de materiais, conferido contra o previsto na issue)
- Feedback qualitativo de professores — **[auto]** para leitura dos formulários e devolutivas arquivados no Drive (`mcp__Google_Drive__read_file_content`); a síntese é editorial

## Ferramentas e fontes de dados

De onde cada família de métrica realmente vem hoje.

### Tráfego do site e atribuição de leads — Vercel (conectado)

- `mcp__Vercel__count_pageviews` — número macro de visitas do período.
- `mcp__Vercel__aggregate_pageviews` — visitas quebradas por até duas dimensões (`utmCampaign`, `requestPath`, `referrerHostname`, `deviceType`, `country`): mostra qual campanha levou tráfego para qual página de espetáculo ou curso.
- `mcp__Vercel__count_events` / `mcp__Vercel__aggregate_events` — conversões de verdade (envio de formulário de inscrição/contato), agrupadas por `eventName`, `utmCampaign` ou `eventData/<propriedade>`.
- `mcp__Vercel__create_observability_query` — funis de visitantes únicos, com `bucketTimezone` em `America/Sao_Paulo` para que os dias batam com o calendário brasileiro.
- `mcp__Vercel__list_project_domains` — confirmar que `ensinoemcena.com.br` e suas variantes estão realmente atreladas ao projeto antes de confiar nos números.

Limites importantes: os dados são **apenas de produção** e **apenas a partir da data em que o Web Analytics foi ligado** — não há histórico retroativo. As UTMs precisam estar na campanha **antes** do disparo; sem UTM, a atribuição é irrecuperável. E `mcp__Vercel__record_events` é telemetria de cache de build, **não** é analytics de marketing — nunca usar para lead.

### Volume comercial e tempo de resposta — Gmail (conectado)

- `mcp__Gmail__search_threads` — contagem de threads por etapa do funil, usando query no estilo Gmail (`label:Proposta/Enviada newer_than:30d`, `proposta has:attachment newer_than:30d`).
- `mcp__Gmail__get_thread` — leitura da conversa inteira em `PLAIN_TEXT` para medir tempo entre nosso envio e a resposta da escola.
- `mcp__Gmail__list_labels` — recuperar os IDs dos labels do funil antes de qualquer contagem por etapa.
- `mcp__Gmail__list_drafts` — propostas preparadas e ainda não enviadas.

Armadilha obrigatória: `mcp__Gmail__search_threads` devolve preview só das **~5 mensagens mais antigas** de cada thread, sem aviso de truncamento. Qualquer métrica de "último retorno da escola" ou tempo de resposta calculada só pela busca sai errada — é obrigatório chamar `mcp__Gmail__get_thread`.

### Apresentações realizadas — Google Calendar (conectado, somente leitura)

- `mcp__Google_Calendar__search_events` — única ferramenta disponível: busca semântica na agenda **principal**, por nome da escola, cidade ou espetáculo.

Limites: não cria, não altera e não cancela evento; não filtra por intervalo de datas (só termo de busca + paginação); não enxerga agendas secundárias ou compartilhadas. Na prática, a agenda serve para **conferir** a lista de apresentações que o relatório montou a partir das issues, não como fonte única.

### Relatórios arquivados e formulários de feedback — Google Drive (conectado)

- `mcp__Google_Drive__search_files` — localizar o relatório do período anterior, planilhas de custo e formulários de feedback de professores (busca por `fullText contains` e `mimeType`, com recorte por `modifiedTime`).
- `mcp__Google_Drive__list_recent_files` — o que a equipe mexeu na semana.
- `mcp__Google_Drive__read_file_content` — ler o conteúdo (Docs, Sheets, PDF, DOCX, XLSX) com `includeComments` para trazer também os comentários de margem.
- `mcp__Google_Drive__create_file` — publicar o relatório fechado como Google Doc na pasta certa (`parentId`).
- `mcp__Google_Drive__share_file` — liberar o relatório para a CEO e parceiros como `reader` ou `commenter`.

Limites: **não existe ferramenta para editar o conteúdo de um arquivo já criado** — revisar um relatório publicado significa criar nova versão com `mcp__Google_Drive__create_file` e arquivar a antiga com `mcp__Google_Drive__trash_file`. E `mcp__Google_Drive__share_file` só **eleva** permissão: tirar acesso tem que ser feito na interface do Drive. Todo fluxo começa por uma busca — `fileId` nunca pode ser inventado.

### Pipeline e dados operacionais — issues do Paperclip

Propostas em negociação, escolas com projeto ativo, público atendido, incidentes e custos vivem nas issues do Paperclip. É de lá que sai o pipeline e o denominador de quase todo indicador operacional; Gmail, Calendar e Drive servem para **conferir** esses números contra o que aconteceu de fato.

### Ainda não conectado — entrada manual obrigatória

Estas fontes **não têm integração disponível**. Os números existem, mas precisam ser levantados por pessoa e digitados no relatório, sempre com a fonte e a data de coleta anotadas:

- **Redes sociais** (Instagram, TikTok, YouTube) — alcance, seguidores, engajamento, visualizações. Não há MCP de redes sociais conectado. Exportar do painel nativo de cada rede.
- **Gestores de anúncios pagos** (Meta Ads, Google Ads, LinkedIn Ads) — investimento, impressões, cliques e **custo por lead**. Não há MCP de anúncios conectado. O CPL é calculado à mão: investimento digitado ÷ leads, usando os leads **[auto]** do Vercel quando a campanha estava com UTM.
- **WhatsApp** — conversas e leads que chegam por mensagem. Não há MCP de WhatsApp conectado.
- **CRM** — não há CRM conectado; o funil vive em labels do Gmail e nas issues do Paperclip.

Nunca apresentar número de rede social ou CPL como se tivesse vindo de sistema. Marcar explicitamente como coleta manual no painel.

## Estrutura do relatório

1. **Sumário executivo** — 3 a 5 linhas com os números-chave
2. **Destaques** — o que mudou desde o último período
3. **Painel** — tabelas e gráficos por área
4. **Análise** — o que está funcionando, o que não está
5. **Próximas ações** — recomendações ao decisor

## Cadência

- Mensal — operacional, para a CEO
- Trimestral — estratégico, para a CEO + parceiros
- Anual — institucional, para divulgação e prestação de contas

## Princípios

- Todo número tem fonte e data de corte
- Comparar com período anterior e meta
- Insight > planilha: explique o "por quê"
- Marcar no painel o que é **[auto]** e o que é **[manual]** — quem lê precisa saber o grau de confiança de cada linha
- Dado de integração pendente (redes, anúncios, WhatsApp) nunca é apresentado como automático

## Checklist antes de entregar

- [ ] Dados conferidos nas fontes automáticas (Vercel, Gmail, Calendar, Drive, issues do Paperclip)
- [ ] Dados manuais (redes sociais, investimento em anúncios/CPL, público atendido, custos) coletados e com fonte anotada
- [ ] Threads comerciais lidas com `mcp__Gmail__get_thread`, não só pelo preview da busca
- [ ] Domínio confirmado com `mcp__Vercel__list_project_domains` antes de usar os números de tráfego
- [ ] Linhas marcadas como [auto] ou [manual]
- [ ] Período e data de corte explícitos
- [ ] Comparativo com período anterior
- [ ] Próximas ações recomendadas
- [ ] Relatório arquivado no Drive (`mcp__Google_Drive__create_file`) e compartilhado com quem precisa (`mcp__Google_Drive__share_file`)

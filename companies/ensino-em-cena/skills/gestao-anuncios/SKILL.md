---
name: gestao-anuncios
description: Monta o pacote de campanha paga (criativo, copy, segmentação, UTMs) para Meta Ads, Google Ads e LinkedIn e mede o resultado no site — nenhum gerenciador de anúncios está conectado; a veiculação é manual por um operador
---

# Gestão de Anúncios

Use para campanhas pagas voltadas à captação de leads B2B (escolas e secretarias).

> **Nenhum gerenciador de anúncios está integrado.** O agente produz e entrega o pacote de campanha; a veiculação é sempre manual, por um operador humano. Ver **Ferramentas**.

## Plataformas

- **Meta Ads** (Instagram + Facebook) — alcance de coordenadores e diretores — *veiculação manual*
- **Google Ads** — busca por termos como "espetáculo educativo", "projeto de leitura escola" — *veiculação manual*
- **LinkedIn Ads** — secretarias e redes de ensino — *veiculação manual*

## Tipos de campanha

| Tipo | Objetivo | Quando usar |
|------|----------|-------------|
| Alcance/marca | Awareness institucional | Início de ano letivo |
| Geração de leads | Formulário direto | Temporada / lançamentos |
| Tráfego para site | Catálogo / landing | Após lançamento |
| Remarketing | Recuperar visitantes | Continuamente |

## Ferramentas

**Nenhum gerenciador de anúncios está conectado.** Não existe MCP de Meta Ads, Google Ads ou LinkedIn Ads nesta configuração — o agente **não cria, não sobe, não pausa e não escala campanhas**. Tudo que envolve conta de anúncio, verba, pixel e veiculação é feito por um operador humano no gerenciador.

O que o agente realmente faz hoje é **montar o pacote de campanha pronto para upload** e, depois, **ler o tráfego do site** para apoiar a atribuição.

### Criativos (conectado)

| Ferramenta | Uso |
|---|---|
| `mcp__Canva__search-brand-templates` | Localizar o template de peça de anúncio na marca (filtrar `dataset='non_empty'` para templates com autofill) |
| `mcp__Canva__create-design-from-brand-template` | Gerar a peça a partir do template da casa |
| `mcp__Canva__read-design` | Ler a peça (com `open_transaction: true` para obter o `transaction_id` de edição) |
| `mcp__Canva__edit-design` | Trocar headline, data, espetáculo e CTA via `replace_text` / `find_and_replace_text`, ajustar `format_text`, `position_element`, `resize_element` e fechar com `finalize` |
| `mcp__Canva__copy-design` | Derivar variações A/B da mesma peça sem refazer o layout |
| `mcp__Canva__publish-brand-template` | Publicar o layout aprovado como template para as próximas campanhas |
| `mcp__Magnific__images_expand` | Reenquadrar a mesma arte em 1:1 (feed), 9:16 (stories/reels) e 16:9 sem nova sessão de fotos |
| `mcp__Magnific__images_retouch` | Limpar saída de emergência, microfone, equipe e sujeira de cena da foto de divulgação |
| `mcp__Magnific__images_upscale_modes_list` / `images_upscale_presets_list` | Conferir modos e presets antes de ampliar foto de baixa resolução |
| `mcp__Magnific__video_crop` | Cortar o trailer master nas proporções exigidas por cada posicionamento |
| `mcp__Magnific__creations_deliver` | Exportar o arquivo final em formato de entrega para o operador |
| `mcp__Adobe_for_creativity__suggest_type_palettes` / `get_type_palette` / `font_preview` / `font_styles` | Definir e conferir a tipografia do anúncio |
| `mcp__Adobe_for_creativity__font_activate` | Ativar a fonte na Creative Cloud (uma fonte por chamada) |

Limites reais: **não há ferramenta de export no Canva** — a peça sai como link de design/thumbnail, e o arquivo para upload vem pela rota Adobe (`mcp__Adobe_for_creativity__pdf_export`, `pdf_to_image`) ou pelo `creations_deliver` do Freepik. O Freepik também **não lê anexos de chat**: fotos locais entram por `mcp__Magnific__creations_upload_show`. Antes da primeira chamada Adobe, rodar `adobe_mandatory_init`.

### Arquivo dos criativos (conectado)

| Ferramenta | Uso |
|---|---|
| `mcp__Google_Drive__create_file` | Criar a pasta da campanha e salvar briefing, copy e planilha de UTMs (usar `disableConversionToGoogleType` para preservar arquivo original de arte) |
| `mcp__Google_Drive__update_file` | Mover peça para a pasta correta da campanha (só muda título e pasta) |
| `mcp__Google_Drive__search_files` / `list_recent_files` | Achar arte, briefing ou peça de campanha anterior |
| `mcp__Google_Drive__share_file` | Liberar a pasta da campanha para o operador (`writer`) ou para revisão (`commenter`) |
| `mcp__Google_Drive__get_file_permissions` | Auditar quem tem acesso à pasta, sobretudo em peças com imagem de alunos |
| `mcp__Google_Drive__trash_file` | Aposentar versão substituída de criativo |

Limite real: o Drive aqui **não atualiza conteúdo de arquivo existente** — revisão vira `create_file` de nova versão + `trash_file` da antiga. E `share_file` só sobe permissão: **revogar acesso só pela interface do Drive**.

### Entrega e atribuição (conectado)

| Ferramenta | Uso |
|---|---|
| `mcp__Gmail__create_draft` / `update_draft` | Preparar o e-mail de entrega do pacote de campanha ao operador e a aprovação de verba |
| `mcp__Gmail__send_message` | Enviar o pacote com anexos (limite de 25MB somados — acima disso, link do Drive) |
| `mcp__Gmail__search_threads` / `get_thread` | Recuperar a thread de aprovação de verba e o combinado com o Comercial |
| `mcp__Vercel__list_project_domains` | Confirmar que `ensinoemcena.com.br` está atachado e verificado no projeto (**verificar isto antes de planejar qualquer análise**) |
| `mcp__Vercel__aggregate_pageviews` | Tráfego por `utmCampaign` × `requestPath` — qual campanha levou a qual página de espetáculo |
| `mcp__Vercel__aggregate_events` | Conversões por `eventName` × `utmCampaign` — quais campanhas geraram envio de formulário |
| `mcp__Vercel__count_pageviews` / `count_events` | Números de topo da temporada |
| `mcp__Vercel__create_observability_query` | Funil de visitantes únicos com `bucketTimezone: America/Sao_Paulo` |

Limites reais: o Web Analytics da Vercel é **só de produção e só a partir da data em que foi habilitado** — se a UTM não estiver no link **antes** do início da campanha, a atribuição daquele período é irrecuperável. `mcp__Vercel__record_events` é telemetria de cache de build, **não** serve para leads.

### Pendente (não conectado — não prometer)

- **Meta Ads / Google Ads / LinkedIn Ads** — sem MCP. Criação, veiculação, orçamento, pausa e escala: 100% manual no gerenciador.
- **CRM** — sem integração. Leads não entram automaticamente; o repasse ao Comercial é manual.
- **WhatsApp** — sem integração.
- **Agendador de redes sociais (Instagram/TikTok)** — sem integração. Publicação orgânica também é manual.
- **Google Calendar** — somente leitura (`mcp__Google_Calendar__search_events`, calendário principal). Datas de campanha não podem ser criadas pelo agente.

## Fluxo

O entregável do agente é um **pacote de campanha pronto para upload**, entregue a um operador humano. A campanha só existe depois que esse operador sobe tudo no gerenciador.

1. **Briefing** — objetivo, público, verba, KPI (CPL alvo), período e espetáculo/programa em foco
2. **Criativo** — peças a partir do brand kit com `mcp__Canva__create-design-from-brand-template` + `mcp__Canva__edit-design`; foto tratada com `mcp__Magnific__images_retouch`; versões 1:1 / 9:16 / 16:9 com `mcp__Magnific__images_expand`; vídeo reenquadrado com `mcp__Magnific__video_crop`; arquivo final por `mcp__Magnific__creations_deliver` ou `mcp__Adobe_for_creativity__pdf_export`
3. **Copy** — headline, texto primário, descrição e CTA por posicionamento, em variações A/B, salvos com `mcp__Google_Drive__create_file`
4. **Segmentação** — cargos (coordenador, diretor), regiões, interesses e listas, escritos como **especificação** para o operador reproduzir no gerenciador
5. **UTMs** — planilha de links com `utm_source`, `utm_medium`, `utm_campaign` (nome padrão da campanha), `utm_content` (criativo) — gerada antes da subida, senão a Vercel não consegue atribuir
6. **Montagem do pacote** — pasta da campanha no Drive (`mcp__Google_Drive__create_file`), peças movidas com `mcp__Google_Drive__update_file`, acesso liberado por `mcp__Google_Drive__share_file`
7. **Entrega ao operador** — `mcp__Gmail__create_draft` com o pacote (link do Drive + especificação de segmentação + UTMs) para revisão humana, depois `mcp__Gmail__send_message`
8. **Veiculação — PENDENTE (manual)** — sem MCP de gerenciador de anúncios. Subida, verba, pixel, pausa e escala são executados por pessoa no Meta/Google/LinkedIn. O agente não confirma campanha no ar; quem confirma é o operador.
9. **Monitoramento** — o agente acompanha o **lado do site**: `mcp__Vercel__aggregate_pageviews` e `mcp__Vercel__aggregate_events` por `utmCampaign` nos primeiros 7 dias. Métricas de dentro do gerenciador (impressões, CPC, CPL, frequência) precisam ser informadas pelo operador — o agente não as lê.
10. **Otimização** — o agente **recomenda** pausar/escalar com base no tráfego e nas conversões do site; a execução é do operador
11. **Relatório** — consolidar tráfego e conversões por campanha e entregar ao Comercial por `mcp__Gmail__send_message` ou documento no Drive, com UTM e origem. **Repasse manual: sem CRM conectado, nenhum lead entra em sistema automaticamente.**

## Gates

- Verba acima de **R$ 3.000/mês** → **board approval da CEO** (gate `campanha-paga-acima-teto` em `.paperclip.yaml`; requester: marketing, owner: ceo). O gate vale para o plano de mídia mesmo com a veiculação manual.
- Mudança de público-alvo estratégica → alinhar com Comercial

## Princípios

- CPL alvo definido antes de subir a campanha
- Toda campanha tem nome padrão: `EEC_{canal}_{objetivo}_{mes-ano}` — o mesmo nome vai no `utm_campaign`, senão não há como cruzar gerenciador e Vercel
- UTM obrigatório em todo link, **criado antes da campanha entrar no ar** (o Web Analytics da Vercel não reconstrói atribuição retroativa)
- Nenhuma campanha é subida pelo agente; o agente entrega o pacote e a execução é de um operador humano
- Leads são repassados **manualmente** ao Comercial e geram issue no `pipeline-comercial/` — **CRM pendente de integração**

## Checklist antes de subir

- [ ] Verba aprovada
- [ ] Criativos revisados
- [ ] Criativos arquivados na pasta da campanha no Drive e compartilhados com o operador
- [ ] Copy e especificação de segmentação escritas para o operador reproduzir no gerenciador
- [ ] UTM em todos os links (planilha entregue junto com o pacote)
- [ ] CPL alvo definido
- [ ] Pixel e conversões configurados — **manual, pelo operador; o agente não valida**
- [ ] Domínio `ensinoemcena.com.br` confirmado no projeto Vercel (`mcp__Vercel__list_project_domains`)
- [ ] Operador identificado e pacote entregue por e-mail
- [ ] Repasse de leads ao Comercial combinado — **CRM pendente, fluxo manual**

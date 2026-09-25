---
name: divulgacao-espetaculo
description: Produz peças de divulgação (posts, vídeos, releases) para espetáculos e projetos da Ensino em Cena
---

# Divulgação de Espetáculo

Use esta skill ao montar campanhas de divulgação para temporadas, lançamentos e captação de escolas.

## Peças padrão por lançamento

- **Post quadrado** (Instagram feed) — título, faixa etária, chamada
- **Stories** (3 a 5) — bastidores, depoimentos, CTA para agendamento
- **Reel curto** (15-30s) — trecho do espetáculo + voz off com sinopse
- **Release** (1 página) — para escolas/imprensa, com sinopse, ficha técnica e contato
- **Landing/seção do site** — sinopse completa, fotos, ficha, formulário de interesse

## Princípios

- Toda peça precisa de CTA claro (agendar, baixar release, falar com Comercial)
- Linguagem voltada à coordenação pedagógica, não ao aluno final
- Usar fotos oficiais do espetáculo (não mockups) sempre que disponíveis
- Manter identidade visual da Ensino em Cena (cores, tipografia)

## Ferramentas

### Canva — peças padrão (post, stories, reel, release, landing)

O layout de cada peça padrão vive como brand template, e cada lançamento sai de uma cópia, não de um arquivo novo.

- `mcp__Canva__search-brand-templates` — localizar o template da peça (filtrar `dataset: 'non_empty'` para os que aceitam autofill)
- `mcp__Canva__get-brand-template-dataset` — ver quais campos o template expõe (título, faixa etária, data, local)
- `mcp__Canva__create-design-from-brand-template` — abrir a peça do lançamento a partir do template (use `page_numbers` para puxar só as páginas necessárias em release e encarte)
- `mcp__Canva__read-design` com `open_transaction: true` seguido de `mcp__Canva__edit-design` — trocar textos (`replace_text`, `find_and_replace_text`), ajustar tipografia (`format_text`), imagens (`update_fill`), e páginas de stories/encarte (`add_page`, `reorder_page`); `finalize` faz o commit
- `mcp__Canva__copy-design` — derivar variações (ex.: versão para escola privada e versão para rede pública) sem refazer
- `mcp__Canva__publish-brand-template` — quando um layout novo virar padrão da casa, publicá-lo para os próximos lançamentos

Limites reais: **não há ferramenta de exportação no Canva aqui** — a entrega sai como link do design mais thumbnails de página. Para PDF do release, passar o conteúdo pelo Adobe (`mcp__Adobe_for_creativity__markdown_to_pdf` ou `mcp__Adobe_for_creativity__pdf_create`). Também não há upload de asset: foto nova de produção ainda precisa entrar pela interface do Canva.

### Freepik/Magnific — imagem e vídeo

- `mcp__Magnific__images_retouch` — limpar saída de emergência, cabo, microfone ou equipe de foto de espetáculo
- `mcp__Magnific__images_expand` — uma foto-chave vira 1:1 (feed), 9:16 (stories/reel) e 21:9 (topo da landing) sem novo ensaio
- `mcp__Magnific__images_upscale_modes_list` e `mcp__Magnific__images_upscale_presets_list` — conferir modo e preset antes de ampliar foto de ensaio para impressão
- `mcp__Magnific__video_magic_cut`, `mcp__Magnific__video_extend`, `mcp__Magnific__video_crop` — montar o reel a partir de trechos, ajustar duração e cortar o master em Reels/YouTube/telão do saguão
- `mcp__Magnific__video_color_grade` — casar imagem de celular com a luz de palco
- `mcp__Magnific__video_music` (com `mixMode: lower`) e `mcp__Magnific__video_soundfx` — trilha e efeitos sob a voz off da sinopse
- `mcp__Magnific__audio_tts_direction` antes de escrever a voz off; `mcp__Magnific__audio_isolate` para salvar depoimento gravado em saguão barulhento; `mcp__Magnific__video_audio_mix` para casar áudio aprovado com a imagem
- Acessibilidade: `mcp__Magnific__video_dubbing` (ou o fluxo revisado `video_dubbing_preview` → `video_dubbing_preview_get` → `video_dubbing_confirm`) para legenda em português em todo vídeo publicado — **limite de 120s por vídeo**, então vídeo longo precisa ser fatiado
- `mcp__Magnific__creations_deliver` — entregar master para o editor/designer; `mcp__Magnific__flows_create` — salvar o pipeline "foto-chave → kit de redes" e reusar a cada montagem

Atenção: `video_vfx`, `video_color_grade` e `video_magic_cut` exigem plano pago (e os dois primeiros limitam a 20s/200MB). Arquivo local só entra via `mcp__Magnific__creations_upload_show`.

### Adobe Fonts — consistência tipográfica com o brand kit

- `mcp__Adobe_for_creativity__suggest_type_palettes` → `mcp__Adobe_for_creativity__get_type_palette` — fechar a paleta tipográfica da campanha (títulos, apoio, corpo, legenda)
- `mcp__Adobe_for_creativity__font_search` com `writing_systems: 'latn'` — garantir suporte a acentuação e cedilha do português
- `mcp__Adobe_for_creativity__font_preview` e `mcp__Adobe_for_creativity__font_styles` — validar o desenho com a Direção e conferir se a família tem os pesos usados nas peças
- `mcp__Adobe_for_creativity__font_activate` (uma fonte por chamada) — liberar a fonte nos apps Adobe; `mcp__Adobe_for_creativity__font_create_kit` — embutir no site/landing
- Importante: `font_activate` **não** instala fonte no sistema nem dentro do Canva — a tipografia do Canva continua sendo configurada no Brand Kit da própria plataforma
- O servidor Adobe exige uma chamada de init antes do primeiro uso das ferramentas dele

### Google Drive — arquivo dos finais

- `mcp__Google_Drive__create_file` com `mimeType: application/vnd.google-apps.folder` — pasta por espetáculo/temporada (Posts / Stories / Reels / Release / Fotos)
- `mcp__Google_Drive__create_file` — subir o release final e o texto das peças; usar `disableConversionToGoogleType` para preservar PDF ou foto original
- `mcp__Google_Drive__search_files` / `mcp__Google_Drive__list_recent_files` — achar arte ou release de temporada anterior (busca por `fullText contains`, não só por nome)
- `mcp__Google_Drive__update_file` — mover arquivo solto para a pasta certa (só muda título e pasta)
- `mcp__Google_Drive__share_file` — liberar o kit para escola ou veículo (`reader` para ficha técnica, `commenter` quando quiser retorno)
- `mcp__Google_Drive__trash_file` — aposentar versão superada

Atenção: **não existe ferramenta para editar o conteúdo de um arquivo já no Drive** — revisão gera arquivo novo. E `share_file` só sobe permissão: revogar acesso (relevante em foto de aluno) só pela interface do Drive.

### Gmail — envio do release para imprensa e escolas

- `mcp__Gmail__create_draft` + revisão humana antes de `mcp__Gmail__send_message` — padrão obrigatório para disparo frio; volume alto de e-mail queima a reputação do domínio
- Release de imprensa: `mcp__Gmail__send_message` com `bcc` para a lista de jornalistas (preserva a privacidade da lista), `htmlBody` formatado e `attachments` com fotos de divulgação — teto de 25MB somados, acima disso subir no Drive e mandar o link
- `mcp__Gmail__update_draft` para iterar o texto — **reenviar os anexos**, senão o PDF do release some silenciosamente
- `mcp__Gmail__create_label` + `mcp__Gmail__label_thread` — funil por etapa (`Imprensa/Retorno`, `Proposta/Enviada`); `mcp__Gmail__update_message_labels` move de etapa numa única chamada
- `mcp__Gmail__search_threads` para achar contatos e conferir retorno, sempre seguido de `mcp__Gmail__get_thread` antes de responder — a busca só mostra as ~5 mensagens **mais antigas** da thread
- `mcp__Gmail__reply` no `messageId` da última mensagem (mantém o encadeamento) e `mcp__Gmail__forward` para reaproveitar o release com outra escola ou mantenedora
- `mcp__Gmail__unmark_thread_spam` — resgatar resposta de escola que caiu em spam durante campanha

Sem envio em lote, sem mala direta e sem agendamento: cada destinatário é uma chamada, e o disparo é sempre imediato.

### Medição da campanha (site institucional)

- `mcp__Vercel__list_project_domains` — confirmar que o domínio da landing está atrelado e verificado antes da campanha
- `mcp__Vercel__create_deployment` — publicar a seção da temporada (ou voltar a um deploy anterior no meio da campanha)
- `mcp__Vercel__aggregate_pageviews` por `['utmCampaign','requestPath']` — qual campanha levou a qual página de espetáculo
- `mcp__Vercel__aggregate_events` por `['eventName','utmCampaign']` — quais campanhas viraram formulário preenchido
- `mcp__Vercel__create_observability_query` com `bucketTimezone: America/Sao_Paulo` — funil de visitantes únicos no calendário brasileiro

Atenção: as UTMs precisam estar no ar **antes** do lançamento — atribuição não é recuperável depois.

### Agenda

- `mcp__Google_Calendar__search_events` — **somente leitura**, e só na agenda principal. Serve para conferir se a data da temporada já está na agenda; criar ou mover data de sessão precisa ser feito à mão por uma pessoa.

### Ainda pendente (não conectado)

Não prometer nem planejar como se funcionasse:

- **WhatsApp** — sem integração; contato com coordenação por WhatsApp continua manual
- **CRM** — sem integração; o funil vive em labels do Gmail, não em CRM
- **Agendador de redes sociais (Instagram/TikTok)** — sem publicação automática; a peça sai pronta do Canva/Freepik e alguém posta manualmente
- **Gerenciadores de mídia paga (Meta Ads / Google Ads / LinkedIn Ads)** — sem integração; impulsionamento é operado fora daqui

## Checklist antes de publicar

- [ ] Ficha técnica conferida com Diretor Artístico
- [ ] Texto revisado pelo Produtor de Conteúdo
- [ ] Tipografia da campanha fechada e ativada (`suggest_type_palettes` → `get_type_palette` → `font_activate`), com acentuação conferida
- [ ] Peças geradas a partir do brand template (`create-design-from-brand-template` + `edit-design`), não de arquivo avulso
- [ ] Fotos tratadas e reenquadradas por formato (`images_retouch`, `images_expand`)
- [ ] Vídeo legendado em português (`video_dubbing`; vídeo acima de 120s fatiado antes)
- [ ] Release em PDF gerado (`markdown_to_pdf` / `pdf_create`) — o Canva não exporta aqui
- [ ] CTA + link de captura funcionando
- [ ] UTMs em links para mensurar origem dos leads — **no ar antes do lançamento**, conferir depois com `aggregate_pageviews` / `aggregate_events`
- [ ] Landing publicada e domínio verificado (`list_project_domains`, `create_deployment`)
- [ ] Release e artes finais arquivados na pasta do espetáculo no Drive (`create_file`, `update_file`)
- [ ] Envio à imprensa e às escolas preparado em rascunho (`create_draft`) e revisado por uma pessoa antes do `send_message`; anexos dentro dos 25MB
- [ ] Threads etiquetadas no funil do Gmail (`create_label`, `label_thread`)
- [ ] Calendário de postagem alinhado com a temporada — datas conferidas com `search_events` (leitura); lançamento nas redes ainda é manual, sem agendador conectado

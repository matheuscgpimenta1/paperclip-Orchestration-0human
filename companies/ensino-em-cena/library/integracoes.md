# Integrações — Mapa de Ferramentas Reais

Este é o arquivo que um agente lê para saber **o que ele realmente consegue fazer**. Só entra aqui ferramenta com servidor MCP acessível e nome verificado.

## Princípios

- **Nome de ferramenta não se inventa.** Se o nome `mcp__*` não está nesta página, não existe para a empresa.
- **Live ≠ credencial confirmada.** Os servidores abaixo responderam e tiveram schema carregado; a autenticação da conta só se prova na primeira chamada real. Se der erro de autorização, reporte à CEO — não contorne.
- **Pendente é pendente.** Nada de descrever como feito o que ainda é manual. A seção [Pendentes](#pendentes) é a lista honesta.
- **Limite documentado vale mais que promessa.** Cada seção lista as armadilhas conhecidas; ignorá-las produz entrega errada silenciosamente.

## Quadro geral

| Integração | Servidor MCP | Status | Agentes plugados | Serve para |
|-----------|--------------|--------|------------------|-----------|
| E-mail | `mcp__Gmail__*` (30 ferramentas) | ✅ live | CEO, Comercial, Marketing, Operações | Prospecção, propostas, logística, release de imprensa |
| Arquivos e documentos | `mcp__Google_Drive__*` (11 ferramentas) | ✅ live | Todos | Roteiros, contratos, fichas técnicas, guias, fotos |
| Agenda | `mcp__Google_Calendar__*` (1 ferramenta) | ✅ live — **somente leitura** | CEO, Operações, Comercial | Conferir turnê, ensaios, reuniões |
| Design de peças | `mcp__Canva__*` (9 ferramentas) | ✅ live — **sem exportação** | Marketing, Produtor Conteúdo, Diretor Artístico | Posts, encartes, apostilas, propostas |
| Tipografia e PDF | `mcp__Adobe_for_creativity__*` | ✅ live | Marketing, Produtor Conteúdo | Brand kit tipográfico, PDF impresso |
| Imagem, vídeo, áudio, legenda | `mcp__Magnific__*` | ✅ live | Marketing, Diretor Artístico | Key art, trailer, trilha, SFX, acessibilidade |
| Site e analytics | `mcp__Vercel__*` | ✅ live | Marketing, CEO | `ensinoemcena.com.br`, deploy, atribuição de lead |
| WhatsApp | — | 🟡 pendente | Comercial, Operações | Lembrete D-2, contato rápido com coordenação |
| CRM | — | 🟡 pendente | Comercial, Marketing, CEO | Pipeline durável de escolas |
| Agendador de redes sociais | — | 🟡 pendente | Marketing | Publicação automática Instagram/TikTok |
| Gerenciador de anúncios | — | 🟡 pendente | Marketing | Meta Ads, Google Ads, LinkedIn Ads |

---

## Gmail — `mcp__Gmail__*`

**Status:** ✅ live · 30 ferramentas · Agentes: CEO, Comercial, Marketing, Operações

### O que existe

**Envio e redação**
`mcp__Gmail__send_message` · `mcp__Gmail__create_draft` · `mcp__Gmail__update_draft` · `mcp__Gmail__get_draft` · `mcp__Gmail__list_drafts` · `mcp__Gmail__delete_draft` · `mcp__Gmail__reply` · `mcp__Gmail__forward`

**Leitura**
`mcp__Gmail__search_threads` · `mcp__Gmail__get_thread` · `mcp__Gmail__get_message`

**Organização (funil por label)**
`mcp__Gmail__list_labels` · `mcp__Gmail__create_label` · `mcp__Gmail__update_label` · `mcp__Gmail__delete_label` · `mcp__Gmail__label_message` · `mcp__Gmail__label_thread` · `mcp__Gmail__unlabel_message` · `mcp__Gmail__unlabel_thread` · `mcp__Gmail__update_message_labels`

**Higiene de caixa**
`mcp__Gmail__trash_message` · `mcp__Gmail__trash_thread` · `mcp__Gmail__untrash_message` · `mcp__Gmail__untrash_thread` · `mcp__Gmail__mark_message_spam` · `mcp__Gmail__mark_thread_spam` · `mcp__Gmail__unmark_message_spam` · `mcp__Gmail__unmark_thread_spam` · `mcp__Gmail__apply_sensitive_message_label` · `mcp__Gmail__apply_sensitive_thread_label`

### Fluxos da empresa

**Prospecção de escolas** (skill `prospeccao-escolas`, agente Comercial)
1. `mcp__Gmail__search_threads` com `escola OR colégio OR coordenação newer_than:180d` para achar contato já existente antes de tratar a escola como lead frio.
2. `mcp__Gmail__create_draft` por escola, personalizado ao projeto político-pedagógico.
3. **Revisão humana antes do disparo** — regra fixa, não opcional (ver armadilhas).
4. `mcp__Gmail__send_message` com o texto final.
5. Funil por label: `mcp__Gmail__create_label` cria `Prospecção/Contato-inicial`, `Prospecção/Follow-up-1`, `Prospecção/Sem-resposta`; `mcp__Gmail__label_thread` aplica; `mcp__Gmail__update_message_labels` move de estágio em uma chamada atômica (adiciona e remove junto).

**Follow-up sem soar automático**
`mcp__Gmail__search_threads` com `from:<escola> newer_than:14d` → `mcp__Gmail__get_thread` em `PLAIN_TEXT` para ler a conversa inteira → `mcp__Gmail__reply` no `messageId` da **última** mensagem, mantendo o encadeamento em vez de abrir thread nova.

**Proposta comercial** (skill `orcamento-proposta`)
`mcp__Gmail__create_draft` com `htmlBody` (sinopse, faixa etária, duração, valores) + `attachments` com PDF de apresentação e ficha técnica. `mcp__Gmail__update_draft` itera o texto; `mcp__Gmail__send_message` dispara; `mcp__Gmail__forward` reaproveita a mesma proposta para a mantenedora ou a diretoria. Acima de 25MB somados, subir no Drive e mandar link.

**Pipeline visível sem CRM**
`mcp__Gmail__list_drafts` mostra o que ainda não saiu; `mcp__Gmail__search_threads` com `proposta has:attachment newer_than:30d` mostra o que já saiu; labels `Proposta/Enviada` e `Proposta/Aprovada` dão o estágio. É paliativo enquanto o CRM não entra.

**Logística com a escola** (skill `logistica-turne`, agente Operações)
`mcp__Gmail__get_thread` para recuperar o combinado e o `messageId` → `mcp__Gmail__reply` com `replyAll=true` confirmando data, horário das sessões, número de alunos, espaço (quadra/auditório), ponto de energia, tempo de montagem e desmontagem. Responder a todos mantém coordenação, secretaria e produção na mesma conversa. Depois `mcp__Gmail__forward` a thread para elenco, técnico de som e transporte com resumo operacional em `forwardText`, e `mcp__Gmail__label_thread` com `Logística/<mês>`.

**Release de imprensa** (skill `divulgacao-espetaculo`, agente Marketing)
`mcp__Gmail__create_draft` para validação da direção → `mcp__Gmail__send_message` com a lista de jornalistas em **bcc** (preserva a privacidade da lista), `htmlBody` formatado e fotos de divulgação em `attachments`. Retorno: `mcp__Gmail__create_label` `Imprensa/Retorno` + `mcp__Gmail__label_thread` nas respostas, com `mcp__Gmail__search_threads` filtrando `from:(jornal OR redação OR pauta) newer_than:30d`.

**Resgate de resposta perdida**
Campanha de e-mail frio derruba retorno em spam. `mcp__Gmail__unmark_thread_spam` resgata a resposta da escola; `mcp__Gmail__untrash_thread` desfaz descarte equivocado.

### Armadilhas e limites

- ⚠️ **`search_threads` só mostra as ~5 mensagens MAIS ANTIGAS de cada thread, sem marcador de truncamento.** Responder sobre "o retorno mais recente da escola" a partir da busca dá resposta errada. É **obrigatório** chamar `mcp__Gmail__get_thread`.
- ⚠️ **`update_draft` remove anexos que não forem reenviados.** Ajustar só o texto do rascunho apaga o PDF da proposta em silêncio.
- Não há **envio em lote nem mala direta**: uma chamada `send_message` por escola. Campanha grande é lenta e aumenta risco de reputação ruim do domínio.
- Não há **agendamento de envio** nem filtros automáticos: o disparo é sempre imediato.
- Não há **leitura de contatos** (People API). A base de escolas vem de planilha no Drive ou do histórico via `search_threads`.
- Operações em massa de lixeira/spam não existem — é uma thread por vez.
- **Regra de fluxo:** prospecção e release sempre `create_draft` → revisão humana → `send_message`. Nunca disparo direto em volume.

---

## Google Drive — `mcp__Google_Drive__*`

**Status:** ✅ live · 11 ferramentas · Agentes: todos

### O que existe

**Descoberta (passo obrigatório)** — `mcp__Google_Drive__search_files` · `mcp__Google_Drive__list_recent_files` · `mcp__Google_Drive__get_file_metadata`
**Leitura** — `mcp__Google_Drive__read_file_content` · `mcp__Google_Drive__download_file_content`
**Escrita e organização** — `mcp__Google_Drive__create_file` · `mcp__Google_Drive__update_file` · `mcp__Google_Drive__copy_file` · `mcp__Google_Drive__trash_file`
**Acesso** — `mcp__Google_Drive__share_file` · `mcp__Google_Drive__get_file_permissions`

### Fluxos da empresa

**Biblioteca central** — `mcp__Google_Drive__create_file` com mimeType `application/vnd.google-apps.folder` monta a árvore (Roteiros / Contratos / Fichas Técnicas / Guias do Professor / Fotos); `mcp__Google_Drive__update_file` com novo `parentId` move o que está espalhado.

**Achar roteiro pelo conteúdo, não pelo nome** — `mcp__Google_Drive__search_files` com `fullText contains '<fala ou personagem>'` + `mimeType = 'application/vnd.google-apps.document'`, recortado por `modifiedTime` da temporada.

**Ler para resumir, adaptar ou decupar** — `search_files` para obter o `fileId` real → `mcp__Google_Drive__read_file_content` com `includeComments: true`, que traz na mesma passada as notas de margem da direção ou da professora.

**Ficha técnica por montagem** — manter um rider mestre e `mcp__Google_Drive__copy_file` com novo título e `parentId` por produção/espaço, em vez de reescrever.

**Contratos** (projeto `contratos-ativos`) — `read_file_content` sobre PDF/DOCX extrai datas, cachês e cláusulas; `mcp__Google_Drive__get_file_permissions` audita quem enxerga o contrato assinado; `mcp__Google_Drive__share_file` libera como `reader` ou `commenter`.

**Distribuição controlada** — guia do professor como `commenter` (feedback sem edição); ficha técnica para técnico do espaço como `reader`.

**Versão de uma data específica** — `mcp__Google_Drive__download_file_content` com `revisionId` explícito recupera o roteiro ou o rider vigente na data da assinatura ou da apresentação.

**Fotos de evento** — `search_files` com `mimeType contains 'image/'` e recorte por `createdTime`; `read_file_content` funciona em PNG/JPEG, então dá para descrever imagem para legenda e texto alternativo.

**Higiene semanal** — `mcp__Google_Drive__list_recent_files` com `orderBy lastModified` mostra o que a empresa mexeu; `mcp__Google_Drive__trash_file` aposenta rascunho superado (recuperável).

### Armadilhas e limites

- ⚠️ **Não existe ferramenta que edite o CONTEÚDO de arquivo existente.** `update_file` muda só título e pasta. Revisar roteiro no lugar é impossível: o contorno é `create_file` de uma nova versão + `trash_file` na antiga — e isso **quebra o histórico de revisões nativo do Drive**. Avise antes de fazer.
- ⚠️ **`share_file` só sobe permissão, nunca desce, e não há revogação.** Crítico para contrato assinado e foto de aluno menor de idade: retirar acesso é trabalho humano na interface do Drive.
- `download_file_content` lê um `revisionId`, mas **nada aqui lista revisões** — só serve se o ID já for conhecido.
- Não dá para **escrever ou responder comentário**; o loop de feedback do guia do professor é só de leitura por aqui.
- `create_file` **converte para formato Google por padrão** — use `disableConversionToGoogleType` para preservar PDF de contrato assinado ou foto original.
- `read_file_content` rejeita `fileId` inventado por design: **todo fluxo começa em `search_files` ou `list_recent_files`**.
- `search_files` não tem termo para lixeira ou favoritos, e `mimeType` precisa ser cláusula própria (nunca palavra dentro de `title`/`fullText`). `parentId = 'root'` é o Meu Drive; drives compartilhados não são endereçáveis.

---

## Google Calendar — `mcp__Google_Calendar__search_events`

**Status:** ✅ live · **1 ferramenta, somente leitura** · Agentes: CEO, Operações, Comercial

### O que existe

`mcp__Google_Calendar__search_events` — busca semântica por texto na agenda **primária**, com `pageSize` e `pageToken`. É a **única** ferramenta do servidor.

### ⚠️ Limitação central

**Não existe criação, alteração nem exclusão de evento.** Nenhuma. Data fechada com escola é lançada no Calendar por uma pessoa; o agente só **confere** o que entrou.

Também não há: seleção de calendário (só o primário), consulta de disponibilidade/free-busy, busca por ID, filtro por intervalo de datas, tratamento de convidados ou de recorrência.

### Como usar mesmo assim

- **Agenda de turnê** (projeto `agenda-turne`, skill `logistica-turne`): manter a agenda no Paperclip/Drive como fonte de trabalho e usar `mcp__Google_Calendar__search_events` com nome da escola, cidade ou "apresentação" para **reconciliar** — o que está no Calendar bate com o que foi confirmado?
- **Ensaios e reuniões**: dá para localizar série existente pelo nome. Marcar, remarcar ou estender é humano.
- **Detecção de conflito é fraca**: sem free-busy, o máximo é buscar termos prováveis e ler o que volta. Não afirme "a data está livre" com base nisso.
- Toda adição à agenda vira **pedido explícito à CEO ou à pessoa responsável**, com data, horário, escola e endereço prontos para colar.

---

## Canva — `mcp__Canva__*`

**Status:** ✅ live · 9 ferramentas · Agentes: Marketing, Produtor de Conteúdo, Diretor Artístico

### O que existe

**Templates de marca** — `mcp__Canva__search-brand-templates` · `mcp__Canva__get-brand-template-dataset` · `mcp__Canva__create-design-from-brand-template` · `mcp__Canva__create-brand-template-draft` · `mcp__Canva__publish-brand-template`
**Design** — `mcp__Canva__copy-design` · `mcp__Canva__read-design` · `mcp__Canva__edit-design` · `mcp__Canva__get-design-dataset`

### Padrão de edição (decore este)

`mcp__Canva__read-design` com `open_transaction: true` devolve o `transaction_id` e a árvore de elementos com `locator_ids` → `mcp__Canva__edit-design` aplica as operações → `finalize` confirma (ou cancela).

Operações disponíveis em `edit-design`: `replace_text`, `find_and_replace_text`, `add_text`, `format_text` (cor, corpo, peso, itálico, sublinhado, entrelinha, alinhamento, links, níveis e marcadores de lista), `update_fill`, `insert_fill`, `insert_shape`, `replace_shape`, `delete_element`, `position_element`, `resize_element`, `rotate_element`, `layer_element`, `recolor_element`, `update_opacity`, `group_elements`/`ungroup_elements`, `crop_media`, `flip_media`, `add_page`, `reorder_page`, `replace_speaker_notes`, `update_title`, `update_text_anchoring`, `update_stroke_properties`, `update_line_properties`, `update_autofill_field`.

### Fluxos da empresa

**Post de divulgação** (skill `gestao-redes-sociais`) — `search-brand-templates` → `create-design-from-brand-template` → `read-design(open_transaction: true)` → `edit-design` com `replace_text` (nome do espetáculo, data, local), `update_fill` (key art) e `format_text` → `finalize`. Montado o layout recorrente uma vez, `mcp__Canva__publish-brand-template` faz todo post futuro nascer dentro da marca.

**Encarte de espetáculo** (`library/templates/encarte.md`) — trabalho multipágina é nativo: `create-design-from-brand-template` com `page_numbers` puxa só as páginas necessárias; `add_page` e `reorder_page` montam elenco, sinopse e patrocinadores; `read-design` com `filter.page_indices` mantém barato inspecionar encarte grande.

**Apostila do aluno e guia do professor** — marcar o mestre com `update_autofill_field` via `create-brand-template-draft` + `edit-design`, publicar com `publish-brand-template`, ler o schema com `mcp__Canva__get-brand-template-dataset` e gerar uma apostila por módulo/turma. Exercícios saem de `add_text`, `insert_shape` (caixas de resposta) e `format_text` com `list_level`/`list_marker`. Guia do professor: `mcp__Canva__copy-design` bifurca a edição do aluno e `replace_speaker_notes` carrega as notas de mediação.

**Proposta comercial** — `search-brand-templates` com `design_types: ['presentation']` e `dataset: 'non_empty'` acha o deck; `get-brand-template-dataset` expõe os campos de cliente/valor/data; a versão por escola sai de `create-design-from-brand-template` + `edit-design` com `find_and_replace_text`.

### Armadilhas e limites

- ⚠️ **Não existe ferramenta de exportação.** Nada de PDF, PNG ou PPTX pelo Canva. O entregável é **link do design** + miniaturas de página do `read-design`. Para PDF impresso, seguir pelas ferramentas de PDF do servidor Adobe.
- ⚠️ **Não há `autofill-design`** nesta sessão. Geração em lote (uma proposta por escola, uma apostila por turma) é **loop** de `create-design-from-brand-template` + `edit-design`, não uma chamada só.
- **Não há upload de asset.** `update_fill`/`insert_fill` exigem `asset_id` já existente no Canva; foto nova de produção entra pela interface do Canva por uma pessoa.
- `update_autofill_field` só funciona em design de página fixa, não em página responsiva (que também restringe as operações disponíveis).
- Descrições do próprio Canva citam ferramentas que **não existem aqui**: `autofill-design`, `search-designs`, `get-design`, `upload-asset`, `start-editing-transaction`, `perform-editing-operations`, `commit-editing-transaction`. Ignore.
- Tipografia do Canva se ajusta no **Brand Kit do Canva** — fonte ativada na Adobe não aparece aqui.

---

## Adobe for Creativity — `mcp__Adobe_for_creativity__*`

**Status:** ✅ live · Agentes: Marketing, Produtor de Conteúdo

### ⚠️ Antes de qualquer chamada

O servidor Adobe exige uma chamada de inicialização obrigatória (`adobe_mandatory_init`) **uma vez por sessão**, antes de qualquer outra ferramenta Adobe — mesmo quando o pedido mapeia direto para uma ferramenta específica.

### Tipografia (schemas verificados)

`mcp__Adobe_for_creativity__font_search` · `mcp__Adobe_for_creativity__font_details` · `mcp__Adobe_for_creativity__font_preview` · `mcp__Adobe_for_creativity__font_styles` · `mcp__Adobe_for_creativity__font_activate` · `mcp__Adobe_for_creativity__font_create_kit` · `mcp__Adobe_for_creativity__suggest_type_palettes` · `mcp__Adobe_for_creativity__get_type_palette`

**Fluxo do brand kit tipográfico** (`library/brand-kit/identidade-visual.md`):
1. `suggest_type_palettes` com intenções do tipo "theatrical", "warm", "editorial" e `primary_style_count` para os slots (título, subtítulo, texto, legenda) — devolve até 5 paletas candidatas.
2. `get_type_palette` no `palette_id` escolhido traz a especificação completa: PostScript name por slot, categoria, uso observado, entreletras.
3. `font_preview` mostra à direção como a fonte realmente é.
4. `font_styles` confirma se a família tem os pesos que a apostila exige.
5. `font_activate` libera a fonte nos apps Adobe — **uma fonte por chamada, sem lote**.
6. `font_create_kit` publica o kit web (CSS/JS) para o site, até 10 fontes por lista de domínios.

Para copy em português, `font_search` com `writing_systems: 'latn'` garante acentuação e cedilha.

### PDF

O servidor expõe a família de PDF, que é **a rota disponível para entregável impresso** já que o Canva não exporta: `mcp__Adobe_for_creativity__pdf_create` · `mcp__Adobe_for_creativity__markdown_to_pdf` · `mcp__Adobe_for_creativity__pdf_combine` · `mcp__Adobe_for_creativity__pdf_split` · `mcp__Adobe_for_creativity__pdf_compress` · `mcp__Adobe_for_creativity__pdf_export` · `mcp__Adobe_for_creativity__pdf_to_image` · `mcp__Adobe_for_creativity__pdf_to_markdown` · `mcp__Adobe_for_creativity__pdf_ocr` · `mcp__Adobe_for_creativity__pdf_redact` · `mcp__Adobe_for_creativity__pdf_page_organize` · `mcp__Adobe_for_creativity__pdf_reorder_pages` · `mcp__Adobe_for_creativity__pdf_rotate_pages` · `mcp__Adobe_for_creativity__pdf_delete_pages` · `mcp__Adobe_for_creativity__pdf_highlight` · `mcp__Adobe_for_creativity__pdf_properties` · `mcp__Adobe_for_creativity__pdf_viewer` · `mcp__Adobe_for_creativity__pdf_edit_ui` · `mcp__Adobe_for_creativity__pdf_operation_status`

Usos diretos: `markdown_to_pdf` transforma proposta ou guia do professor escrito em Markdown em PDF anexável no Gmail; `pdf_combine` junta proposta + ficha técnica + plano pedagógico num só arquivo; `pdf_compress` cabe nos 25MB do Gmail; `pdf_ocr` torna pesquisável contrato escaneado; `pdf_redact` tarja dado pessoal antes de compartilhar.

### Armadilhas e limites

- ⚠️ Esquecer o init obrigatório faz as chamadas Adobe falharem.
- `font_activate` dá acesso **dentro dos apps Adobe** — não instala fonte no sistema nem no Canva, e exige autenticação e direito de uso.
- `font_create_kit` é tudo-ou-nada: uma fonte sem direito de uso derruba o kit inteiro.
- `suggest_type_palettes` e `get_type_palette` usam `sessionId` para encadear as duas chamadas.
- As ferramentas de PDF tiveram os **nomes** verificados, não os schemas — confira os parâmetros na primeira chamada real antes de prometer o formato do entregável.

---

## Freepik / Magnific — `mcp__Magnific__*`

**Status:** ✅ live · Agentes: Marketing, Diretor Artístico

### Imagem

`mcp__Magnific__images_expand` (reenquadra por outpaint para 1:1, 16:9, 9:16, 4:3, 3:4, 3:2, 2:3, 21:9) · `mcp__Magnific__images_retouch` (inpaint/apagar por máscara) · `mcp__Magnific__retouch_models_list` · `mcp__Magnific__images_upscale_modes_list` · `mcp__Magnific__images_upscale_presets_list` · `mcp__Magnific__images_models_settings` · `mcp__Magnific__images_pbr_maps` (mapas de material — uso nichado em previsualização de cenografia 3D)

**Kit de divulgação a partir de uma foto:** `images_retouch` apaga saída de emergência, petaca de microfone e equipe ao fundo da foto de cena → `images_upscale_modes_list`/`images_upscale_presets_list` definem o modo e o preset da casa antes de ampliar para impressão → `images_expand` transforma o mesmo frame em 9:16 para story, 1:1 para feed e 21:9 para o hero do site, sem refazer a foto. `mcp__Magnific__flows_create` engarrafa a cadeia inteira como fluxo reutilizável por montagem.

### Vídeo

`mcp__Magnific__video_magic_cut` (junta fragmentos gerando os quadros da emenda — plano pago) · `mcp__Magnific__video_extend` · `mcp__Magnific__video_crop` (reenquadra sem tarja preta) · `mcp__Magnific__video_color_grade` (LUT de catálogo + ajustes manuais — máx. 20s/200MB, plano pago) · `mcp__Magnific__video_vfx` + `mcp__Magnific__video_vfx_list` (máx. 20s/200MB, plano pago) · `mcp__Magnific__video_music` · `mcp__Magnific__video_soundfx` · `mcp__Magnific__video_audio_mix` (gratuito, não gera nada) · `mcp__Magnific__video_speak_models_list`

**Trailer de espetáculo:** `video_magic_cut` monta a partir de fragmentos de ensaio → `video_extend` estica até a duração exigida pelo slot → `video_color_grade` aproxima o material de celular da paleta de luz do palco → `video_crop` corta o master único em Reels, YouTube e telão do hall. Trilha com `video_music` usando `mixMode: 'lower'` para a música abaixar sob a fala dos atores; `video_audio_mix` assenta a locução aprovada sobre a imagem sem custo.

### Áudio

`mcp__Magnific__audio_sfx_generate` (1–30s, com `loop` para ambiência e 1–4 variações) · `mcp__Magnific__audio_tts_direction` · `mcp__Magnific__audio_voice_change` · `mcp__Magnific__audio_isolate` (mín. 4,6s)

Cenas e ensaios: passos, porta batendo, trovão, chuva, plateia. `loop` faz ambiência contínua e as variações evitam que a deixa repetida soe idêntica. Locução: chamar `audio_tts_direction` **antes** de escrever o texto para aprender a sintaxe de direção de interpretação; `audio_isolate` salva depoimento gravado em hall barulhento; `audio_voice_change` recasta a leitura da direção na voz do personagem.

### Acessibilidade — legenda e dublagem

`mcp__Magnific__video_dubbing` abre o estúdio (idioma, legenda, `keepOriginalVoice`, 30 presets de legenda; nada é cobrado até confirmar). Caminho revisado, sem interface: `mcp__Magnific__video_dubbing_preview` (transcrição e tradução **gratuitas**, devolve `previewId`) → `mcp__Magnific__video_dubbing_preview_get` (revisor corrige tempo de legenda e nomes de personagem que o modelo ouviu errado) → `mcp__Magnific__video_dubbing_confirm` (cobra e renderiza só depois das correções).

Legenda em português em todo vídeo de espetáculo é política de acessibilidade da empresa. `keepOriginalVoice: true` legenda sem mexer no áudio. O mesmo caminho dubla para espanhol ou inglês em inscrição de festival.

### Entrega para a equipe

`mcp__Magnific__creations_deliver` (gratuito) rende no formato nativo de cada profissional: ProRes/DNxHR/h264/sequência PNG para o editor, `png16`/`tiff16`/`psd_layers` para o designer, `wav_48k`/`mp3` para o operador de som.

### Organização do acervo

`mcp__Magnific__creations_upload_show` · `mcp__Magnific__creations_register_download` · `mcp__Magnific__creations_comments_list` · `mcp__Magnific__creations_tags` · `mcp__Magnific__tags_create` · `mcp__Magnific__tags_list` · `mcp__Magnific__tags_update` · `mcp__Magnific__tags_delete` · `mcp__Magnific__tags_assign` · `mcp__Magnific__tags_unassign` · `mcp__Magnific__spaces_rename` · `mcp__Magnific__feed_view_options`

### Armadilhas e limites

- ⚠️ **Dublagem/legenda tem teto de 120 segundos por vídeo** — gravação de espetáculo inteiro precisa ser segmentada.
- ⚠️ `video_vfx` e `video_color_grade` limitam a 20s/200MB e exigem plano pago; `video_magic_cut` também exige plano pago.
- Depois de qualquer geração, seguir o campo `instruction` da resposta: `creations_show` para pré-visualizar e `creations_wait` para confirmar e obter a URL final do arquivo antes de encadear na ferramenta seguinte. **Nunca regerar criação em fila.**
- Arquivo local entra por `creations_upload_show` — o servidor **não lê anexo de chat**.
- **Nunca exponha identificador interno** (UUID, id de sessão, referência de pasta) para a equipe: use título e `webUrl`.
- Legenda em devanágari (híndi) não é suportada.

---

## Vercel — `mcp__Vercel__*`

**Status:** ✅ live · Agentes: Marketing, CEO · Site: `ensinoemcena.com.br`

### O que existe

**Analytics** — `mcp__Vercel__aggregate_pageviews` · `mcp__Vercel__count_pageviews` · `mcp__Vercel__aggregate_events` · `mcp__Vercel__count_events` · `mcp__Vercel__create_observability_query`
**Domínio e deploy** — `mcp__Vercel__list_project_domains` · `mcp__Vercel__add_project_domain` · `mcp__Vercel__create_deployment` · `mcp__Vercel__get_project_trace`

### Fluxos da empresa

**Antes de qualquer análise:** descobrir o escopo (time/projeto) e rodar `mcp__Vercel__list_project_domains` para confirmar que `ensinoemcena.com.br` e as variantes www/redirect estão de fato neste projeto. Isso **ainda não foi verificado ao vivo**.

**Atribuição de lead** (skill `relatorio-metricas`, agente Marketing):
- `mcp__Vercel__aggregate_pageviews` agrupado por `['utmCampaign', 'requestPath']` — qual campanha levou tráfego para qual página de espetáculo.
- `mcp__Vercel__aggregate_events` agrupado por `['eventName', 'utmCampaign']` (ou por `eventData/<curso>`) — qual campanha gerou **envio de formulário de matrícula**, que é o número que importa.
- `mcp__Vercel__count_pageviews` e `mcp__Vercel__count_events` dão o topo de linha da temporada.
- `mcp__Vercel__create_observability_query` monta funil de visitante único com `bucketTimezone` em `America/Sao_Paulo`, para os dias baterem com o calendário brasileiro.

**Campanha e páginas** — `mcp__Vercel__add_project_domain` sobe subdomínio de campanha ou redireciona URL de espetáculo antigo; `mcp__Vercel__create_deployment` publica a página da temporada e, redeployando por `deploymentId`, **reverte na hora** para a última versão boa no meio de uma campanha. `mcp__Vercel__get_project_trace` diagnostica deploy lento ou quebrado.

### Armadilhas e limites

- ⚠️ **UTM tem que estar no ar ANTES da campanha começar.** Web Analytics só cobre dados posteriores à ativação; atribuição de campanha já veiculada é irrecuperável.
- ⚠️ `mcp__Vercel__record_events` **não é analytics de marketing** — é telemetria de cache de build do Turborepo. Não use para lead.
- Os dados de Web Analytics são de **produção** apenas.
- O servidor expõe centenas de outras ferramentas (env vars, DNS, firewall, edge config, flags, sandboxes, billing). Fora do escopo da empresa: **não mexa** em configuração de projeto sem pedido explícito da CEO.

---

## Pendentes

Nada aqui funciona hoje. Nenhum agente deve descrever essas ações como feitas — descreva o que produziu e **quem precisa executar manualmente**.

### WhatsApp — Comercial, Operações

- **Status:** 🟡 pendente · sem servidor MCP
- **O que falta:** conta **WhatsApp Business API** (via Meta ou BSP homologado), número verificado, templates de mensagem aprovados pela Meta (mensagem ativa fora da janela de 24h exige template aprovado) e credenciais no `.paperclip.yaml`.
- **Impacto direto:** o passo **D-2 (lembrete por WhatsApp)** do playbook `onboarding-escola` é manual.
- **Enquanto isso:** redigir a mensagem pronta para copiar e colar, entregar à pessoa responsável junto com nome e telefone do contato, e registrar no issue que o lembrete foi enviado. Contato rápido com coordenação migra para `mcp__Gmail__reply` na thread existente.

### CRM — Comercial, Marketing, CEO

- **Status:** 🟡 pendente · provedor ainda não definido
- **O que falta:** decisão de provedor (RD Station, HubSpot, Pipedrive, planilha estruturada), conta, e conector MCP ou API com credenciais.
- **Impacto direto:** nenhum — os contratos de execução do Comercial e do Marketing já apontam o `pipeline-comercial` como fonte da verdade e marcam o CRM como pendente. Nenhum agente deve prometer registro em CRM.
- **Enquanto isso:** o pipeline vive nos **issues do Paperclip** (projeto `pipeline-comercial`), com o funil espelhado em labels do Gmail (`mcp__Gmail__create_label` + `mcp__Gmail__update_message_labels`) e a base de escolas numa planilha no Drive, localizada por `mcp__Google_Drive__search_files`. Nunca deixe lead só no chat.

### Agendador de redes sociais — Marketing

- **Status:** 🟡 pendente · sem conector (Instagram/TikTok não publicam por aqui)
- **O que falta:** conta em agendador (Meta Business Suite, Buffer, Later, mLabs), contas Instagram/TikTok vinculadas como conta profissional e token de API com permissão de publicação.
- **Impacto direto:** o passo 3 do Fluxo da skill `gestao-redes-sociais` para no preparo e arquivamento — a publicação em si **não executa**.
- **Enquanto isso:** entregar o pacote completo pronto para publicação manual — arte via `mcp__Canva__edit-design` (link do design), variações de formato via `mcp__Magnific__images_expand`, legenda e hashtags no issue do `calendario-editorial`, com data e horário de publicação sugeridos. Deixe explícito no relatório que **a publicação é humana**.

### Gerenciador de anúncios (Meta / Google / LinkedIn) — Marketing

- **Status:** 🟡 pendente · sem conector
- **O que falta:** conta de anúncios ativa em cada plataforma, Gerenciador de Negócios com pixel instalado no site, forma de pagamento aprovada e acesso de API com permissão de campanha.
- **Impacto direto:** a etapa 8 ("Veiculação") da skill `gestao-anuncios` **não executa**. Lembre também que campanha paga acima de R$ 3.000/mês passa por gate de aprovação da CEO — o gate continua valendo quando a integração entrar.
- **Enquanto isso:** entregar o plano de mídia completo (público, criativo, orçamento diário, período, texto do anúncio) para subida manual, e **exigir que as UTMs sejam definidas junto com o plano, antes da veiculação** — sem isso, `mcp__Vercel__aggregate_events` não consegue atribuir nenhuma matrícula à campanha depois. Custo por lead entra no relatório via número informado manualmente pela plataforma, com fonte e data de corte.

---

## Quando algo mudar

1. Atualize este arquivo **e** a tabela de integrações do `README.md` da empresa (as duas precisam concordar).
2. Ajuste as skills que citam a integração — hoje `gestao-redes-sociais` (etapa 3), `gestao-anuncios` (etapa 4), `relatorio-metricas` (fontes) e o playbook `onboarding-escola` (D-2).
3. Registre os nomes `mcp__*` exatos, não a marca. Agente só usa o que está escrito aqui.

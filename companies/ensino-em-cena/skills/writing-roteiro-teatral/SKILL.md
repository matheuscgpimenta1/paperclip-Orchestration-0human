---
name: writing-roteiro-teatral
description: Escreve e revisa roteiros teatrais educativos adequados ao circuito escolar (duração, faixa etária, alinhamento pedagógico)
---

# Roteiro Teatral Educativo

Use esta skill ao escrever ou revisar roteiros para espetáculos da Ensino em Cena.

## Princípios

- **Duração:** entre 40 e 60 minutos para escolas (uma aula dupla)
- **Faixa etária:** sempre identificada no cabeçalho (ex.: 6º ao 9º ano)
- **Tema central:** explícito e alinhado ao briefing pedagógico
- **Linguagem:** acessível ao público-alvo, sem infantilizar
- **Estrutura:** prólogo curto → desenvolvimento em cenas numeradas → desfecho → ganchos para mediação pós-espetáculo

## Formato do roteiro

```
TÍTULO
Faixa etária | Duração | Personagens

CENA 1 — [local, tempo]
[Rubrica curta]
PERSONAGEM: fala.
```

## Ferramentas

**Google Drive — versionamento e biblioteca de roteiros aprovados (conectado)**

- `mcp__Google_Drive__search_files` para localizar um roteiro pelo conteúdo (`fullText contains` com uma fala ou personagem), não só pelo título; é o passo obrigatório para obter o `fileId` real — nunca invente um.
- `mcp__Google_Drive__read_file_content` para ler o roteiro antes de revisar, com `includeComments` ligado para trazer as anotações de margem da direção e da coordenação pedagógica na mesma passada.
- `mcp__Google_Drive__create_file` para publicar a nova versão do roteiro na pasta certa (ou criar a pasta com mimeType `application/vnd.google-apps.folder`), e `mcp__Google_Drive__update_file` para renomear ou mover entre "Em escrita" e "Aprovados".
- `mcp__Google_Drive__copy_file` para derivar uma adaptação (outra faixa etária, versão reduzida) a partir do roteiro-mestre em vez de reescrever.
- `mcp__Google_Drive__share_file` em `reader` ou `commenter` para liberar o roteiro ao elenco e à escola, e `mcp__Google_Drive__get_file_permissions` para auditar quem enxerga um texto ainda não aprovado.
- **Limitações reais:** não existe ferramenta para editar o conteúdo de um arquivo já existente — revisar "no mesmo arquivo" é impossível; o caminho é `create_file` da nova versão + `mcp__Google_Drive__trash_file` na antiga, o que quebra o histórico nativo de revisões do Drive. Por isso, **nomeie a versão no título** (ex.: `Roteiro — v3 — 2026-09`). `mcp__Google_Drive__download_file_content` lê uma `revisionId` específica, mas nada aqui lista revisões: só funciona se o ID já for conhecido. `share_file` apenas eleva permissão, nunca reduz nem revoga — revogar é na interface do Drive. Não há como escrever ou responder comentários: o retorno do professor e da direção é somente leitura por aqui.

**Freepik/Magnific — exploração de trilha, efeitos sonoros e voz (conectado)**

Use apenas como **exploração e maquete de ensaio**, nunca como entrega final sem validação da direção.

- `mcp__Magnific__audio_sfx_generate` para maquetar deixas sonoras do roteiro (porta batendo, trovão, passos, burburinho de plateia): 1–30s, com `loop` para ambiências contínuas e variações para a mesma deixa não soar idêntica a cada repetição.
- `mcp__Magnific__audio_tts_direction` **antes** de escrever qualquer texto de narração ou locução — ela devolve a sintaxe de direção de interpretação (tags, pausas, roteiro de cena) e evita gastar geração com leitura errada.
- `mcp__Magnific__audio_voice_change` para recastear uma leitura gravada pela direção na voz do personagem, preservando timing e emoção; `mcp__Magnific__audio_isolate` para salvar áudio de voz gravado em teatro ou saguão ruidoso (mínimo 4,6s).
- `mcp__Magnific__creations_deliver` para entregar o arquivo ao operador de som em `wav_48k`.
- **Como o servidor funciona:** após qualquer geração é preciso chamar `creations_show` e `creations_wait` para obter o arquivo final; e `mcp__Magnific__creations_upload_show` é o único caminho para trazer um áudio local — o servidor não lê anexos do chat. Nunca exponha identificadores internos do Freepik para a equipe: use títulos e a `webUrl`.

**Canva — materiais de leitura e encenação (conectado)**

- `mcp__Canva__search-brand-templates` para achar o template da casa (roteiro de leitura dramática, folha de cena, encarte do espetáculo) e `mcp__Canva__create-design-from-brand-template` para abrir a peça a partir dele, com `page_numbers` quando só algumas páginas interessam.
- `mcp__Canva__read-design` com `open_transaction: true` seguido de `mcp__Canva__edit-design` para preencher título, faixa etária, duração, elenco e as falas (`replace_text`, `find_and_replace_text`, `add_text`, `add_page`, `reorder_page`, `format_text` para numerar exercícios e marcações de cena) e fechar com `finalize`.
- `mcp__Canva__copy-design` para derivar a versão do aluno a partir da versão do mediador, e `mcp__Canva__publish-brand-template` para fixar o layout como padrão da companhia.
- **Limitações reais:** **não há ferramenta de exportação no Canva desta sessão** — a entrega sai como URL de design/compartilhamento, não como PDF baixado. Para PDF impresso, o caminho é a família Adobe (`mcp__Adobe_for_creativity__markdown_to_pdf`, `pdf_create`, `pdf_combine`), lembrando que o servidor Adobe exige `adobe_mandatory_init` antes da primeira chamada. Também não há upload de asset: fotos novas de produção precisam entrar pela interface do Canva.

**Ainda pendentes (não conectados — não prometa à equipe):** WhatsApp, CRM, agendador automático de redes sociais (Instagram/TikTok) e gestores de mídia paga (Meta Ads / Google Ads / LinkedIn Ads).

## Checklist antes de entregar

- [ ] Cabeçalho com faixa etária, duração e elenco mínimo
- [ ] Cada cena tem rubrica de espaço cênico
- [ ] Vocabulário verificado para a faixa etária
- [ ] Indicações para o guia do professor (perguntas de mediação) anexadas
- [ ] Aprovado pelo Diretor Artístico e Coordenador Pedagógico

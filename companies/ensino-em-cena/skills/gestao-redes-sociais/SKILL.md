---
name: gestao-redes-sociais
description: Planeja o calendário editorial e produz as peças de redes sociais (Instagram, TikTok, Facebook, YouTube); a publicação ainda é manual — não há agendador integrado
---

# Gestão de Redes Sociais

Use para planejamento editorial e produção das peças das redes da Ensino em Cena. A produção é automatizada; a publicação em si ainda depende de uma pessoa (ver **Ferramentas**).

## Canais

| Canal | Foco | Frequência |
|-------|------|------------|
| Instagram (feed) | Espetáculos, projetos, depoimentos | 3-4x semana |
| Instagram (stories) | Bastidores, lembretes, enquetes | Diário |
| Instagram (reels) | Trechos de espetáculo, prévias | 2x semana |
| TikTok | Reels reaproveitados + conteúdo de bastidor | 2-3x semana |
| Facebook | Reposicionamento para coordenação escolar | 2x semana |
| YouTube | Trailers, vídeos institucionais | Por lançamento |

## Pilares editoriais

1. **Produto** — espetáculos e projetos no portfólio
2. **Bastidor** — humanizar o trabalho artístico-pedagógico
3. **Impacto** — depoimentos de escolas, alunos, professores
4. **Educação** — conteúdo de valor para coordenadores (BNCC, leitura)
5. **CTA** — chamadas para agendamento, contato, lançamento

## Fluxo

1. Planejamento mensal — calendário editorial publicado no Drive (`mcp__Google_Drive__create_file` / `mcp__Google_Drive__search_files`)
2. Produção semanal — peças criadas no Canva a partir de templates do brand kit (`mcp__Canva__search-brand-templates` → `mcp__Canva__create-design-from-brand-template` → `mcp__Canva__read-design` com `open_transaction: true` → `mcp__Canva__edit-design`); imagem e vídeo tratados no Freepik quando necessário
3. Preparo e arquivamento — **não há agendador de redes sociais conectado**. O agente deixa a peça pronta e arquivada no Drive (`mcp__Google_Drive__create_file`, com o link do design Canva quando for peça nativa), registra no calendário editorial (data, hora, canal, legenda, hashtags, link com UTM) e avisa o responsável por e-mail (`mcp__Gmail__create_draft` / `mcp__Gmail__send_message`). **A publicação no Instagram, TikTok, Facebook e YouTube é feita manualmente por uma pessoa**, até que um agendador seja integrado.
4. Monitoramento — métricas semanais; tráfego e conversão do site medidos por UTM no Vercel (`mcp__Vercel__aggregate_pageviews`, `mcp__Vercel__aggregate_events`); métricas nativas das redes coletadas manualmente; ajustes no calendário do mês seguinte

## Ferramentas

**Produção de peças — conectada.** O agente consegue criar a peça de ponta a ponta.

| Necessidade | Ferramentas | Observações |
|-------------|-------------|-------------|
| Arte de post, story, reels, capa | `mcp__Canva__search-brand-templates`, `mcp__Canva__get-brand-template-dataset`, `mcp__Canva__create-design-from-brand-template`, `mcp__Canva__copy-design`, `mcp__Canva__read-design`, `mcp__Canva__edit-design`, `mcp__Canva__create-brand-template-draft`, `mcp__Canva__publish-brand-template` | Edição só dentro de transação: `read-design` com `open_transaction: true` e depois `edit-design` com `finalize`. Não há ferramenta de exportação do Canva nesta sessão — a entrega sai como link do design + thumbnails das páginas, não como PNG/JPG baixado. `update_fill` exige `asset_id` de imagem já existente no Canva: foto nova de espetáculo pode precisar de upload manual pela interface. |
| Tratamento de imagem | `mcp__Magnific__images_retouch` (apagar saída de emergência, microfone, equipe no fundo), `mcp__Magnific__images_expand` (mesma foto em 1:1, 9:16, 16:9), `mcp__Magnific__images_upscale_modes_list`, `mcp__Magnific__images_upscale_presets_list`, `mcp__Magnific__retouch_models_list`, `mcp__Magnific__flows_create` | `flows_create` guarda o pipeline "foto de divulgação → kit de redes" para reusar a cada montagem. |
| Vídeo (reels, trailer, corte) | `mcp__Magnific__video_crop` (um master vira Reels/YouTube/telão), `mcp__Magnific__video_magic_cut`, `mcp__Magnific__video_extend`, `mcp__Magnific__video_color_grade`, `mcp__Magnific__video_vfx`, `mcp__Magnific__video_music`, `mcp__Magnific__video_soundfx`, `mcp__Magnific__video_audio_mix`, `mcp__Magnific__creations_deliver` | `video_vfx` e `video_color_grade` limitam a 20s/200MB e exigem plano pago; `video_magic_cut` também é plano pago. |
| Legendas e acessibilidade | `mcp__Magnific__video_dubbing`, `mcp__Magnific__video_dubbing_preview`, `mcp__Magnific__video_dubbing_preview_get`, `mcp__Magnific__video_dubbing_confirm` | Limite de 120s por vídeo — depoimento longo precisa ser fatiado. O preview é gratuito: revisar transcrição e nomes de personagens antes de confirmar. |
| Tipografia do brand kit | `mcp__Adobe_for_creativity__suggest_type_palettes`, `mcp__Adobe_for_creativity__get_type_palette`, `mcp__Adobe_for_creativity__font_search` (usar `writing_systems: latn` para acentuação em português), `mcp__Adobe_for_creativity__font_preview`, `mcp__Adobe_for_creativity__font_styles`, `mcp__Adobe_for_creativity__font_activate`, `mcp__Adobe_for_creativity__font_create_kit` | O servidor Adobe exige uma chamada de inicialização obrigatória antes da primeira ferramenta. `font_activate` libera a fonte nos apps Adobe — **não** dentro do Canva; a tipografia do Canva é configurada separadamente no Brand Kit. |
| Arquivo e calendário editorial | `mcp__Google_Drive__create_file`, `mcp__Google_Drive__search_files`, `mcp__Google_Drive__update_file`, `mcp__Google_Drive__copy_file`, `mcp__Google_Drive__read_file_content`, `mcp__Google_Drive__share_file`, `mcp__Google_Drive__list_recent_files`, `mcp__Google_Drive__trash_file` | Não existe ferramenta que altere o **conteúdo** de um arquivo já no Drive — `update_file` só muda título e pasta. Revisar um calendário editorial significa criar nova versão e descartar a antiga. `share_file` só eleva permissão, nunca revoga. |
| Aviso e aprovação interna | `mcp__Gmail__create_draft`, `mcp__Gmail__send_message`, `mcp__Gmail__reply`, `mcp__Gmail__search_threads`, `mcp__Gmail__get_thread`, `mcp__Gmail__label_thread` | `search_threads` mostra só as ~5 mensagens **mais antigas** de cada conversa: para ler a aprovação mais recente é obrigatório chamar `get_thread`. |
| Datas de espetáculo e gravação | `mcp__Google_Calendar__search_events` | **Somente leitura**, e só na agenda principal. Serve para conferir datas já marcadas; não cria nem move evento. |
| Mensuração de tráfego e conversão | `mcp__Vercel__aggregate_pageviews`, `mcp__Vercel__aggregate_events`, `mcp__Vercel__count_pageviews`, `mcp__Vercel__count_events`, `mcp__Vercel__create_observability_query`, `mcp__Vercel__list_project_domains` | Cobre só o site (ensinoemcena.com.br) e só dados posteriores à ativação do Web Analytics — a UTM precisa existir **antes** do post ir ao ar. Usar `bucketTimezone: America/Sao_Paulo`. Não mede alcance, seguidores nem engajamento nativo das redes. |

**Publicação e agendamento — PENDENTE.** Não existe MCP de agendador de redes sociais nesta sessão. Nada publica automaticamente no Instagram, no TikTok, no Facebook ou no YouTube, nem programa horário de post. Enquanto isso não for integrado, o agente para no passo 3 do Fluxo e uma pessoa publica.

Também continuam **pendentes** (não prometer a ninguém que funcionam):

- WhatsApp — sem integração
- CRM — sem integração; o funil vive em labels do Gmail e em arquivo no Drive
- Agendador de redes sociais (Instagram / TikTok / Facebook / YouTube) — sem integração
- Gerenciadores de mídia paga (Meta Ads, Google Ads, LinkedIn Ads) — sem integração; ver skill `gestao-anuncios`

## Princípios

- Todo conteúdo passa por revisão antes de ser liberado para publicação
- Linguagem voltada à coordenação pedagógica e direção escolar (não ao aluno final)
- UTMs em todo link para mensuração
- Identidade visual consistente (cores, tipografia, marca-d'água)

## Gates

- Posts que mencionam parceiros ou escolas específicas → aprovação Comercial
- Campanhas pagas → ver skill `gestao-anuncios`

## Checklist antes de liberar para publicação

- [ ] Peça revisada (texto + visual)
- [ ] CTA claro + link com UTM
- [ ] Data e hora ideais por canal
- [ ] Hashtags padrão do brand kit
- [ ] Registro no calendário editorial
- [ ] Arquivo final (ou link do design Canva) salvo na pasta do mês no Drive
- [ ] Responsável pela publicação manual avisado, com data e canal

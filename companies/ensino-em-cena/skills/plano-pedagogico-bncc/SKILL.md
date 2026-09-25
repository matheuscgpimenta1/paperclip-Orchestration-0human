---
name: plano-pedagogico-bncc
description: Estrutura projetos pedagógicos (leitura, escrita, espetáculos) alinhados à BNCC, com objetivos, etapas e avaliação
---

# Plano Pedagógico BNCC

Use esta skill ao desenhar projetos de leitura/escrita ou validar alinhamento pedagógico de espetáculos.

## Estrutura obrigatória

1. **Identificação:** título, segmento (Fundamental I/II, Médio), duração total
2. **Competências da BNCC:** listar códigos das competências gerais e específicas mobilizadas
3. **Objetivos de aprendizagem:** verbos no infinitivo, observáveis
4. **Etapas:** sequência didática com nº de encontros, atividades e materiais
5. **Avaliação:** instrumentos (produção textual, rodas de leitura, dramatizações) e critérios
6. **Materiais:** lista do que será entregue à escola (caderno do aluno, guia do professor, encarte)

## Princípios

- Toda etapa deve ter objetivo declarado
- Avaliação formativa preferencial; somativa só quando solicitada pela escola
- Sempre prever momento de mediação coletiva (roda)
- Considerar acessibilidade e diversidade do público

## Ferramentas

**Google Drive — referência BNCC e documentos da sequência didática (conectado)**

- `mcp__Google_Drive__search_files` é o passo obrigatório de descoberta: busque o documento de referência da BNCC e planos anteriores por conteúdo (`fullText contains` com o código da habilidade, ex.: `EF69LP44`) combinado com `mimeType = 'application/vnd.google-apps.document'` e recorte por `modifiedTime` para pegar só o material do ano letivo corrente. Nunca invente um `fileId`.
- `mcp__Google_Drive__read_file_content` para ler a referência BNCC, o plano de outra turma ou um contrato/briefing da escola (funciona também em PDF, DOCX, XLSX) antes de escrever; com `includeComments` para trazer as observações da coordenação.
- `mcp__Google_Drive__create_file` com `textContent` para publicar a sequência didática pronta direto na pasta certa como Google Doc, e `create_file` com mimeType `application/vnd.google-apps.folder` para montar a árvore (Planos / Sequências / Cadernos do aluno / Guias do professor).
- `mcp__Google_Drive__copy_file` para partir de um plano-mestre por segmento (Fundamental I, Fundamental II, Médio) em vez de reescrever, e `mcp__Google_Drive__update_file` para renomear e mover entre pastas.
- `mcp__Google_Drive__share_file` em `commenter` para a escola comentar o plano sem editar, e `mcp__Google_Drive__get_file_permissions` para auditar quem tem acesso; `mcp__Google_Drive__list_recent_files` para a revisão semanal do que foi mexido e `mcp__Google_Drive__trash_file` para aposentar rascunhos superados (recuperável na lixeira).
- **Limitações reais:** não existe ferramenta para alterar o **conteúdo** de um arquivo existente — revisar um plano em si mesmo é impossível; publique nova versão com `create_file` e descarte a antiga, versionando **no título** (ex.: `Plano 7º ano — v2 — 2026`). `share_file` só eleva permissão, nunca revoga — revogar é na interface do Drive, o que importa em planos com dados de alunos. Não é possível escrever nem responder comentários: o retorno da coordenação da escola é somente leitura por aqui. E `create_file` converte uploads para formato Google por padrão — use `disableConversionToGoogleType` para preservar um PDF assinado.

**Canva — caderno do aluno e guia do professor (conectado)**

- `mcp__Canva__search-brand-templates` (com `dataset: 'non_empty'` para achar os templates que aceitam preenchimento automático) e `mcp__Canva__get-brand-template-dataset` para ver o esquema de campos (turma, segmento, módulo, nº do encontro) antes de gerar.
- `mcp__Canva__create-design-from-brand-template` para abrir um caderno por módulo/turma, e `mcp__Canva__read-design` com `open_transaction: true` + `mcp__Canva__edit-design` para montar as páginas de exercício: `add_page`, `add_text`, `insert_shape` (caixas de resposta), `format_text` com `list_level`/`list_marker` para numerar atividades, `replace_text` para os dados da turma — fechando com `finalize`.
- Guia do professor: `mcp__Canva__copy-design` bifurca a edição do aluno em edição do professor em vez de refazer do zero, e `replace_speaker_notes` (via `edit-design`) guarda as notas de mediação do encontro.
- `mcp__Canva__create-brand-template-draft` + `update_autofill_field` + `mcp__Canva__publish-brand-template` para fixar o caderno-mestre como template da companhia, reaproveitado a cada turma.
- **Limitações reais:** **não há exportação no Canva desta sessão** — a entrega sai como URL de design, não como PDF para impressão na escola; para PDF, passe pelo Adobe (`mcp__Adobe_for_creativity__markdown_to_pdf`, `pdf_create`, `pdf_combine`, `pdf_compress`), lembrando que o servidor Adobe exige `adobe_mandatory_init` antes da primeira chamada. Não há ferramenta de preenchimento em lote exposta: gerar um caderno por turma é um laço de `create-design-from-brand-template` + `edit-design`, um por vez. `update_autofill_field` só funciona em páginas de tamanho fixo, não responsivas. E não há upload de asset — imagens novas entram pela interface do Canva.

**Ainda pendentes (não conectados — não prometa à escola):** WhatsApp, CRM, agendador automático de redes sociais (Instagram/TikTok) e gestores de mídia paga (Meta Ads / Google Ads / LinkedIn Ads).

## Checklist antes de entregar

- [ ] Códigos BNCC verificados e atualizados
- [ ] Sequência didática viável no calendário escolar
- [ ] Materiais do aluno e do professor diferenciados
- [ ] Aprovado pelo Coordenador Pedagógico

---
name: orcamento-proposta
description: Monta orçamento e proposta comercial para escolas e secretarias (espetáculo, projeto pedagógico ou pacote)
---

# Orçamento e Proposta Comercial

Use ao montar propostas para escolas particulares, redes públicas e secretarias.

## Componentes do orçamento

| Item | Como calcular |
|------|---------------|
| Cachê base | Tabela em `library/tabela-cache/` por tipo de produção e raio de deslocamento |
| Deslocamento | Km × custo logístico OU diárias da equipe |
| Hospedagem/alimentação | Se fora da sede, multiplicar por nº de pernoites × equipe |
| Materiais | Caderno do aluno + guia do professor + encarte (por turma atendida) |
| Impostos e taxas | Aplicar conforme regime tributário da Ensino em Cena |
| Margem | Definida pela CEO no início do ano |

## Estrutura da proposta

1. **Capa** — nome da escola, data da proposta, validade
2. **Sumário executivo** — o que está sendo proposto em 3 linhas
3. **Produto** — sinopse, faixa etária, duração, ficha técnica resumida
4. **Alinhamento pedagógico** — BNCC, objetivos de aprendizagem
5. **Investimento** — tabela de itens, total e formas de pagamento
6. **Cronograma** — datas propostas e prazos de confirmação
7. **Próximos passos** — quem aceita, onde assina, contato

## Gates de aprovação

- Cachê dentro da tabela → Comercial envia direto
- Cachê com desconto > 10% OU acima do teto → **board approval da CEO** antes de enviar
- Pacote multi-escola/rede → CEO + Operações validam capacidade antes de prometer datas

## Ferramentas

### Tabela de cachê e arquivo das propostas (Google Drive — conectado)

- Buscar a tabela de cachê vigente com `mcp__Google_Drive__search_files` (nunca chutar o `fileId`) e ler os valores com `mcp__Google_Drive__read_file_content`. Use `includeComments` para capturar ressalvas da CEO deixadas em comentário.
- Se a proposta precisar reproduzir a tabela exatamente como estava numa data passada (renegociação, contrato já assinado), use `mcp__Google_Drive__download_file_content` com `revisionId` — o servidor não lista revisões, então o ID precisa ser conhecido de antemão.
- Arquivar a proposta enviada com `mcp__Google_Drive__create_file` na pasta de propostas (`parentId`), com `disableConversionToGoogleType` quando o arquivo for um PDF assinado que deve manter o formato original.
- Compartilhar com a escola via `mcp__Google_Drive__share_file` em `reader` ou `commenter`, e conferir quem já tem acesso com `mcp__Google_Drive__get_file_permissions`.
- **Limite real:** não existe ferramenta para editar o conteúdo de um arquivo já existente — revisar uma proposta significa criar nova versão com `mcp__Google_Drive__create_file` e aposentar a antiga com `mcp__Google_Drive__trash_file`. E `share_file` só sobe permissão: revogar acesso é manual, pela interface do Drive.

### Renderização com identidade visual (Canva — conectado)

- Localizar o template da proposta com `mcp__Canva__search-brand-templates` (`design_types: ['presentation']`) e criar a versão da escola com `mcp__Canva__create-design-from-brand-template`.
- Preencher nome da escola, datas, itens do investimento e validade com `mcp__Canva__read-design` (`open_transaction: true`) seguido de `mcp__Canva__edit-design` (`find_and_replace_text`, `replace_text`, `format_text`) e `finalize` para commitar.
- Consultar os campos disponíveis com `mcp__Canva__get-brand-template-dataset`; se o template ainda não estiver marcado, preparar o mestre com `mcp__Canva__create-brand-template-draft` + `mcp__Canva__publish-brand-template`.
- **Limite real:** não há ferramenta de exportação no Canva — a entrega sai como URL do design. Para um PDF de verdade, use a via Adobe (`mcp__Adobe_for_creativity__pdf_create` / `mcp__Adobe_for_creativity__markdown_to_pdf` / `mcp__Adobe_for_creativity__pdf_combine`), lembrando que o servidor Adobe exige uma chamada de init antes do primeiro uso.

### Envio e acompanhamento da thread (Gmail — conectado)

- Sempre montar a proposta primeiro como rascunho com `mcp__Gmail__create_draft` (`htmlBody` + `attachments`), revisar com `mcp__Gmail__get_draft` e só então disparar com `mcp__Gmail__send_message`. Envio direto sem revisão humana não é o padrão da casa.
- Acima de 25MB no total, não anexe: suba no Drive e coloque o link no corpo.
- **Armadilha:** `mcp__Gmail__update_draft` faz merge nos textos mas **remove anexos que não forem reenviados** — ao ajustar só o texto, reenvie o PDF da proposta ou ele some silenciosamente.
- Etiquetar o funil com `mcp__Gmail__create_label` / `mcp__Gmail__list_labels` (labels exigem ID, não nome) e `mcp__Gmail__label_thread` — ex.: `Proposta/Enviada`, `Proposta/Aprovada`. Para mover de etapa em uma chamada só, use `mcp__Gmail__update_message_labels`.
- Follow-up: `mcp__Gmail__search_threads` para achar a conversa e **obrigatoriamente** `mcp__Gmail__get_thread` (`PLAIN_TEXT`) antes de responder — a busca só mostra as ~5 mensagens mais antigas da thread, sem aviso de truncamento, então responder a partir do preview gera erro. Responder com `mcp__Gmail__reply` no `messageId` da última mensagem, mantendo o encadeamento.
- `mcp__Gmail__forward` reaproveita a proposta para a mantenedora/diretoria; `mcp__Gmail__unmark_thread_spam` resgata retorno de escola que caiu em spam.

### Pré-validação de datas com Operações (Google Calendar — conectado, somente leitura)

- `mcp__Google_Calendar__search_events` consulta a agenda primária por termo (nome da escola, cidade, "ensaio", "temporada") para conferir se as datas propostas já têm compromisso.
- **Limite crítico:** é a única ferramenta do servidor. Não cria, não altera e não cancela evento, não consulta disponibilidade (free/busy), não lê agendas secundárias e não filtra por período. A confirmação da data continua sendo combinada com Operações e lançada manualmente na agenda — o Calendar aqui serve para *conferir* o que já foi lançado, não para reservar.

### Pendentes (não conectados — não prometer ao cliente nem à equipe)

- **CRM** — sem integração. O registro do pipeline é feito como issue no projeto Paperclip `pipeline-comercial`, não em registro de CRM.
- **WhatsApp** — sem integração. Contato por WhatsApp com a escola é manual.
- **Agendador de redes sociais** (Instagram/TikTok) e **gestores de mídia paga** (Meta Ads / Google Ads / LinkedIn Ads) — sem integração; fora do escopo deste fluxo.

## Checklist antes de enviar

- [ ] Ficha técnica compatível com o espaço da escola
- [ ] Cachê conferido contra a tabela vigente lida do Drive (`mcp__Google_Drive__search_files` → `mcp__Google_Drive__read_file_content`)
- [ ] Datas pré-validadas com Operações e conferidas na agenda com `mcp__Google_Calendar__search_events` (confirmação e lançamento na agenda seguem manuais)
- [ ] Template de proposta atualizado em `library/templates/proposta-comercial.md`
- [ ] Proposta renderizada com identidade visual no Canva (`mcp__Canva__create-design-from-brand-template` + `mcp__Canva__edit-design`) e, se o PDF for exigido, gerado pela via Adobe
- [ ] Rascunho criado com `mcp__Gmail__create_draft` e revisado por humano antes do `mcp__Gmail__send_message` (anexos reenviados se o rascunho foi atualizado)
- [ ] Thread etiquetada com `Proposta/Enviada` via `mcp__Gmail__label_thread`
- [ ] Cópia da proposta enviada arquivada no Drive com `mcp__Google_Drive__create_file`
- [ ] Registro no pipeline como issue do projeto `pipeline-comercial` (CRM segue pendente)

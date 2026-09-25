---
name: logistica-turne
description: Planeja e executa logística de turnê — agenda, deslocamento, hospedagem, montagem e equipe técnica
---

# Logística de Turnê

Use ao receber contrato fechado do Comercial e ao coordenar apresentações em rota.

## Insumos necessários (antes de planejar)

- Contrato assinado + briefing logístico do Comercial
- Ficha técnica do espetáculo (Diretor Artístico)
- Disponibilidade da equipe artística e técnica
- Calendário da empresa (Google Calendar)

## Etapas

1. **Confirmação** — confirma data com a escola por e-mail e registra a agenda no Calendar (o agente redige os dados do evento; a criação no Calendar é feita por humano — ver Ferramentas)
2. **Equipe** — escala elenco e técnica; confirma disponibilidade individual
3. **Deslocamento** — define modal (van, carro, avião), reserva, distribui passagens
4. **Hospedagem/alimentação** — se aplicável, reserva e fecha por pernoite
5. **Materiais** — lista de equipamento, cenário, figurino e encartes para a escola; ficha técnica localizada e compartilhada a partir do Drive
6. **Comunicação com a escola** — confirma horário de chegada, tempo de montagem, espaço, contato no local (respondendo na própria thread da contratação, com replyAll)
7. **Execução** — equipe em rota; ponto focal disponível para imprevistos
8. **Pós-evento** — relatório curto: o que funcionou, ocorrências, sugestões — gravado no Drive e enviado por e-mail aos envolvidos

## Padrões

- Toda apresentação vira issue child no projeto `agenda-turne/`
- Bloqueio em Calendar inclui: equipe, equipamento, contato da escola, endereço — o agente entrega esse texto pronto para lançamento manual
- Briefing logístico para a escola enviado por e-mail (WhatsApp pendente) 7 dias antes
- Lembrete automático 48h antes (rotina), por e-mail (WhatsApp pendente)

## Ferramentas

### Google Calendar — agenda da turnê (SOMENTE LEITURA)

O servidor expõe **uma única ferramenta**: `mcp__Google_Calendar__search_events` (busca semântica por texto, apenas no calendário principal, com paginação).

- **Não existe** criação, edição ou exclusão de evento; também não há consulta de disponibilidade (free/busy), seleção de outros calendários, filtro por intervalo de datas nem tratamento de convidados.
- Portanto o agente **não bloqueia** a agenda: ele **redige os dados do evento** (título no padrão `Apresentação — <espetáculo> — <escola>`, data, horário de chegada e de início, endereço, equipe escalada, equipamento, contato no local) e **entrega para um humano lançar** no Google Calendar.
- Depois do lançamento, use `mcp__Google_Calendar__search_events` (buscando pelo nome da escola, da cidade ou do espetáculo) para **conferir o que de fato entrou** no calendário e reconciliar com a rota planejada.
- Como não há detecção de conflito, o cruzamento de datas entre apresentações é feito na lista da própria turnê (`agenda-turne/`), não no Calendar.

### Gmail — confirmações e briefing para a escola

- Confirmação de data e logística: `mcp__Gmail__get_thread` (formato `PLAIN_TEXT`) para reler o combinado e pegar o `messageId` da última mensagem, e `mcp__Gmail__reply` com `replyAll=true` — assim coordenação pedagógica, secretaria e produção ficam na mesma conversa.
- Atenção: `mcp__Gmail__search_threads` mostra preview só das ~5 mensagens **mais antigas** da thread, sem aviso de truncamento. Nunca responda sobre "o último retorno da escola" a partir da busca — chame sempre `mcp__Gmail__get_thread`.
- Briefing logístico (7 dias antes): `mcp__Gmail__create_draft` com `htmlBody` para revisão humana e depois `mcp__Gmail__send_message`. Anexos somam até 25MB; acima disso, suba no Drive e mande o link.
- Se ajustar um rascunho já com anexo, reenvie o anexo em `mcp__Gmail__update_draft` — anexos **não** são mesclados e somem silenciosamente.
- Repasse para elenco, técnica e transporte: `mcp__Gmail__forward` da thread da escola com `forwardText` resumindo endereço, horário de chegada e contato na portaria.
- Funil e organização: `mcp__Gmail__list_labels` (os labels exigem ID, não nome), `mcp__Gmail__create_label` para `Logistica/<mês>` e `mcp__Gmail__label_thread` para agrupar a temporada.
- Resposta da escola que caiu em spam: `mcp__Gmail__unmark_thread_spam`.

### Google Drive — ficha técnica, contrato e relatório pós-evento

- Localize sempre antes de ler: `mcp__Google_Drive__search_files` (ou `mcp__Google_Drive__list_recent_files`) para obter o `fileId` real — `fileId` inventado é rejeitado.
- Ficha técnica e contrato: `mcp__Google_Drive__read_file_content` para extrair datas, valores, cláusulas e exigências técnicas (funciona em Google Docs, PDF e DOCX); use `includeComments` para trazer as anotações da direção.
- Ficha técnica por montagem/espaço: `mcp__Google_Drive__copy_file` a partir do rider mestre, com novo título e `parentId`, em vez de reescrever.
- Envio para a escola e para o técnico do espaço: `mcp__Google_Drive__share_file` com papel `reader` (ficha técnica) ou `commenter` (material pedagógico), e `mcp__Google_Drive__get_file_permissions` para auditar quem enxerga o contrato. **A ferramenta só eleva permissão** — revogar ou rebaixar acesso tem de ser feito na interface do Drive.
- Relatório pós-evento: `mcp__Google_Drive__create_file` com `textContent` direto na pasta da temporada. Não existe ferramenta para editar o **conteúdo** de um arquivo já existente (`mcp__Google_Drive__update_file` muda só título e pasta), então correção vira novo arquivo + `mcp__Google_Drive__trash_file` no antigo.
- Ao subir contrato assinado em PDF, use `disableConversionToGoogleType` para não converter o original.

### Pendentes (não use, não prometa)

- **WhatsApp** — sem integração. Todo briefing e lembrete sai por **e-mail (WhatsApp pendente)**; o envio por WhatsApp, se necessário, é manual.
- **CRM** — pendente; pipeline segue em labels do Gmail e nas issues de `agenda-turne/`.
- **Agendador de redes sociais** (Instagram/TikTok automático) — pendente.
- **Gestores de mídia paga** (Meta Ads / Google Ads / LinkedIn Ads) — pendentes.
- Gmail **não** tem envio em lote, mala direta nem agendamento de envio: cada escola é uma chamada `mcp__Gmail__send_message`, sempre imediata.

## Gates de escalonamento

- Conflito de data entre apresentações → CEO decide prioridade
- Atraso de pagamento da escola → Comercial reativa relacionamento; Operações pausa logística

## Checklist por apresentação

- [ ] Contrato + ficha técnica em mãos (localizados com `mcp__Google_Drive__search_files` e lidos com `mcp__Google_Drive__read_file_content`)
- [ ] Equipe confirmada
- [ ] Transporte e hospedagem reservados
- [ ] Briefing enviado para escola por e-mail (WhatsApp pendente) — rascunho em `mcp__Gmail__create_draft`, revisado e disparado com `mcp__Gmail__send_message`
- [ ] Confirmação de logística respondida na thread da escola (`mcp__Gmail__get_thread` + `mcp__Gmail__reply` com `replyAll=true`)
- [ ] Ficha técnica compartilhada com o espaço (`mcp__Google_Drive__share_file`, papel `reader`)
- [ ] Dados do evento redigidos e entregues para lançamento manual no Calendar (não há criação de evento)
- [ ] Evento conferido no Calendar após o lançamento (`mcp__Google_Calendar__search_events`)
- [ ] Lembrete 48h antes enviado por e-mail (WhatsApp pendente)
- [ ] Relatório pós-evento gravado no Drive (`mcp__Google_Drive__create_file`)
- [ ] Issue child criada em `agenda-turne/`

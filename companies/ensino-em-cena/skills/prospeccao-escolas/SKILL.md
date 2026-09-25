---
name: prospeccao-escolas
description: Prospecção de escolas e secretarias de educação para venda de espetáculos e projetos pedagógicos
---

# Prospecção de Escolas

Use esta skill ao prospectar novas escolas/secretarias ou qualificar leads recebidos do Marketing.

## Pipeline

1. **Lead** — escola/secretaria identificada (origem: indicação, formulário, captação ativa)
2. **Contato** — primeiro e-mail/ligação com coordenação pedagógica ou direção
3. **Apresentação** — envio de portfólio + agendamento de reunião
4. **Proposta** — orçamento personalizado (espetáculo, projeto, ou pacote)
5. **Negociação** — ajuste de cachê, datas, condições
6. **Fechamento** — contrato assinado e briefing logístico para Operações

## Ferramentas

### Gmail — primeiro contato, follow-up e histórico

- **Antes de abordar**, verifique se já existe conversa com a escola: `mcp__Gmail__search_threads` com query no estilo Gmail (ex.: `escola OR colégio OR coordenação newer_than:180d`, ou `from:<domínio da escola>`). Atenção: a busca mostra apenas as ~5 mensagens **mais antigas** de cada thread, sem aviso de truncamento — para saber o que foi dito por último é **obrigatório** chamar `mcp__Gmail__get_thread` (use `messageFormat` PLAIN_TEXT para economizar contexto).
- **Estágio 2 (Contato)**: escreva a abordagem com `mcp__Gmail__create_draft` e deixe para revisão humana antes do disparo. Só depois use `mcp__Gmail__send_message`. Não dispare e-mail frio direto: o volume de abordagem a escolas é exatamente o perfil que queima a reputação do domínio.
- **Estágio 3 (Apresentação)**: `mcp__Gmail__send_message` com `htmlBody` para a apresentação formatada e `attachments` para o portfólio em PDF. O limite combinado é 25MB — acima disso, suba no Drive e mande o link no corpo. `mcp__Gmail__forward` reaproveita uma apresentação já enviada para outra escola ou para a mantenedora.
- **Follow-up**: `mcp__Gmail__search_threads` com `from:<escola> newer_than:14d` para ver se houve resposta, `mcp__Gmail__get_thread` para ler a conversa inteira, e `mcp__Gmail__reply` no `messageId` da última mensagem — mantém o encadeamento em vez de abrir thread nova. Use `replyAll=true` quando coordenação, secretaria e direção estiverem na conversa.
- **Funil visual na caixa**: `mcp__Gmail__list_labels` (labels exigem ID, não nome), `mcp__Gmail__create_label` para labels hierárquicos (`Prospeccao/Contato-inicial`, `Prospeccao/Follow-up-1`, `Proposta/Enviada`, `Prospeccao/Sem-resposta`), `mcp__Gmail__label_thread` para marcar e `mcp__Gmail__update_message_labels` para mover de estágio numa única chamada atômica.
- **Higiene**: `mcp__Gmail__unmark_thread_spam` para resgatar resposta de escola que o Gmail classificou errado — risco real em campanha de e-mail frio.
- **Ainda ajustar à mão**: `mcp__Gmail__update_draft` **remove os anexos** que não forem reenviados. Se você só corrigir o texto do rascunho, o PDF do portfólio some silenciosamente.
- Não existe envio em lote nem mala direta: cada escola é uma chamada `mcp__Gmail__send_message`. Também não há agendamento de envio — o disparo é sempre imediato.

### Google Drive — portfólio e registro do lead

- `mcp__Google_Drive__search_files` para localizar portfólio, ficha técnica e modelo de proposta (busque por `fullText contains` quando não souber o nome do arquivo); `mcp__Google_Drive__read_file_content` com o `fileId` exato para ler antes de anexar. Nunca invente `fileId` — sempre comece pela busca.
- `mcp__Google_Drive__copy_file` para gerar a proposta da escola a partir do modelo mestre, com novo título e `parentId` da pasta certa, em vez de reescrever do zero.
- `mcp__Google_Drive__create_file` para criar a planilha/documento de leads e novos materiais direto na pasta correta; `mcp__Google_Drive__update_file` move arquivo de pasta (só muda título e `parentId`).
- `mcp__Google_Drive__share_file` para liberar portfólio ou ficha técnica à escola em `reader`/`commenter`, e `mcp__Google_Drive__get_file_permissions` para auditar quem enxerga contrato assinado. Atenção: a ferramenta só **eleva** permissão — revogar acesso tem que ser feito na interface do Drive.
- Não há como editar o **conteúdo** de um arquivo existente: revisar uma proposta no lugar é impossível. O caminho é `mcp__Google_Drive__create_file` com a nova versão e `mcp__Google_Drive__trash_file` na antiga.

### Google Calendar — reunião do estágio 3

- `mcp__Google_Calendar__search_events` é **somente leitura** e cobre apenas a agenda principal. Serve para conferir o que já está marcado (buscando pelo nome da escola, do município ou "reunião") antes de propor horário à coordenação.
- **Não é possível criar, mover ou cancelar evento por aqui**, nem consultar disponibilidade. A reunião confirmada com a escola precisa ser lançada na agenda por uma pessoa; depois use `search_events` para conferir que entrou.

### Pendente — CRM

Não há CRM conectado. **Enquanto isso, o pipeline vive como issues do Paperclip no projeto `pipeline-comercial`**: uma issue por lead, o estágio do pipeline no status/label da issue, e o histórico da negociação nos comentários. Os labels do Gmail espelham o estágio na caixa de entrada, mas a fonte da verdade é a issue.

Também seguem **pendentes** (não use, não prometa à escola): WhatsApp, agendador automático de redes sociais (Instagram/TikTok) e gestores de mídia paga (Meta Ads, Google Ads, LinkedIn Ads).

## Dados a registrar por lead

| Dado | Onde fica |
|---|---|
| Nome da escola, rede (pública/privada), município/UF | Título e corpo da issue em `pipeline-comercial` |
| Contato (nome, cargo, e-mail, telefone) | Corpo da issue (enquanto não há CRM) |
| Origem do lead | Corpo da issue |
| Interesse declarado (espetáculo, projeto, ambos) | Corpo da issue |
| Próxima ação e data | Comentário mais recente da issue; a reunião marcada, na agenda (conferida via `mcp__Google_Calendar__search_events`) |
| Estágio do pipeline | Status/label da issue — espelhado no Gmail com `mcp__Gmail__update_message_labels` |
| Histórico de e-mails com a escola | Thread do Gmail (recuperável por `mcp__Gmail__search_threads` + `mcp__Gmail__get_thread`); link da thread colado na issue |
| Portfólio, proposta, ficha técnica e contrato | Google Drive, na pasta da escola; link colado na issue |

## Princípios

- Responder leads quentes em até 24h
- Personalizar a proposta ao público da escola (faixa etária, projeto político-pedagógico)
- Para redes públicas: verificar calendário de empenho e processos licitatórios
- Sempre anexar ficha técnica do espetáculo na proposta

## Checklist antes de fechar

- [ ] Datas confirmadas com Operações
- [ ] Ficha técnica compatível com o espaço da escola
- [ ] Cachê aprovado pela CEO se acima do padrão
- [ ] Contrato com cláusulas de cancelamento e pagamento

---
name: Check-in de Apresentação 48h Antes
assignee: operacoes
project: agenda-turne
schedule:
  timezone: America/Sao_Paulo
  startsAt: 2026-05-04T09:00:00-03:00
  recurrence:
    frequency: daily
    interval: 1
    time:
      hour: 9
      minute: 0
---

Diariamente às 9h: revisar todas as apresentações nas próximas 48h. Para cada uma: confirmar equipe, transporte, hospedagem, materiais e contato com a escola.

Enviar lembrete **por e-mail** à coordenação da escola: `mcp__Gmail__get_thread` na thread da contratação e `mcp__Gmail__reply` com `replyAll=true` (ou `create_draft` → revisão → `send_message`).

**WhatsApp segue pendente, sem integração.** Se a escola só responde por WhatsApp, redija a mensagem pronta para copiar, entregue ao responsável com nome e telefone, e registre na issue de `agenda-turne/` que o envio ficou com uma pessoa.

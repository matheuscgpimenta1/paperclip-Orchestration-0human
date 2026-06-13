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

1. **Confirmação** — confirma data com a escola e bloqueia agenda no Calendar
2. **Equipe** — escala elenco e técnica; confirma disponibilidade individual
3. **Deslocamento** — define modal (van, carro, avião), reserva, distribui passagens
4. **Hospedagem/alimentação** — se aplicável, reserva e fecha por pernoite
5. **Materiais** — lista de equipamento, cenário, figurino e encartes para a escola
6. **Comunicação com a escola** — confirma horário de chegada, tempo de montagem, espaço, contato no local
7. **Execução** — equipe em rota; ponto focal disponível para imprevistos
8. **Pós-evento** — relatório curto: o que funcionou, ocorrências, sugestões

## Padrões

- Toda apresentação vira issue child no projeto `agenda-turne/`
- Bloqueio em Calendar inclui: equipe, equipamento, contato da escola, endereço
- Briefing logístico para a escola enviado por e-mail/WhatsApp 7 dias antes
- Lembrete automático 48h antes (rotina)

## Gates de escalonamento

- Conflito de data entre apresentações → CEO decide prioridade
- Atraso de pagamento da escola → Comercial reativa relacionamento; Operações pausa logística

## Checklist por apresentação

- [ ] Contrato + ficha técnica em mãos
- [ ] Equipe confirmada
- [ ] Transporte e hospedagem reservados
- [ ] Briefing enviado para escola
- [ ] Bloqueio no Calendar
- [ ] Issue child criada em `agenda-turne/`

# Ensino em Cena — Agent Company

Pacote de empresa-agente (Agent Company) modelando a **Ensino em Cena**, empresa que produz espetáculos teatrais educativos e projetos de leitura e escrita para escolas.

Site: https://ensinoemcena.com.br

## Como a empresa opera

Modelo **hub-and-spoke**: a CEO recebe demandas (escolas, secretarias, ideias de produção) e delega para seis especialistas independentes que reportam diretamente a ela.

```
                    ┌─────────┐
                    │   CEO   │
                    └────┬────┘
       ┌────────┬────────┼────────┬────────┬────────┐
       ▼        ▼        ▼        ▼        ▼        ▼
   Diretor   Coord.   Produtor  Marketing Comercial Operações
  Artístico Pedagógico Conteúdo
```

## Arquitetura de 6 camadas

```
┌─────────────────────────────────────────────────────────────────────┐
│ 6. GOVERNANÇA      Budget mensal por agente · gates de aprovação    │
│                    (cachê com desconto >10%, novo espetáculo,        │
│                    campanha paga >R$3k, parceria >R$50k)             │
├─────────────────────────────────────────────────────────────────────┤
│ 5. INTEGRAÇÕES     Canva · Google Calendar · Drive · Email           │
│                    WhatsApp · CRM · Social scheduler · Ads manager   │
├─────────────────────────────────────────────────────────────────────┤
│ 4. CONHECIMENTO    library/ — catálogo, tabela de cachê, BNCC,       │
│                    brand kit, templates, playbooks                   │
├─────────────────────────────────────────────────────────────────────┤
│ 3. SKILLS          9 skills cobrindo criação, pedagogia, vendas,     │
│                    marketing, operações e métricas                   │
├─────────────────────────────────────────────────────────────────────┤
│ 2. AGENTES         CEO + 6 especialistas (estrutura flat)            │
├─────────────────────────────────────────────────────────────────────┤
│ 1. DADOS VIVOS     Issues do Paperclip — pipeline, contratos,        │
│                    agenda de turnê, catálogo em evolução, editorial  │
└─────────────────────────────────────────────────────────────────────┘
```

## Organograma e skills por agente

| Agente | Cargo | Reporta a | Skills |
|--------|-------|-----------|--------|
| `ceo` | Diretora Geral | — | `relatorio-metricas` |
| `diretor-artistico` | Direção Artística | ceo | `writing-roteiro-teatral` |
| `coordenador-pedagogico` | Coordenação Pedagógica | ceo | `plano-pedagogico-bncc` |
| `produtor-conteudo` | Produção de Conteúdo | ceo | `writing-roteiro-teatral`, `plano-pedagogico-bncc` |
| `marketing` | Marketing e Comunicação | ceo | `divulgacao-espetaculo`, `gestao-redes-sociais`, `gestao-anuncios`, `relatorio-metricas` |
| `comercial` | Comercial | ceo | `prospeccao-escolas`, `orcamento-proposta` |
| `operacoes` | Produção Executiva | ceo | `logistica-turne` |

## Skills (9)

### Criação
- `writing-roteiro-teatral` — escrita/revisão de roteiros para o circuito escolar
- `plano-pedagogico-bncc` — projetos pedagógicos alinhados à BNCC

### Mercado
- `prospeccao-escolas` — pipeline comercial para escolas e secretarias
- `orcamento-proposta` — orçamento e proposta comercial
- `divulgacao-espetaculo` — peças de divulgação para temporadas
- `gestao-redes-sociais` — planejamento e publicação automatizada
- `gestao-anuncios` — campanhas pagas Meta/Google/LinkedIn

### Operação
- `logistica-turne` — agenda, deslocamento, hospedagem, equipe

### Inteligência
- `relatorio-metricas` — relatórios consolidados por área e período

## Projetos (8)

### Projetos contínuos (guarda-chuvas de dados vivos)
- `pipeline-comercial` — leads em qualquer estágio (Comercial)
- `contratos-ativos` — contratos assinados em execução (Operações)
- `agenda-turne` — apresentações agendadas, sincroniza Calendar (Operações)
- `catalogo-em-evolucao` — novos espetáculos/projetos em concepção (Diretor Artístico)
- `calendario-editorial` — planejamento mensal de conteúdo (Marketing)

### Projetos de meta/temporada
- `temporada-espetaculos` — espetáculos em circulação (Diretor Artístico)
- `lancamento-projeto-leitura` — novo projeto Fund. II (Coord. Pedagógico)
- `captacao-q2` — meta comercial Q2 (Comercial)

## Tarefas recorrentes (6)

| Tarefa | Cadência | Owner |
|--------|----------|-------|
| `reuniao-semanal-status` | Seg 9h BRT | CEO |
| `revisao-pipeline-comercial` | Sex 10h BRT | Comercial |
| `checkin-turne-48h` | Diário 9h | Operações |
| `planejamento-conteudo-mensal` | Dia 25, 10h | Marketing |
| `relatorio-mensal-captacao` | Dia 30, 17h | Comercial |
| `relatorio-trimestral-ceo` | Trimestral dia 30, 15h | CEO |

## Integrações (configuradas em `.paperclip.yaml`)

| Integração | Agentes plugados | Status |
|-----------|------------------|--------|
| Canva | Marketing, Produtor Conteúdo, Diretor Artístico | ✅ disponível no ambiente |
| Google Calendar | CEO, Operações, Comercial | ✅ disponível no ambiente |
| Google Drive | Todos | ✅ disponível no ambiente |
| Email | CEO, Comercial, Marketing, Operações | 🟡 requer conta institucional |
| WhatsApp | Comercial, Operações | 🟡 requer Business API |
| CRM | Comercial, Marketing, CEO | 🟡 definir provedor |
| Social scheduler | Marketing | 🟡 ex.: Meta Business Suite |
| Ads manager | Marketing | 🟡 contas Meta/Google/LinkedIn |

## Governança

Gates de aprovação codificados em `.paperclip.yaml`:

- Desconto de cachê > 10% → aprovação da CEO
- Novo espetáculo → aprovação da CEO
- Campanha paga > R$ 3.000/mês → aprovação da CEO
- Parceria/contrato > R$ 50.000 → aprovação da CEO

Budget mensal por agente também declarado em `governance.budget`.

## Base de conhecimento (`library/`)

Esqueletos prontos para a Fernanda e especialistas preencherem:

```
library/
├── catalogo-espetaculos/          Cada espetáculo um arquivo (+_template.md)
├── catalogo-projetos-leitura/     Cada projeto pedagógico um arquivo (+_template.md)
├── tabela-cache/                  Preços por tipo, segmento, raio
├── bncc/                          Competências gerais + por etapa
├── brand-kit/                     Identidade visual, voz e tom, hashtags
├── templates/                     proposta, contrato, ficha-técnica, release,
│                                  briefing logístico, encarte, guia do professor
└── playbooks/                     onboarding-escola, pos-evento
```

## Estrutura do pacote

```
ensino-em-cena/
├── COMPANY.md
├── .paperclip.yaml                Integrações + governança + inputs
├── LICENSE
├── README.md
│
├── agents/                        7 agentes (CEO + 6 especialistas)
│   ├── ceo/AGENTS.md
│   ├── diretor-artistico/AGENTS.md
│   ├── coordenador-pedagogico/AGENTS.md
│   ├── produtor-conteudo/AGENTS.md
│   ├── marketing/AGENTS.md
│   ├── comercial/AGENTS.md
│   └── operacoes/AGENTS.md
│
├── skills/                        9 skills customizadas
│   ├── writing-roteiro-teatral/SKILL.md
│   ├── plano-pedagogico-bncc/SKILL.md
│   ├── prospeccao-escolas/SKILL.md
│   ├── orcamento-proposta/SKILL.md
│   ├── divulgacao-espetaculo/SKILL.md
│   ├── gestao-redes-sociais/SKILL.md
│   ├── gestao-anuncios/SKILL.md
│   ├── logistica-turne/SKILL.md
│   └── relatorio-metricas/SKILL.md
│
├── projects/                      8 projetos (contínuos + temporada)
│   ├── pipeline-comercial/PROJECT.md
│   ├── contratos-ativos/PROJECT.md
│   ├── agenda-turne/PROJECT.md
│   ├── catalogo-em-evolucao/PROJECT.md
│   ├── calendario-editorial/PROJECT.md
│   ├── temporada-espetaculos/PROJECT.md
│   ├── lancamento-projeto-leitura/PROJECT.md
│   └── captacao-q2/PROJECT.md
│
├── tasks/                         6 tarefas recorrentes
│   ├── reuniao-semanal-status/TASK.md
│   ├── revisao-pipeline-comercial/TASK.md
│   ├── checkin-turne-48h/TASK.md
│   ├── planejamento-conteudo-mensal/TASK.md
│   ├── relatorio-mensal-captacao/TASK.md
│   └── relatorio-trimestral-ceo/TASK.md
│
└── library/                       Base de conhecimento (esqueletos)
    ├── catalogo-espetaculos/
    ├── catalogo-projetos-leitura/
    ├── tabela-cache/
    ├── bncc/
    ├── brand-kit/
    ├── templates/
    └── playbooks/
```

## Fluxo end-to-end (escola → palco → fidelização)

```
1. Marketing capta lead via redes/anúncios     →  cria issue em pipeline-comercial
2. Comercial qualifica e envia proposta        →  usa skill orcamento-proposta + template
3. CEO aprova se acima do gate                 →  board_approval
4. Comercial fecha contrato                    →  move para contratos-ativos
5. Operações planeja logística                 →  cria issues em agenda-turne + Calendar
6. Diretor Artístico + equipe executam         →  apresentação
7. Operações registra pós-evento               →  playbook pos-evento
8. Comercial faz follow-up de fidelização      →  retroalimenta pipeline-comercial
```

## Getting Started

Para importar no Paperclip:

```bash
paperclipai company import --from ./companies/ensino-em-cena
```

Depois preencher `library/` com dados reais (catálogo, cachês, brand kit) e configurar credenciais das integrações conforme `.paperclip.yaml`.

## Referências

- [Agent Companies Specification](https://agentcompanies.io/specification)
- [Paperclip](https://github.com/paperclipai/paperclip)

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
│ 5. INTEGRAÇÕES     live: Gmail · Drive · Calendar (leitura) · Canva │
│                    Adobe Fonts/PDF · Freepik · Vercel               │
│                    pendente: WhatsApp · CRM · social scheduler ·    │
│                    ads manager  ·  mapa: library/integracoes.md     │
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
- `gestao-redes-sociais` — planejamento editorial e produção das peças; **publicação manual** (sem agendador conectado)
- `gestao-anuncios` — pacote de campanha paga Meta/Google/LinkedIn pronto para upload; **veiculação manual**

### Operação
- `logistica-turne` — agenda, deslocamento, hospedagem, equipe

### Inteligência
- `relatorio-metricas` — relatórios consolidados por área e período

## Projetos (8)

### Projetos contínuos (guarda-chuvas de dados vivos)
- `pipeline-comercial` — leads em qualquer estágio (Comercial)
- `contratos-ativos` — contratos assinados em execução (Operações)
- `agenda-turne` — apresentações agendadas; **reconciliado** com o Calendar por `search_events` (leitura), lançamento no Calendar é manual (Operações)
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

> Mapa completo de ferramentas, limites e armadilhas de cada servidor: [`library/integracoes.md`](library/integracoes.md).

| Integração | Servidor MCP | Agentes plugados | Status |
|-----------|--------------|------------------|--------|
| E-mail (Gmail) | `mcp__Gmail__*` | CEO, Comercial, Marketing, Operações | ✅ live |
| Google Drive | `mcp__Google_Drive__*` | Todos | ✅ live |
| Google Calendar | `mcp__Google_Calendar__*` | CEO, Operações, Comercial | ✅ live (somente leitura) |
| Canva | `mcp__Canva__*` | Marketing, Produtor Conteúdo, Diretor Artístico | ✅ live |
| Adobe (fontes + PDF) | `mcp__Adobe_for_creativity__*` | Marketing, Produtor Conteúdo | ✅ live |
| Freepik / Magnific (imagem, vídeo, áudio, legendas) | `mcp__Magnific__*` | Marketing, Diretor Artístico | ✅ live |
| Site + analytics (Vercel) | `mcp__Vercel__*` | Marketing, CEO | ✅ live |
| WhatsApp | — | Comercial, Operações | 🟡 pendente — requer Business API |
| CRM | — | Comercial, Marketing, CEO | 🟡 pendente — definir provedor |
| Social scheduler (publicação automática Instagram/TikTok) | — | Marketing | 🟡 pendente — sem conector |
| Ads manager (Meta/Google/LinkedIn) | — | Marketing | 🟡 pendente — sem conector |

Notas de escopo do que está live:

- **Google Calendar** expõe apenas `mcp__Google_Calendar__search_events` (agenda primária, busca semântica). Dá para **consultar e conferir** a agenda de turnê; **criar, mover ou cancelar** data continua sendo trabalho humano no Calendar.
- **Gmail** não tem disparo em lote, agendamento de envio nem leitura de contatos: prospecção e release saem de `create_draft` → revisão humana → `send_message`, uma escola por vez. Para ler o retorno mais recente de uma thread, sempre `get_thread` (a busca só mostra as mensagens mais antigas).
- **Google Drive** lê, cria, copia, move e compartilha arquivos, mas **não edita conteúdo de arquivo existente** e só sobe permissão (não revoga) — importante para contratos e fotos de alunos.
- **Canva** edita designs de ponta a ponta, porém **não exporta**: o entregável sai como link de design. Para PDF impresso, seguir pelas ferramentas de PDF do servidor Adobe.
- **Vercel** cobre domínio, deploy e analytics de `ensinoemcena.com.br` — atribuição de lead depende de UTM configurada **antes** da campanha.
- Sem social scheduler e sem ads manager conectados, as skills `gestao-redes-sociais` e `gestao-anuncios` produzem o material e o plano; a **publicação e o disparo de mídia paga são manuais**.

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
5. Operações planeja logística                 →  cria issues em agenda-turne; dados do
                                                  evento vão para lançamento manual no Calendar
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

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

## Organograma

| Agente | Cargo | Reporta a | Skills |
|--------|-------|-----------|--------|
| `ceo` | Diretora Geral | — | — |
| `diretor-artistico` | Direção Artística | ceo | `writing-roteiro-teatral` |
| `coordenador-pedagogico` | Coordenação Pedagógica | ceo | `plano-pedagogico-bncc` |
| `produtor-conteudo` | Produção de Conteúdo | ceo | `writing-roteiro-teatral`, `plano-pedagogico-bncc` |
| `marketing` | Marketing e Comunicação | ceo | `divulgacao-espetaculo` |
| `comercial` | Comercial | ceo | `prospeccao-escolas` |
| `operacoes` | Produção Executiva | ceo | — |

## Projetos

- `temporada-espetaculos` — espetáculos em circulação (owner: diretor-artistico)
- `lancamento-projeto-leitura` — novo projeto de leitura/escrita Fund. II (owner: coordenador-pedagogico)
- `captacao-q2` — meta comercial Q2 (owner: comercial)

## Skills customizadas

- `writing-roteiro-teatral` — escrita/revisão de roteiros para o circuito escolar
- `plano-pedagogico-bncc` — projetos pedagógicos alinhados à BNCC
- `prospeccao-escolas` — pipeline comercial para escolas e secretarias
- `divulgacao-espetaculo` — peças de divulgação para temporadas

## Tarefas recorrentes

- `reuniao-semanal-status` — segunda 9h BRT (CEO)
- `relatorio-mensal-captacao` — dia 30 17h BRT (Comercial)

## Estrutura

```
ensino-em-cena/
├── COMPANY.md
├── agents/
│   ├── ceo/AGENTS.md
│   ├── diretor-artistico/AGENTS.md
│   ├── coordenador-pedagogico/AGENTS.md
│   ├── produtor-conteudo/AGENTS.md
│   ├── marketing/AGENTS.md
│   ├── comercial/AGENTS.md
│   └── operacoes/AGENTS.md
├── skills/
│   ├── writing-roteiro-teatral/SKILL.md
│   ├── plano-pedagogico-bncc/SKILL.md
│   ├── prospeccao-escolas/SKILL.md
│   └── divulgacao-espetaculo/SKILL.md
├── projects/
│   ├── temporada-espetaculos/PROJECT.md
│   ├── lancamento-projeto-leitura/PROJECT.md
│   └── captacao-q2/PROJECT.md
├── tasks/
│   ├── reuniao-semanal-status/TASK.md
│   └── relatorio-mensal-captacao/TASK.md
├── .paperclip.yaml
├── LICENSE
└── README.md
```

## Getting Started

Para importar este pacote no Paperclip:

```bash
paperclipai company import --from ./companies/ensino-em-cena
```

## Referências

- [Agent Companies Specification](https://agentcompanies.io/specification)
- [Paperclip](https://github.com/paperclipai/paperclip)

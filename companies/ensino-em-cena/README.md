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

| Agente | Cargo | Reporta a | Foco |
|--------|-------|-----------|------|
| `ceo` | Diretora Geral | — | Estratégia, parcerias, delegação |
| `diretor-artistico` | Direção Artística | ceo | Concepção e direção dos espetáculos |
| `coordenador-pedagogico` | Coordenação Pedagógica | ceo | Projetos de leitura/escrita, alinhamento BNCC |
| `produtor-conteudo` | Produção de Conteúdo | ceo | Roteiros, guias do professor, encartes |
| `marketing` | Marketing e Comunicação | ceo | Site, redes sociais, captação de leads |
| `comercial` | Comercial | ceo | Vendas para escolas e secretarias |
| `operacoes` | Produção Executiva | ceo | Logística de turnê, agendamentos |

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

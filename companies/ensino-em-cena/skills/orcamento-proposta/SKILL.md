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

## Checklist antes de enviar

- [ ] Ficha técnica compatível com o espaço da escola
- [ ] Datas pré-validadas com Operações
- [ ] Template de proposta atualizado em `library/templates/proposta-comercial.md`
- [ ] PDF gerado com identidade visual (brand kit)
- [ ] Registro no pipeline (projeto `pipeline-comercial`)

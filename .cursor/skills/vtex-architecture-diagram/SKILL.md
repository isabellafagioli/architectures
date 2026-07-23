---
name: vtex-architecture-diagram
description: >-
  Cria ou atualiza diagramas HTML de arquitetura de referência VTEX (camadas,
  cores oficiais, setas síncronas/assíncronas, estilo flat rosa/cinza). Use when
  the user asks for VTEX architecture, reference architecture, diagrama de
  arquitetura VTEX, Basic B2B/B2C/marketplace diagrams, or Axia-like HTML boards.
---

# Skill: Diagrama de arquitetura VTEX

## Quando usar

- Pedidos de arquitetura VTEX, reference architecture, diagrama HTML
- Adaptar um fluxo de negócio (ERP, pagamento, OMS, terceiros) ao padrão VTEX
- Ajustar visual para ficar parecido com os boards oficiais (rosa suave, cinza, flat)

## Fonte oficial

Seguir [Understanding VTEX reference architectures](https://developers.vtex.com/docs/guides/understanding-vtex-reference-architectures).

## Convenção obrigatória (cores e setas)

| Elemento | Visual |
| --- | --- |
| Nativo VTEX (infra VTEX) | Fundo rosa suave + borda rosa |
| Customização na infra VTEX | Fundo branco + borda rosa |
| Terceiros (fora da VTEX) | Fundo branco + borda preta/escura |
| Back office (fora da VTEX) | Fundo cinza + borda preta/escura |
| Módulo opcional (ex.: Camada de Integração) | Borda tracejada |
| Chamada síncrona | Seta azul (fina, ortogonal) |
| Chamada assíncrona | Seta preta/cinza escura (fina, ortogonal) |
| Processo manual | Seta tracejada âmbar/marrom |

## Camadas (macro)

Montar nesta ordem visual típica:

1. **Canais do lojista** (storefront, app, calculadora, etc.)
2. **Serviços principais VTEX** (módulos nativos em pills rosa)
3. **Terceiros** (pagamento, calculadoras, apps externos — com logo quando houver)
4. **Camada de Integração** (opcional, tracejada)
5. **Back office do lojista** (ERP, WMS, certificação, etc.)

Idioma padrão: **português** (labels de camada e fluxos). Nomes de produto (OMS, VTEX, SAP) podem ficar no original.

## Estilo visual (boards oficiais)

- Fundo com **grid sutil** cinza claro
- Tudo **flat**: sem glow, sem sombra pesada, sem “UI dashboard”
- Cantos arredondados em painéis e módulos
- Tipografia limpa sans-serif de sistema (evitar Inter/Roboto como “marca”; pode usar system stack)
- Rosa desaturado (`#F7C6D6` / borda `#E89BB3`) — não roxo
- Título simples acima do diagrama; legenda compacta
- Conectores **ortogonais** (só eixos X/Y), finos, sem atravessar texto/caixas
- Rotas de seta apenas nos **vãos** entre painéis; caixas com `z-index` acima do SVG
- Conexões internas (ex.: Checkout → OMS) podem ser HTML no vão interno, não overlay sobre pills

## Conteúdo do HTML

Entregar **um arquivo HTML autocontido** (CSS + JS + logos SVG inline quando possível) em pasta do projeto, ex.:

`{cliente-ou-caso}/arquitetura-vtex-{nome}.html`

Incluir:

1. Título + 1–2 frases do caso
2. Legenda (cores + tipos de seta)
3. Diagrama com as 4–5 camadas
4. Faixa **Fluxo principal** numerada (passo a passo do negócio)
5. Destaque nos módulos realmente envolvidos no fluxo (cor de highlight suave, não badge flutuante no hero)

## Fluxo de trabalho

1. Extrair do pedido: atores externos, módulos VTEX, ERP/back office, sync vs async vs manual
2. Mapear cada ator para a camada correta da convenção
3. Desenhar o HTML no estilo flat rosa/cinza
4. Desenhar setas só em corredores; validar que não cobrem labels
5. Se houver “explicação de regra de negócio” (ex.: Promoções), manter o **módulo** na grade e a explicação como texto simples ao lado/abaixo — não duplicar como segundo card de módulo
6. Abrir/validar o HTML; commitar na pasta do caso

## Checklist rápido

- [ ] Cores batem com a tabela oficial
- [ ] Setas ortogonais e tipadas (sync/async/manual)
- [ ] Nada de seta por cima de texto ou logo
- [ ] Português nas camadas e no fluxo
- [ ] HTML abre sozinho (logos embutidos ou paths relativos corretos)
- [ ] Visual flat próximo aos boards de referência VTEX

## Exemplo de mapeamento

```
CEPEL (terceiro, sync) → Checkout (VTEX)
Banco do Brasil (terceiro, sync) → Hub de Pagamentos (VTEX)
Checkout → OMS (async, interno VTEX)
OMS → SAP (async, via camada de integração / API aberta)
SAP → Reckify (manual, pós-venda)
Promoções = módulo VTEX + texto de regra de negócio
```

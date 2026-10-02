---
name: react-state-architecture
description: Orienta o desenho e revisa a arquitetura de estado em aplicações React, cobrindo colocation, estado derivado, distinção entre UI state e server state, imutabilidade e prevenção de re-renderizações por estado mal posicionado. Use quando o usuário for projetar ou revisar onde e como o estado é gerenciado numa aplicação React. Para a estratégia de busca dos dados em si, use data-fetching.
---

# React State Architecture

Orienta o desenho e revisa implementações existentes. Em revisão, apenas relata. Quando o usuário pedir a implementação, estes critérios orientam o código.

Não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## Categorias de estado

Identificar a categoria de cada pedaço de estado é o primeiro passo para decidir onde e como gerenciá-lo:

- **Estado local de UI**: valores que controlam o comportamento visual de um único componente ou de uma subárvore pequena — modal aberto/fechado, tab ativa, campo de busca controlado, índice do carrossel. Fica no componente mais próximo que o usa.
- **Estado compartilhado da aplicação**: valores que precisam ser acessados ou modificados por componentes em partes diferentes da árvore e que representam decisões da sessão do usuário — usuário autenticado, tema, idioma, carrinho de compras. Fica num contexto, store ou nível suficientemente alto na árvore.
- **Dados remotos (server state)**: dados buscados de uma API ou serviço externo. Têm características próprias: validade temporal, necessidade de revalidação, estados de loading/error/success. Misturá-los com estado de UI cria acoplamento desnecessário.

## Regras

### Estado local primeiro

- O estado começa local. Só sobe na árvore (lifting) quando um segundo componente independente precisar lê-lo ou modificá-lo.
- Não criar contexto ou store global por conveniência ou para evitar prop drilling de dois níveis. Estado global tem custo de manutenção proporcional ao seu escopo.

### Colocation

- Estado fica no nível mais baixo da árvore onde ele é realmente necessário. Estado colocado mais alto do que o necessário causa re-renderizações em componentes que não o usam.
- Quando o estado sobe para permitir compartilhamento, mover para o ancestral comum mais próximo dos consumidores, não para o topo da aplicação.

### Estado derivado

- Não armazenar em estado o que pode ser calculado de outras variáveis de estado ou props. Exemplos: lista filtrada, total de itens, valor formatado, flag derivada de outro estado.
- Estado derivado armazenado cria duas fontes de verdade: os valores podem divergir quando uma é atualizada e a outra não. O cálculo acontece durante a renderização ou num `useMemo` quando o custo computacional for real e mensurável.
- `useMemo` para estado derivado é justificado quando o cálculo é genuinamente custoso (ordenação ou transformação de listas grandes) e o profiling confirma o custo. Não adicionar `useMemo` por precaução.

### Imutabilidade

- Estado nunca é mutado diretamente. Arrays e objetos são substituídos por novas referências.
- Mutação direta (ex.: `state.items.push(item)`, `state.user.name = 'novo'`) não dispara re-renderização e causa bugs difíceis de rastrear.
- Para objetos de estado complexos com atualizações frequentes, considerar utilitários de atualização imutável se o projeto já os usa. Não introduzir biblioteca nova só para isso.

### Efeitos colaterais em handlers

- Lógica de negócio que roda em resposta a uma ação do usuário fica no handler do evento, não num `useEffect` que observa mudanças de estado.
- Múltiplas chamadas a `setState` num mesmo handler são agrupadas pelo React em um único re-render (em eventos sintéticos e dentro de `startTransition`). Não usar `useEffect` para encadear atualizações de estado que deveriam acontecer juntas.

### Dados remotos

- Dados buscados de APIs têm seus estados de ciclo de vida (loading, error, success, stale) representados explicitamente — como estado local, como contexto ou por uma biblioteca de gerenciamento de server state.
- Ferramentas como TanStack Query, SWR ou equivalentes resolvem deduplicação, cache, revalidação e estados de ciclo de vida de forma consistente. São opções a considerar quando o projeto lida com muitas fontes de dados remotas ou quando a lógica de cache manual se torna complexa. A adoção é avaliada com base na necessidade concreta e passa por `dependency-review`.
- Não misturar dados remotos em stores de UI state: um store que gerencia o tema e o idioma não deve também ser o cache de dados da API.

### Estado global e contexto

- Contexto do React é adequado para estado que muda raramente e é lido por muitos componentes (tema, idioma, usuário autenticado). Para estado que muda com frequência, contexto causa re-renderizações em todos os consumidores; avaliar alternativas.
- Stores externos (Zustand, Jotai, Redux e similares) têm mecanismos próprios de seleção de estado que evitam re-renderizações desnecessárias. A escolha entre eles é avaliada com base na necessidade concreta do projeto e passa por `dependency-review`.
- Não introduzir gerenciador de estado global por conveniência quando colocation e lifting resolvem o problema.

## Revisão

O que verificar em código existente:

- Estado derivado armazenado (duas fontes de verdade).
- Estado posicionado mais alto do que o necessário (re-renderizações desnecessárias).
- Estado de dados remotos misturado com estado de UI.
- Mutação direta de estado.
- `useEffect` para encadear atualizações de estado que deveriam ser feitas juntas num handler.
- Estado global criado por conveniência sem necessidade real de compartilhamento amplo.

## Severidade

- 🛑 **Bloqueante**: mutação direta de estado que causa comportamento incorreto silencioso, ou `useEffect` que cria loop por atualizar estado que está nas suas próprias dependências.
- ⚠️ **Importante**: estado derivado armazenado com risco real de inconsistência, estado global introduzido sem necessidade de compartilhamento amplo, dados remotos misturados com UI state de forma que dificulta tratamento de loading/error.
- 💡 **Sugestão**: melhoria opcional de colocation, extração de lógica derivada ou reorganização de stores.

## Formato da saída

Para cada achado: severidade, `arquivo:linha`, problema, impacto e correção recomendada. Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente.

## Concluído quando

A revisão foi entregue no formato acima ou, em orientação de desenho, a abordagem recomendada foi apresentada com as decisões em aberto.

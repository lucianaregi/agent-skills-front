---
name: react-component-review
description: Revisa componentes React com foco em bugs, anatomia e responsabilidades, uso correto de useEffect, tipagem de props, colocação de estado, composição e contratos de componente. Use quando o usuário pedir revisão de código React, ou quando um diff tocar componentes, hooks customizados ou a estrutura de composição de UI. Para acessibilidade, use accessibility-review; para performance de re-renderização, use performance-review; para segurança, use security-review.
---

# React Component Review

Apenas relata. Não altera código sem pedido explícito.

Seguir as convenções que o projeto já adota (nomenclatura, organização de arquivos, padrões de composição). Apontar inconsistências internas e desvios de disciplina, não preferências de estilo não relacionadas à correção.

## O que verificar

### Anatomia e responsabilidades

- **Coesão**: um componente tem uma responsabilidade identificável. Componentes que orquestram dados (chamadas a serviços, transformações complexas) e ao mesmo tempo renderizam UI detalhada de apresentação tendem a crescer além do controlável e a dificultar testes. Avaliar se a separação faz sentido no contexto do projeto.
- **Tamanho como sinal**: componentes com aproximadamente 100 linhas ou mais são candidatos a avaliação de coesão e decomposição. O tamanho não é um limite mecânico — um componente grande e coeso é preferível a vários pequenos e artificiais.
- **Não criar artificialmente**: componentes ou hooks criados apenas para reduzir o tamanho de um arquivo, sem responsabilidade própria identificável, adicionam indireção sem benefício. A extração deve ser motivada por reutilização real, testabilidade ou coesão, não por contagem de linhas.

### Tipagem

- Props explicitamente tipadas com TypeScript. A interface ou o tipo de props é declarado próximo ao componente ou importado de um local estabelecido pelo projeto.
- Evitar `any` em props, retornos de hooks e variáveis de estado. Quando a tipagem completa for inviável no momento, usar `unknown` com narrowing explícito é preferível a `any` silencioso.
- Props opcionais têm valor padrão definido ou o componente trata explicitamente o caso `undefined`.

### useEffect

- `useEffect` é o mecanismo correto para sincronização com sistemas externos: subscriptions, timers, eventos do DOM, APIs do browser (foco, media queries), sincronização com bibliotecas externas ao React.
- **Não usar `useEffect` para**:
  - transformar dados que podem ser derivados durante a renderização — calcular um valor filtrado, formatado ou ordenado a partir de props ou estado é uma expressão na função de renderização, não um efeito;
  - sincronizar estado derivado — armazenar em estado algo que pode ser calculado de outras variáveis de estado ou props cria duplicação e risco de inconsistência;
  - responder a eventos de usuário — lógica que deve rodar quando o usuário clica, digita ou submete um formulário fica no handler do evento, não num efeito que observa mudanças de estado causadas pelo evento.
- Um `useEffect` com array de dependências vazio que executa lógica de negócio é um sinal de alerta: pode indicar que a lógica pertence ao servidor ou a um inicializador de módulo, não ao ciclo de vida de um componente.
- Dependências do `useEffect` refletem fielmente o que é usado dentro do efeito. Omitir dependências para "controlar quando o efeito roda" é uma supressão do comportamento esperado pelo React, não uma otimização.
- Efeitos que criam recursos externos (subscriptions, timers, event listeners) têm função de cleanup.

### Estado

- Estado colocado no nível mais baixo da árvore onde ele é realmente necessário (ver `react-state-architecture` para diretrizes detalhadas de arquitetura de estado).
- Não armazenar em estado o que pode ser derivado de outras variáveis de estado ou props: cria duas fontes de verdade que precisam ser mantidas sincronizadas.
- Estado inicial não derivado de props que mudam após a montagem. Props usadas como estado inicial são aceitáveis quando a intenção é claramente "valor inicial" (nome da prop indica isso ou comentário explica).

### Composição e contratos

- **Contrato de props**: props públicas representam o contrato do componente com seus consumidores. Mudanças que removem, renomeam ou alteram o tipo de props existentes são breaking changes e devem ser sinalizadas.
- **Prop drilling excessivo**: passar as mesmas props por três ou mais níveis de componentes intermediários que não as usam é um sinal de que o estado está no nível errado ou que a composição pode ser revista.
- **Children e composição**: quando um componente precisa ser flexível em seu conteúdo interno, `children` e padrões de composição (compound components, render props) são preferíveis a props que recebem JSX ou configuração complexa.
- **Chaves em listas**: elementos renderizados em listas têm `key` estável e única dentro da lista. Índice de array como `key` é aceitável apenas quando a lista nunca é reordenada e os itens não têm identidade própria.

### Renderização e efeitos colaterais

- Nenhum efeito colateral direto durante a renderização (chamadas a serviços, mutações de variáveis externas, `console.log` de diagnóstico deixado no código).
- Componentes são funções puras em relação aos seus inputs: dado o mesmo estado e as mesmas props, produzem o mesmo JSX.
- Chamadas a APIs e serviços externos ficam em handlers de evento, em `useEffect` ou em camadas de dados — nunca diretamente no corpo da função de renderização fora de condições de inicialização.

## Severidade

- 🛑 **Bloqueante**: bug confirmado causado por dependências omitidas em `useEffect`, efeito sem cleanup que causa memory leak, prop obrigatória sem tipagem que resulta em erro em runtime, ou loop de renderização causado por objeto/array criado inline em dependências de efeito.
- ⚠️ **Importante**: `useEffect` para cálculo derivado ou resposta a evento, estado duplicado que cria risco de inconsistência, prop drilling além de três níveis sem justificativa, `any` em contrato público de componente.
- 💡 **Sugestão**: melhoria opcional de coesão, composição ou clareza.

## Formato da saída

Para cada achado: severidade, `arquivo:linha`, problema, impacto e correção recomendada. Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente.

## Concluído quando

Os componentes no escopo foram revisados e o relatório foi entregue no formato acima.

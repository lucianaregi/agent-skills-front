---
name: react-testing-behavior
description: Cria e revisa testes de componentes React focados no comportamento observável pela pessoa que usa a interface, usando React Testing Library como abordagem principal. Use quando o usuário pedir testes de componentes React, ou para revisar testes existentes que testam detalhes de implementação em vez de comportamento. Para princípios gerais de teste independentes de framework, use testing.
---

# React Testing Behavior

Cria ou altera testes de componentes React. Não altera código de produção sem pedido.

Os princípios gerais de testes — comportamento não implementação, determinismo, asserções específicas, estrutura AAA — são cobertos pela skill `testing`. Esta skill os aplica ao contexto de componentes React com React Testing Library.

## Princípios

- **O que o usuário vê e faz**: testes verificam o que aparece na tela e o que acontece quando o usuário interage. Se um teste quebraria apenas com mudança de nome de variável interna, de estrutura de hook ou de detalhe de implementação sem mudança de comportamento visível, ele está testando o que não deveria.
- **Consultas semânticas**: a hierarquia de consultas do Testing Library reflete o que tecnologias assistivas e o usuário percebem:
  1. `getByRole` — preferida; corresponde à semântica HTML e ao ARIA (ex.: `getByRole('button', { name: 'Salvar' })`)
  2. `getByLabelText` — para campos de formulário associados a um label
  3. `getByPlaceholderText` — quando não há label (segunda opção, não primeira)
  4. `getByText` — para texto visível que não é label de controle
  5. `getByDisplayValue` — para campos com valor já preenchido
  6. `getByAltText` — para imagens
  7. `getByTitle` — evitar quando possível
  8. `getByTestId` — último recurso; indica que o elemento não tem semântica observável
- Não usar seletores baseados em classes CSS, IDs internos ou estrutura do DOM (ex.: `.querySelector`, `container.firstChild`) para localizar elementos nos testes.

## Interações

- Usar `userEvent` (de `@testing-library/user-event`) para simular interações de usuário: clique, digitação, tab, foco, hover. `userEvent` simula o comportamento real do browser com maior fidelidade que `fireEvent`.
- `fireEvent` é aceitável para eventos que `userEvent` não cobre ou em testes de integração onde a fidelidade completa não é necessária.
- Aguardar atualizações assíncronas com `waitFor`, `findBy*` ou `findAllBy*`. Não usar `act()` manualmente exceto em casos muito específicos de integração com APIs não suportadas pelo Testing Library.

## O que testar

- **Renderização inicial**: dado um conjunto de props, o componente mostra o conteúdo esperado. Não testar a estrutura do DOM, mas o conteúdo semântico.
- **Interações**: dado que o usuário executa uma ação (clique, digitação, submissão de formulário), o resultado esperado acontece — conteúdo muda, callback é chamado, navegação ocorre, erro aparece.
- **Estados condicionais**: loading, error, empty, estados habilitado/desabilitado. Cada estado relevante tem pelo menos um teste.
- **Integração com contexto e providers**: componentes que dependem de contexto são testados com o provider correspondente. Mockar o provider inteiro é um sinal de que o teste pode não estar verificando o comportamento real.

## O que não testar

- Estado interno do componente (valores de `useState`, referências de `useRef`).
- Sequência de chamadas a funções internas.
- Estrutura do DOM que não tem significado semântico (quantidade de `<div>`s, aninhamento de elementos).
- Estilos CSS diretamente — testes de snapshot de estilos são frágeis e raramente verificam comportamento.
- Implementação de hooks customizados através do componente quando o hook pode ser testado diretamente (e vice-versa: não testar o componente inteiro só para testar a lógica do hook).

## Mocks e dublês

- Fronteiras externas que justificam mock: chamadas a APIs (fetch, axios), módulos de roteamento, timers, serviços de analytics, o relógio do sistema.
- Não mockar componentes filhos para simplificar o teste do pai: isso quebra a verificação do comportamento de composição. Se o filho é complexo demais para o teste do pai, o teste está no nível errado.
- Não mockar o próprio hook que o componente usa para "isolar" o componente — o hook é parte da implementação, não uma fronteira.
- Mocks representam o contrato da fronteira, não a implementação interna. Um mock de `fetch` retorna os dados que a API retornaria, no formato que a API retornaria.

## Estrutura e organização

- Seguir o framework, a organização e a convenção de nomes já usados no projeto.
- Cada teste tem uma única ação principal e verifica seu resultado. Testes que exercitam vários fluxos independentes num único caso dificultam diagnóstico de falha.
- Dados de teste são mínimos: usar apenas o que o teste precisa, não um objeto completo de produção com 30 campos quando o teste usa dois.
- Evitar `beforeEach` que configura estado compartilhado entre testes que verificam comportamentos independentes. Preferir setup local por teste ou factories de dados.

## Testes de acessibilidade automatizados

- Ferramentas como `jest-axe` ou `vitest-axe` executam verificações automáticas de acessibilidade na árvore renderizada. São um complemento útil, não um substituto para revisão manual ou para a skill `accessibility-review`.
- Quando o projeto já usa esse tipo de ferramenta, verificar se os testes existentes a estão aplicando de forma consistente nos componentes relevantes.

## Concluído quando

- Os testes novos ou alterados executam e passam.
- Cada teste falharia se o comportamento visível protegido estivesse errado.
- A suíte relevante foi executada. Falhas fora dos testes criados ou alterados são relatadas, não corrigidas.

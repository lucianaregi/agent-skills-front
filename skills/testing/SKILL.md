---
name: testing
description: Cria e revisa testes automatizados focados em comportamento observável, determinísticos e pouco frágeis, em qualquer stack frontend. Use quando o usuário pedir para escrever, melhorar, corrigir ou revisar testes, aumentar a cobertura ou investigar testes instáveis. Para reproduzir um bug com teste antes de corrigi-lo, use bug-fix. Para testes de componentes React com Testing Library, use react-testing-behavior.
---

# Testing

Cria ou altera testes. Não altera código de produção sem pedido; se o código não for testável como está, explicar o motivo e perguntar antes.

## Princípios

- **Comportamento, não implementação**: verificar entradas, saídas e efeitos observáveis do contrato. Uma mudança interna que mantém o comportamento visível não deve quebrar o teste.
- **Dublês só onde necessário**: substituir (mocks, stubs, fakes) o que é externo, lento ou não determinístico — rede, serviços de terceiros, relógio, geolocalização, câmera. Para lógica pura (formatação, cálculos, validação), usar instâncias reais.
- **Determinismo**: não depender da ordem de execução, do estado deixado por outro teste nem de relógio, fuso ou aleatoriedade sem controle.
- **Asserções específicas**: verificar valores concretos, não apenas "não é nulo", "não lançou exceção" ou "foi chamado".
- **Autoria**: não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## Estrutura

- Separar preparação, ação e verificação (Arrange-Act-Assert), com uma ação principal por teste.
- Seguir o framework, a organização e a convenção de nomes já usados no projeto. Sem convenção, o nome deve indicar o cenário e o resultado esperado.
- Para vários casos da mesma regra, preferir testes parametrizados a laços ou condicionais dentro do teste.

## O que testar no frontend

- **Lógica pura**: funções de formatação, validação, cálculo e transformação são os candidatos mais simples e mais valiosos. Não precisam de browser, DOM nem framework.
- **Integração entre módulos**: como utilitários, hooks e serviços se comportam juntos, sem renderizar UI.
- **Contrato de componentes**: dado um conjunto de props e estado, qual é o resultado observável (HTML renderizado, evento emitido, chamada a serviço externo). Ver `react-testing-behavior` para abordagem específica de React.
- **Fluxos críticos de usuário**: sequências de ações que representam o caminho principal da funcionalidade, verificando o resultado final observável.

## Evitar

- Testar detalhes de implementação: estrutura interna do DOM, nomes de classes CSS, ordem de chamadas de funções privadas.
- Tornar algo público só para testar.
- Criar mocks de estruturas de dados simples ou do próprio objeto sob teste.
- Resolver testes instáveis com retry, esperas fixas (`setTimeout`) ou `waitFor` sem critério de parada. Identificar a causa antes (assincronismo não aguardado, estado compartilhado entre testes, dependência de tempo real).
- Snapshots de UI de grande granularidade que quebram com qualquer mudança visual, inclusive intencional.

## Concluído quando

- Os testes novos ou alterados executam e passam.
- Cada asserção falharia se o comportamento protegido estivesse errado: um teste que passa com qualquer implementação não protege nada. Se o teste foi escrito antes da implementação, ele foi visto falhando primeiro.
- A suíte relevante foi executada. Falhas fora dos testes criados ou alterados são relatadas, não corrigidas.

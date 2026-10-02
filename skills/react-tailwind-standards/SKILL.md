---
name: react-tailwind-standards
description: Revisa o uso de Tailwind CSS em projetos React, cobrindo design tokens versus valores arbitrários, quando extrair componentes reutilizáveis, organização de classes e composição dinâmica com clsx ou tailwind-merge. Use quando o usuário pedir revisão de estilização com Tailwind, ou quando um diff introduzir ou alterar classes Tailwind em componentes React.
---

# React Tailwind Standards

Apenas relata. Não altera código sem pedido explícito.

Seguir a configuração e as convenções de Tailwind que o projeto já adota. Apontar desvios de consistência e padrões que dificultam a manutenção, não preferências estéticas sem impacto prático.

## O que verificar

### Design tokens e valores arbitrários

- **Escala do Tailwind primeiro**: usar utilitários da escala padrão ou do tema customizado do projeto (`tailwind.config`) antes de recorrer a valores arbitrários. `p-4` é um token; `p-[17px]` é um valor arbitrário.
- **Valores arbitrários com motivo**: valores arbitrários são aceitos quando representam uma necessidade real que a escala não cobre — uma dimensão exata imposta por um terceiro, um valor de grid específico, uma cor pontual fora do sistema. Verificar se o valor arbitrário não é apenas a escala mal lembrada (ex.: `w-[192px]` quando `w-48` equivale a `192px`).
- **Tokens de tema para valores recorrentes**: um valor arbitrário que aparece em mais de um lugar é candidato a virar um token no `tailwind.config` (cor, espaçamento, fonte, breakpoint). Valores repetidos sem token criam inconsistência quando o sistema visual muda.
- **Cores fora do tema**: cores hardcoded como `bg-[#1a2b3c]` em componentes de UI (não em código de demonstração ou gerado) indicam que a paleta do projeto não está mapeada no tema.

### Quando extrair um componente

- Uma combinação de classes Tailwind que representa um conceito visual reutilizável — `Button`, `Badge`, `Input`, `Card` — e que aparece repetidamente com as mesmas variações é candidata a extração de componente React.
- A extração é motivada por reutilização real, consistência e facilidade de mudança centralizada, não pela quantidade de classes ou pelo tamanho do JSX.
- Não criar componentes de abstração apenas porque dois elementos têm classes semelhantes mas contextos completamente diferentes.
- Quando o projeto já tem um sistema de componentes (biblioteca interna ou externa), verificar se o novo código usa esses componentes antes de replicar classes.

### Organização das classes

- Classes organizadas de forma consistente dentro do projeto facilitam leitura e revisão. Uma ordem legível comum: layout e display → posicionamento → dimensões → espaçamento → tipografia → cores e visual → bordas → sombras → estados e variantes (`hover:`, `focus:`, `disabled:`) → responsividade (`sm:`, `md:`, `lg:`).
- A ordem não é uma regra estética rígida — o que importa é consistência dentro do projeto. Se o projeto usa Prettier com `prettier-plugin-tailwindcss`, a ordem é gerenciada automaticamente; não apontar ordenação como achado nesses projetos.
- Strings de classes muito longas num mesmo elemento são um sinal de que o componente pode ter responsabilidades visuais demais, ou que variantes deveriam ser expressas como props.

### Composição dinâmica de classes

- Concatenação manual de classes condicionais com template literals ou ternários longos é difícil de ler e pode produzir classes duplicadas ou conflitantes (ex.: duas classes de cor de fundo aplicadas simultaneamente, onde a última na string vence, não a última na ordem de aplicação).
- `clsx` ou `classnames` resolvem a composição condicional de forma legível. `tailwind-merge` resolve conflitos entre classes da mesma propriedade Tailwind (ex.: `p-4` e `p-2` no mesmo elemento). Quando o projeto já usa essas ferramentas, verificar se estão sendo usadas de forma consistente.
- Não adicionar `clsx` ou `tailwind-merge` para casos triviais (um ou dois ternários simples) — a dependência não se justifica. Não torná-las obrigatórias em projetos que não as usam; a adoção é avaliada por `dependency-review`.

### Classes de acessibilidade

- `sr-only` para texto visível apenas a leitores de tela (rótulos de ícones funcionais, conteúdo de contexto para AT).
- `focus-visible:` para estilos de foco aplicados apenas na navegação por teclado (sem afetar usuários de mouse). Não usar `focus:outline-none` ou `focus:ring-0` sem substituto equivalente — remove o indicador de foco para usuários de teclado.
- `not-sr-only` para reverter `sr-only` em breakpoints maiores quando necessário.

### Responsividade

- Breakpoints usados de forma consistente com a estratégia do projeto (mobile-first por padrão no Tailwind: sem prefixo = todos os tamanhos, `sm:` = a partir de `sm`).
- Elementos interativos têm área de toque adequada em viewports pequenas.
- Texto não transborda nem fica ilegível em viewports menores do que as testadas durante o desenvolvimento.

## Severidade

- 🛑 **Bloqueante**: conflito de classes que produz comportamento visual incorreto (ex.: duas cores de fundo onde apenas uma deveria se aplicar), remoção de indicador de foco sem substituto.
- ⚠️ **Importante**: valor arbitrário que duplica um token da escala existente, cor hardcoded recorrente fora do tema, composição dinâmica que produz classes conflitantes sem `tailwind-merge`.
- 💡 **Sugestão**: extração de componente para padrão visual recorrente, candidato a token de tema, melhoria de organização de classes.

## Formato da saída

Para cada achado: severidade, `arquivo:linha`, problema, impacto na manutenção ou no comportamento visual e correção recomendada. Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente.

## Concluído quando

Os componentes e arquivos no escopo foram revisados e o relatório foi entregue no formato acima.

---
name: refactoring
description: Reestrutura código frontend existente sem alterar o comportamento observável, em passos pequenos validados por testes. Use quando o usuário pedir para refatorar, simplificar, extrair, renomear, reorganizar ou eliminar duplicação em componentes, módulos ou estilos. Para corrigir bugs, use bug-fix; refatoração não adiciona funcionalidade.
---

# Refactoring

Altera código. O comportamento externo deve ficar idêntico: saídas, efeitos colaterais, contratos públicos de componentes (props, eventos emitidos, slots), acessibilidade e aparência visível.

## Regras

- Refatorar apenas a área pedida. Oportunidades fora dela viram sugestões.
- Não alterar contratos públicos de componentes (interface de props, eventos, API pública de módulos) sem pedido explícito.
- Bug encontrado durante a refatoração é relatado, não corrigido: corrigir mudaria o comportamento.
- Não misturar refatoração com funcionalidade nova.
- Não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## Procedimento

1. Definir o objetivo concreto (ex.: extrair um componente, eliminar duplicação de lógica de formatação, simplificar uma condicional, converter classe para função, reorganizar módulos) e a área afetada.
2. Rodar os testes que cobrem a área e registrar o baseline, incluindo falhas preexistentes.
   - Se a cobertura for insuficiente, propor testes de caracterização antes de alterar o código. Se o esforço for significativo, perguntar ao usuário antes de escrevê-los.
   - Se seguir sem testes, declarar como o comportamento foi preservado (ex.: verificação de tipos pelo compilador, execução manual no browser, equivalência estrutural verificada) e o risco que resta.
3. Alterar em passos pequenos, rodando os testes após cada passo.
4. Validar: nenhuma falha nova em relação ao baseline. Quando a refatoração afeta aparência visual ou interação, verificar manualmente ou por teste de UI que nada mudou.

## Concluído quando

O objetivo foi atingido, os testes não têm falhas novas em relação ao baseline e o usuário recebeu um resumo das mudanças, com as limitações da validação.

---
name: dependency-review
description: Avalia a adição, atualização ou remoção de dependências de terceiros em projetos frontend e a escolha entre recurso nativo, SDK oficial, abstração do ecossistema e biblioteca externa, considerando necessidade, benefício, bundle size, acoplamento, sobreposição, manutenção, licença, vulnerabilidades e breaking changes. Use quando o usuário pedir para adicionar, atualizar ou remover uma biblioteca, SDK ou framework, escolher entre alternativas, ou quando um diff alterar manifestos ou lockfiles de dependências.
---

# Dependency Review

Avalia e recomenda. Só aplica a mudança quando o usuário a pediu e a recomendação for prosseguir. Nos demais casos, e sempre que houver ponto marcado para decisão humana, apresentar a recomendação e aguardar. Itens não verificados não impedem a aplicação, mas precisam ser informados.

Não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## Regra de verificação

Manutenção, licença, versões e vulnerabilidades devem vir de uma fonte consultada: registro de pacotes (npm, jsr), repositório do projeto, arquivo de licença ou ferramenta de auditoria do ecossistema (ex.: `npm audit`, `pnpm audit`). Não afirmar nada disso de memória. Se não for possível consultar, marcar o item como **não verificado**.

## Adição

1. **Necessidade**: uma dependência já presente no projeto resolve? Um código próprio curto e de baixo risco é preferível a uma dependência nova. Exceção: domínios com armadilhas conhecidas, como internacionalização, datas e fusos horários, animações complexas, parsing de formatos. Neles, preferir uma biblioteca consolidada.
2. **Ordem de preferência**, quando pertinente:
   1. recurso nativo da linguagem, da plataforma web ou do framework já em uso;
   2. SDK oficial do serviço ou fornecedor integrado;
   3. abstração oficial do ecossistema;
   4. dependência externa.

   SDK oficial de um fornecedor não é abstração do ecossistema: ele prende o código àquele fornecedor.
3. **Benefício concreto**: o que a dependência entrega que as opções anteriores não entregam. Sem benefício demonstrável, não adicionar.
4. **Sobreposição**: já existe dependência ou framework que resolve o mesmo problema? Evitar duas soluções para o mesmo fim.
5. **Acoplamento**: quanto do código passa a depender dos tipos e da API da biblioteca. Preferir que ela fique restrita à camada de infraestrutura ou de adaptadores, sem vazar para componentes de UI genéricos.
6. **Bundle size e tree-shaking**: qual é o peso adicionado ao bundle do cliente? A biblioteca suporta tree-shaking? Dependências que aumentam significativamente o bundle sem benefício proporcional são desvantagem concreta em projetos frontend.
7. **Duplicatas no lockfile**: a dependência já existe como dependência transitiva em outra versão? Duplicatas aumentam o bundle e podem causar comportamento inesperado.
8. **Manutenção**: releases recentes, issues respondidas, compatibilidade com a versão da plataforma usada no projeto.
9. **Licença**: compatível com a licença e o modelo de distribuição do projeto. Incluir licenças de assets (fontes, ícones) quando aplicável. Em caso de dúvida (ex.: copyleft em software distribuído), sinalizar para decisão humana.
10. **Vulnerabilidades** conhecidas na versão escolhida.

## Atualização

1. Ler o changelog ou as release notes entre a versão atual e a versão alvo, identificando breaking changes e depreciações.
2. Verificar licença e vulnerabilidades da versão alvo. A licença pode mudar entre versões.
3. Em salto de versão major, localizar no código os usos afetados antes de atualizar. Se a atualização exigir adaptações de código significativas, apresentá-las antes de aplicar.
4. Revisar o diff do lockfile: dependências transitivas adicionadas, removidas ou com mudança de major.
5. Depois de atualizar, rodar build e testes relevantes e confirmar que não há falhas novas em relação ao estado anterior.

## Remoção

1. Procurar usos restantes em código, configuração, scripts e documentação, incluindo usos indiretos (plugins carregados por configuração, CLIs chamadas em scripts). Se houver usos, listá-los e só removê-los se isso fizer parte do pedido.
2. Remover pelo gerenciador de pacotes do ecossistema, que atualiza o lockfile. Não editar o lockfile à mão.
3. Rodar build e testes relevantes e confirmar que não há falhas novas.

## Formato da saída

- **Recomendação**: prosseguir, não prosseguir ou usar uma alternativa (indicar qual).
- **Justificativa** por critério, em poucas linhas, com os itens não verificados marcados. Na adição, incluir o benefício concreto e por que as opções anteriores na ordem de preferência não bastam.
- **Ações necessárias**: ajustes de código, migração, pontos de atenção.

## Concluído quando

A recomendação foi entregue. Se a mudança foi aplicada, manifesto e lockfile estão consistentes e a validação não tem falhas novas.

---
name: bug-fix
description: Investiga um bug até a causa raiz, reproduz o erro, aplica a menor correção que resolve a causa e protege contra regressão com teste. Use quando o usuário relatar erro, comportamento visual inesperado, exceção no console, estado incorreto de UI ou teste falhando e pedir diagnóstico ou correção. Para reestruturar código sem mudar comportamento, use refactoring.
---

# Bug Fix

Altera código e testes. Se o usuário pediu apenas diagnóstico, executar os passos 1 a 3 e relatar sem corrigir. Nesse caso, o teste de reprodução só fica no repositório se o usuário concordar.

Não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## Procedimento

1. **Levantar evidências**: descrição, passos de reprodução, mensagens de erro no console, stack traces, erros de rede, comportamento visual reportado e o código envolvido. Formular hipóteses e verificar o que cada uma prevê antes de escolher uma.
2. **Registrar o baseline**: rodar os testes da área afetada (a suíte completa, se for viável) e anotar as falhas que já existiam.
3. **Reproduzir**: escrever um teste automatizado que falhe pelo motivo do bug e confirmar que ele falha pelo motivo esperado, não por erro no próprio teste.
   - Se não for viável automatizar (ausência de infraestrutura de testes, bug visual que depende de layout, interação com serviço externo, timing de animação), documentar a reprodução manual passo a passo — incluindo URL, estado da aplicação, ação exata e resultado observado — e o motivo de não automatizar. Não criar infraestrutura de testes sem autorização.
   - Se não for possível reproduzir, parar e relatar o que foi tentado e as hipóteses restantes. Não aplicar uma correção especulativa sem avisar.
4. **Corrigir a causa raiz** com a menor mudança suficiente. Não mascarar o sintoma: nada de `catch` vazio, valor padrão para esconder o erro, condição de guarda que torna o problema invisível ou asserção removida ou afrouxada.
5. **Validar**: o teste de reprodução passa, não há falhas novas em relação ao baseline e, quando o bug era visual ou de interação, a reprodução manual confirma o comportamento correto.

Outros bugs encontrados no caminho são relatados, não corrigidos.

## Concluído quando

- A causa raiz está explicada em poucas linhas e ligada à evidência.
- O teste de regressão passa (ou a reprodução manual está documentada com os passos e o resultado observado).
- Não há falhas novas em relação ao baseline.
- Falhas preexistentes e bugs não relacionados foram relatados ao usuário.

---
name: task-planning
description: Elabora, com base na inspeção do código, um plano curto antes da implementação, organizado em entregas pequenas e verificáveis, cobrindo objetivo, decisões, escopo, arquivos afetados, passos, riscos, dúvidas e forma de validação. Em tarefas não triviais, salva o plano aprovado no repositório para que o trabalho não dependa do histórico da conversa. Use quando o usuário pedir um plano ou abordagem, ou antes de tarefas com várias etapas, vários arquivos ou risco de quebra. Não implementa por conta própria.
---

# Task Planning

Planeja; não altera código. O único arquivo que pode criar ou atualizar é o do próprio plano, quando ele for salvo (ver "Plano salvo no repositório"). A implementação só começa depois da aprovação do usuário, ou quando ele já pediu a implementação e o plano não tem decisões bloqueantes em aberto.

## Regras

- Inspecionar o código antes de listar arquivos. Não inventar caminhos nem componentes; marcar com "(novo)" os arquivos a criar.
- Planejar apenas o que foi pedido. Melhorias percebidas vão como observação separada.
- Tamanho proporcional à tarefa: tarefa pequena, plano de poucas linhas.
- Não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## Critérios do plano

- **Entrega verificável**: cada passo termina num estado cujo comportamento pode ser verificado (teste passando, funcionalidade exercitável no browser, comportamento visual observável). "Componente criado" ou "configuração adicionada" não conta como entrega.
- **Fatias verticais**: quando a tarefa entrega funcionalidade, preferir fatias pequenas que atravessam as camadas necessárias até um comportamento de ponta a ponta, em vez de construir uma camada inteira por vez. Tarefas horizontais por natureza (atualização de dependências, configuração de CI) seguem sua própria ordem.
- **Nada sem consumidor**: não planejar componente, hook, utilitário, abstração ou configuração que a mesma entrega não use.
- **Sem etapa cerimonial**: etapas que só produzem estrutura ou configuração sem comportamento verificável entram apenas se o usuário pedir ou se forem pré-requisito direto de um passo da mesma entrega.
- **Decisões antes de começar**: identificar as decisões que mudam a implementação — estratégia de busca de dados, onde colocar o estado, arquitetura de componentes, tecnologia de animação, abordagem de estilização — e marcá-las como bloqueantes enquanto não estiverem fechadas.

## Formato

```markdown
### Plano: <tarefa>

**Objetivo**: <1 a 2 frases: o que muda e por quê>

**Decisões**
- Escolhas técnicas aprovadas e o motivo, quando não for óbvio.

**Escopo**
- Inclui: ...
- Não inclui: ...

**Arquivos**
- `caminho/arquivo`: o que muda
- `caminho/outro-arquivo` (novo): propósito

**Passos**
1. ...

**Riscos**
- Regressões em componentes que usam o código alterado, mudanças de contrato de props, efeitos em acessibilidade ou performance.

**Em aberto**
- Dúvidas, premissas e decisões ainda não aprovadas, com a premissa adotada ou a pergunta ao usuário. Marcar como **bloqueante** o que precisa estar decidido antes de começar.

**Validação**
- Automatizada: testes a criar ou executar, com os comandos do projeto.
- Manual: verificação no browser, estados a exercitar (loading, erro, vazio, interação).
- Sem validação: o que ficará sem cobertura e o risco disso.
```

Omitir seções que não se aplicam.

## Plano salvo no repositório

- **Quando salvar**: só em implementações não triviais, quando o plano tiver decisões, várias etapas, riscos ou contexto que precise sobreviver à sessão. Planos pequenos ficam só na conversa.
- **Onde**: seguir a convenção do projeto para local e nome dos planos. Sem convenção, propor um local (ex.: `docs/plans/<tarefa>.md`). Informar o caminho ao apresentar o plano, para que a aprovação do plano cubra também o arquivo.
- **Quando gravar**: depois da aprovação e antes de começar a implementação.
- **Conteúdo**: só o necessário para continuar o trabalho sem o histórico da conversa, no formato acima. O que ainda não foi aprovado fica em **Em aberto**, nunca em **Decisões**.
- **Durante a implementação**: se surgir algo que mude o plano aprovado de forma relevante (abordagem, escopo, arquivos, riscos), parar, propor a atualização do plano e pedir a decisão do usuário. Ajustes de detalhe que não mudam o que foi aprovado podem ser registrados diretamente no plano.
- **Ao concluir**: opcionalmente, registrar no plano o resultado e os desvios relevantes em relação ao que foi aprovado.
- **Limite**: o plano salvo não autoriza ampliar o escopo. O que não estiver em **Decisões** nem na lista "Inclui" do **Escopo** continua precisando de aprovação.

## Concluído quando

O plano foi entregue, com as decisões bloqueantes explícitas para o usuário. Se o plano precisar ser salvo, o arquivo foi gravado depois da aprovação.

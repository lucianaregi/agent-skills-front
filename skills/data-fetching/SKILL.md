---
name: data-fetching
description: Orienta o desenho e revisa a estratégia de busca de dados no frontend, cobrindo onde iniciar o fetch, loading e error states, cancelamento, requisições duplicadas e race conditions. Use quando o usuário for projetar ou revisar como dados são buscados e apresentados numa interface. Para decidir onde armazenar e derivar os dados depois de buscados, use react-state-architecture se o projeto usa React.
---

# Data Fetching

Orienta o desenho e revisa implementações existentes. Em revisão, apenas relata. Quando o usuário pedir a implementação, estes critérios orientam o código.

Não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## Desenho

### Onde buscar os dados

- **No servidor quando possível**: buscar dados no servidor (SSR, geração estática, funções de borda) elimina a latência de uma segunda requisição do cliente e não expõe credenciais ao browser. Só buscar no cliente quando o dado for específico de sessão interativa, atualizado em tempo real ou dependente de ação do usuário que não pode ser antecipada no servidor.
- **Colocação**: a busca de dados fica no nível mais próximo possível do componente que a consome, não centralizada por padrão. Centralizar só quando múltiplos componentes independentes precisarem do mesmo dado e a busca duplicada tiver custo real.
- **Granularidade**: buscar só o que a tela precisa. Payload excessivo aumenta o tempo de carregamento e o custo de banda.

### Estados obrigatórios

Toda busca de dados tem ao menos três estados representados na UI: **carregando**, **erro** e **sucesso**. O estado vazio (resposta bem-sucedida sem dados) é um quarto estado quando relevante para o contexto.

- Estado de carregamento dá feedback imediato ao usuário. Skeleton screens ou spinners são igualmente válidos; a escolha depende do contexto visual.
- Estado de erro informa o que falhou de forma útil e, quando aplicável, oferece ação de recuperação (ex.: "Tentar novamente").
- Estado vazio não é um caso de erro; tem mensagem própria.

### Requisições paralelas e sequenciais

- Requisições independentes são disparadas em paralelo, não em sequência. Encadear requisições independentes serializa a latência sem motivo.
- Requisições que dependem do resultado de outra são sequenciais; essa dependência é explícita no código, não implícita por posição.

### Cancelamento e race conditions

- Requisições disparadas por interação do usuário (busca com digitação, filtros, paginação) são canceladas quando substituídas por uma nova antes de completar. Sem cancelamento, uma resposta atrasada de uma requisição antiga pode sobrescrever o resultado da mais recente.
- O mecanismo de cancelamento depende da tecnologia usada: `AbortController` para `fetch` nativo; opção equivalente em bibliotecas de HTTP.
- Componentes que disparam requisições ao montar devem cancelá-las ao desmontar.

### Requisições duplicadas

- A mesma requisição não deve ser disparada em paralelo por múltiplos consumidores independentes. Deduplicação pode ser feita por cache compartilhado, contexto ou biblioteca de gerenciamento de server state. A abordagem é escolhida com base na necessidade concreta do projeto.

### Revalidação e cache

- Dados com validade curta (preços, notificações, status em tempo real) têm estratégia de revalidação explícita. Dados estáticos ou de baixa volatilidade podem ser cacheados de forma mais agressiva.
- Cache no cliente não substitui cache no servidor quando ambos fazem sentido.
- Após mutação (criação, edição, exclusão), os dados afetados são revalidados ou atualizados de forma consistente.

### Tratamento de erro

- Erros de rede e erros de aplicação (4xx, 5xx) são tratados de forma diferente quando o comportamento para o usuário deve ser diferente.
- Não silenciar erros: um `catch` vazio que mantém a tela em estado de carregamento infinito é pior do que um estado de erro visível.
- Erros esperados (ex.: recurso não encontrado, sessão expirada) têm tratamento específico, não genérico.

## Revisão

O que verificar em código existente:

- Estados de carregamento e erro representados na UI.
- Requisições independentes disparadas em paralelo.
- Presença de cancelamento em requisições que podem ser substituídas.
- Tratamento explícito de erros de rede e de aplicação.
- Revalidação após mutações.
- Ausência de credenciais expostas no cliente.

## Severidade

- 🛑 **Bloqueante**: credencial exposta ao cliente, race condition que corrompe o estado da UI ou erro de rede silenciado que deixa a interface em estado inconsistente.
- ⚠️ **Importante**: ausência de estado de carregamento ou erro, requisições independentes serializadas sem motivo, cancelamento ausente em busca interativa.
- 💡 **Sugestão**: melhoria opcional.

## Formato da saída

Para cada achado: severidade, `arquivo:linha` ou componente, problema, impacto para o usuário e correção recomendada. Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente.

## Concluído quando

A revisão foi entregue no formato acima ou, em orientação de desenho, a abordagem recomendada foi apresentada com as decisões em aberto.

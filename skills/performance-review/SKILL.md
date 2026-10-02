---
name: performance-review
description: Revisa o impacto de código e decisões de implementação nos Core Web Vitals e na percepção de desempenho da interface, cobrindo carregamento de recursos, JavaScript no caminho crítico, re-renderizações evitáveis e lazy loading. Use quando o usuário pedir revisão de performance, ou quando um diff adicionar recursos pesados, introduzir carregamento síncrono de scripts, alterar o carregamento de imagens ou modificar lógica de renderização com potencial de re-renderização excessiva.
---

# Performance Review

Apenas relata. Não altera código sem pedido explícito.

Avaliar com base no que é verificável no código. Impactos de desempenho que dependem de dados de produção, condições de rede reais ou comportamento de CDN são declarados como premissas não verificadas.

## O que verificar

### Carregamento inicial

- **JavaScript no caminho crítico**: scripts que bloqueiam a renderização da página são carregados de forma assíncrona (`async` ou `defer`) ou diferidos. Scripts de terceiros (analytics, chat, mapas) raramente precisam bloquear o carregamento inicial.
- **CSS crítico**: o CSS necessário para renderizar o conteúdo acima da dobra é carregado antes do conteúdo, ou injetado inline quando o projeto adota essa estratégia. CSS não crítico pode ser carregado de forma assíncrona.
- **Fonts**: fontes externas usam `font-display: swap` ou equivalente para evitar texto invisível durante o carregamento (FOIT). Preload de fontes críticas quando a tipografia é parte central da experiência.
- **Tamanho do bundle**: imports que trazem uma biblioteca inteira quando apenas parte dela é usada (ex.: `import _ from 'lodash'` em vez de `import debounce from 'lodash/debounce'`). Verificar se a biblioteca suporta tree-shaking e se o bundler está configurado para aproveitá-lo.

### Imagens

- Imagens sem dimensões explícitas causam Cumulative Layout Shift (CLS) ao carregar. Toda imagem com posição relevante no layout tem `width` e `height` definidos, ou usa CSS para reservar o espaço antes do carregamento.
- Imagens fora da viewport inicial são carregadas de forma lazy (`loading="lazy"` ou equivalente da biblioteca em uso). Imagens acima da dobra não devem ser lazy.
- O formato das imagens é adequado: fotos em JPEG/WebP/AVIF; gráficos com transparência em PNG/WebP; ícones e ilustrações simples em SVG.
- Imagens são servidas no tamanho próximo ao exibido, não redimensionadas pelo browser. `srcset` e `sizes` ou o componente de imagem do framework são usados quando o tamanho varia por viewport.

### Renderização e interatividade

- **Operações de layout e paint**: manipulações de DOM que forçam reflow (leitura de propriedades de layout seguida de escrita, dentro de loops) são evitadas. Ler primeiro, escrever depois.
- **Animações**: propriedades `transform` e `opacity` são preferidas a `top`/`left`/`width`/`height` em animações, porque não causam reflow.
- **Scroll e resize**: handlers de `scroll` e `resize` que executam trabalho pesado são debounced ou throttled.
- **Re-renderizações desnecessárias**: funções e objetos criados inline em props (ex.: arrow functions em JSX, objetos literais em props) causam re-renderização de componentes filhos a cada render do pai quando o filho compara props por referência. Avaliar se o custo da re-renderização justifica memoização.
- **Listas longas**: listas com muitos itens (centenas ou mais) que renderizam todos os elementos simultaneamente podem causar jank. Verificar se o projeto já usa virtualização e se ela está sendo contornada.

### Lazy loading de código

- Rotas, modais, abas e funcionalidades acessadas por poucos usuários ou raramente no fluxo principal são candidatos a carregamento sob demanda (code splitting). Carregar todo o código da aplicação antecipadamente aumenta o tempo de carregamento inicial sem benefício proporcional.
- Imports dinâmicos (`import()`) são usados de forma criteriosa: não dividir em fatias tão pequenas que o overhead de requisição supere o ganho.

### Requisições de rede

- Requisições redundantes (mesma URL disparada múltiplas vezes sem necessidade) são identificadas. Deduplicação e cache são os mecanismos corretos; polling sem intervalo definido é um sinal de alerta.
- Payloads de API maiores do que o necessário para a tela atual aumentam o tempo de carregamento e o custo de parse. Verificar se a API suporta campos selecionados ou paginação quando o payload é visivelmente excessivo.

## Severidade

- 🛑 **Bloqueante**: script síncrono de terceiros no `<head>` que bloqueia a renderização, CLS causado por imagens sem dimensões em posição de destaque, ou lista longa sem virtualização que torna a interface inutilizável.
- ⚠️ **Importante**: bundle excessivo por import sem tree-shaking, imagens pesadas sem lazy loading, animações em propriedades que causam reflow, polling sem intervalo ou handlers de scroll sem debounce/throttle.
- 💡 **Sugestão**: melhoria opcional que pode melhorar métricas mas não é urgente.

## Formato da saída

Para cada achado: severidade, `arquivo:linha` ou componente, problema, métrica ou experiência afetada e correção recomendada. Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente e indicar o que foi verificado.

## Concluído quando

Todas as áreas aplicáveis foram verificadas e o relatório foi entregue no formato acima.

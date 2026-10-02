---
name: accessibility-review
description: Revisa HTML semântico, uso de ARIA, navegação por teclado, foco visível, rótulos de formulário, contraste de cores e compatibilidade com leitores de tela. Use quando o usuário pedir revisão de acessibilidade, ou quando um diff tocar formulários, modais, menus, botões, tabelas, imagens ou qualquer componente interativo.
---

# Accessibility Review

Apenas relata. Não altera código sem pedido explícito.

Avaliar com base no que está presente no código e na marcação. Não reportar problemas que dependem exclusivamente de configuração de ambiente ou de comportamento do sistema operacional. Quando a verificação depender de execução no browser ou de teste com tecnologia assistiva, declarar a premissa.

> ⚠️ Validação completa de acessibilidade exige testes manuais com tecnologias assistivas reais (leitores de tela, navegação por teclado, zoom de texto) e avaliação especializada. Esta skill cobre o que é verificável estática e automaticamente no código.

## O que verificar

### Estrutura e semântica

- **Landmarks**: a página usa elementos semânticos (`<header>`, `<main>`, `<nav>`, `<footer>`, `<aside>`, `<section>`) para demarcar regiões principais. Sem eles, tecnologias assistivas não conseguem navegar por regiões.
- **Hierarquia de headings**: `<h1>` a `<h6>` em ordem lógica, sem pular níveis. O `<h1>` identifica o conteúdo principal da página, não o nome do site.
- **Listas**: itens de navegação e enumerações usam `<ul>` ou `<ol>`, não divs ou spans em sequência.
- **Tabelas**: `<th>` com `scope` correto (`col` ou `row`); `<caption>` ou `aria-label` descrevendo o propósito. Tabelas de layout são substituídas por CSS.
- **Uso de `div` e `span`**: não substituem elementos semânticos com significado intrínseco. Um `<div>` clicável não é um botão.

### Imagens e mídia

- **Texto alternativo**: toda `<img>` tem `alt`. Imagens decorativas têm `alt=""`. O texto alternativo descreve a função ou o conteúdo da imagem, não "imagem de" ou o nome do arquivo.
- **Ícones funcionais**: ícones que comunicam ação ou estado têm rótulo acessível (`aria-label` ou texto oculto via técnica equivalente a `sr-only`). Ícones puramente decorativos têm `aria-hidden="true"`.
- **Mídia**: vídeos têm legendas; áudios têm transcrição quando o conteúdo é informativo.

### Formulários

- Todo `<input>`, `<select>` e `<textarea>` tem um `<label>` associado por `for`/`id` ou encapsulado. `placeholder` não substitui `<label>`.
- Campos obrigatórios indicam isso de forma acessível (`aria-required="true"` ou atributo `required`, além de indicação visual).
- Mensagens de erro são associadas ao campo por `aria-describedby` ou `aria-errormessage`, não apenas pela posição visual.
- Agrupamentos de campos relacionados (ex.: grupo de radio buttons) usam `<fieldset>` e `<legend>`.

### Interatividade e teclado

- Todos os controles interativos são alcançáveis e operáveis por teclado: Tab para foco, Enter/Space para ativar, Escape para fechar.
- A ordem de foco do teclado corresponde à ordem visual e lógica do conteúdo.
- O foco visível é claramente identificável. Não remover `outline` sem substituir por indicador equivalente.
- Modais aprisionam o foco enquanto abertos (focus trap) e o devolvem ao elemento de origem ao fechar.
- Menus e dropdowns seguem os padrões de interação por teclado do ARIA Authoring Practices (ex.: setas para navegar entre itens de menu).
- Conteúdo que aparece ao passar o mouse (`hover`) também aparece ao receber foco do teclado.

### ARIA

- `role`, `aria-label`, `aria-labelledby`, `aria-describedby`, `aria-expanded`, `aria-hidden`, `aria-live` e demais atributos ARIA são usados conforme a especificação. ARIA complementa, não substitui, semântica HTML nativa.
- `aria-hidden="true"` não é aplicado a elementos que contêm foco ou que são interativos.
- `aria-live` é usado com parcimônia; regiões com `aria-live="assertive"` interrompem o leitor de tela e só se justificam para alertas urgentes.
- Roles customizados têm todos os estados e propriedades ARIA obrigatórios para aquele role.

### Contraste e percepção visual

- Texto normal (abaixo de 18pt ou 14pt negrito) tem relação de contraste mínima de 4,5:1 com o fundo (WCAG AA).
- Texto grande (18pt ou 14pt negrito ou acima) tem relação mínima de 3:1.
- Componentes de interface e elementos gráficos informativos têm contraste mínimo de 3:1 em relação ao entorno.
- Informação não é transmitida apenas por cor; há indicador textual, ícone ou padrão complementar.

### Movimento e distração

- Animações que piscam mais de três vezes por segundo ou cobrem área significativa da tela podem causar convulsões. Verificar presença de gatilhos fotossensíveis.
- Animações decorativas respeitam a preferência `prefers-reduced-motion` quando o projeto já suporta essa media query.

## Severidade

- 🛑 **Bloqueante**: conteúdo inacessível para uma categoria de usuários (ex.: formulário sem rótulos, imagem informativa sem alt, modal sem focus trap, contraste abaixo de 3:1 em texto principal). Impede o merge.
- ⚠️ **Importante**: problema real que reduz a usabilidade para usuários de tecnologia assistiva, mas com contorno existente (ex.: hierarquia de headings quebrada, foco visível fraco mas presente, ícone funcional sem rótulo em contexto secundário).
- 💡 **Sugestão**: melhoria opcional que eleva a experiência além do mínimo.

## Formato da saída

Para cada achado: severidade, `arquivo:linha` ou componente, critério violado, impacto para o usuário e correção recomendada. Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente e indicar o que foi verificado.

## Concluído quando

Todas as áreas aplicáveis foram verificadas, o relatório foi entregue no formato acima e as premissas não verificáveis estaticamente foram declaradas.

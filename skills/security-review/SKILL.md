---
name: security-review
description: Analisa código frontend e configuração em busca de riscos de segurança concretos e exploráveis, como XSS, dados sensíveis expostos no cliente, credenciais vazadas, armazenamento inseguro de tokens, variáveis de ambiente expostas ao bundle e configurações inseguras de CORS e CSP. Use quando o usuário pedir revisão ou auditoria de segurança, ou quando uma mudança tocar autenticação, autorização, entrada do usuário, dados sensíveis, armazenamento no cliente ou integração com serviços externos.
---

# Security Review

Apenas relata. Não corrige sem pedido explícito.

## Princípios

- Reportar riscos com caminho de exploração plausível no código real: de onde vem a entrada, por onde passa e onde é usada ou exibida.
- Não reportar riscos teóricos sem ligação com o código. Se a exploração depender de algo fora do repositório (configuração do servidor, proxy, CDN), declarar a premissa.

## O que verificar

### XSS (Cross-Site Scripting)

- **HTML injetado diretamente**: uso de APIs que inserem HTML sem sanitização (ex.: `innerHTML`, `outerHTML`, `document.write`, e equivalentes em frameworks como `dangerouslySetInnerHTML` no React ou `v-html` no Vue). A defesa principal é evitar essas APIs; quando HTML fornecido pelo usuário for necessário, usar uma biblioteca de sanitização consolidada.
- **Saída sem escape**: conteúdo dinâmico inserido no DOM sem o escape automático do mecanismo de templates. Frameworks modernos fazem isso por padrão; verificar onde esse mecanismo é contornado explicitamente.
- **URLs dinâmicas**: valores controlados pelo usuário usados em `href`, `src` ou `action` sem validação de esquema. Um `javascript:` em `href` é XSS.
- **postMessage**: mensagens recebidas via `window.addEventListener('message', ...)` validam a origem (`event.origin`) antes de processar o dado.

### Armazenamento e exposição no cliente

- **Tokens de autenticação**: tokens de sessão ou JWT armazenados em `localStorage` ou `sessionStorage` são acessíveis a qualquer script na página (incluindo scripts injetados por XSS). Cookies com `HttpOnly` não são acessíveis via JavaScript. A escolha de armazenamento implica escolha de vetor de ataque; ela deve ser explícita e consciente.
- **Dados sensíveis no cliente**: PII (dados pessoais, documentos, dados financeiros), credenciais ou chaves de API não devem ser armazenados no cliente além do necessário para a operação imediata.
- **Variáveis de ambiente expostas ao bundle**: variáveis de ambiente prefixadas para exposição pública (o mecanismo varia por bundler e framework) ficam visíveis no código JavaScript enviado ao browser. Verificar se contêm valores que não deveriam ser públicos (chaves de API com permissões sensíveis, segredos de serviços).

### Autenticação e autorização

- **Verificação apenas no cliente**: controle de acesso baseado só em estado do cliente (ex.: esconder um botão ou rota sem verificação do servidor) é contornável. A autorização real acontece no servidor; o cliente pode refletir o estado, não defini-lo.
- **Expiração de token**: tokens sem renovação silenciosa podem expirar durante o uso, causando erros inesperados. Tokens sem expiração são risco permanente se comprometidos.
- **Redirecionamento após login**: URLs de redirecionamento pós-autenticação (`?redirect=`) sem validação permitem open redirect — enviar o usuário para um destino externo malicioso após um login legítimo.

### Dependências e supply chain

- **Scripts de terceiros inline ou carregados sem integridade**: scripts externos sem `integrity` (Subresource Integrity) podem ser modificados pelo servidor de origem. Verificar especialmente scripts de CDN público.
- **`eval` e equivalentes**: `eval()`, `new Function()`, `setTimeout` com string e similares executam código arbitrário. A presença deles é sempre um sinal de alerta.

### CORS e CSP

- **CORS**: requisições cross-origin com credenciais (`withCredentials: true` ou `credentials: 'include'`) funcionam apenas se o servidor responder com a origem específica do cliente (não `*`) e `Access-Control-Allow-Credentials: true`. Verificar se o cliente está enviando credenciais para origens não confiáveis.
- **Content Security Policy**: se o projeto define CSP, verificar se `unsafe-inline` e `unsafe-eval` estão presentes sem necessidade. Cada um deles enfraquece significativamente a proteção. Um CSP ausente é uma premissa a declarar.

### Dados enviados a terceiros

- Scripts de analytics, chat, mapas e monitoramento recebem contexto da página. Verificar se dados pessoais ou sensíveis chegam a esses scripts por URL, por variáveis globais ou por eventos automaticamente capturados.
- A retenção e o uso dos dados pelo terceiro costumam estar fora do repositório (contrato, configuração da conta); declarar como premissa.

### Se a aplicação usa modelos de IA ou expõe ferramentas a agentes

Aplicam-se os mesmos critérios de `ai-integration` relativos a injeção de instruções, saída do modelo como entrada não confiável e exposição de ferramentas. Ver também `ai-integration`, se disponível.

## Severidade

- 🛑 **Bloqueante**: vulnerabilidade explorável (XSS, open redirect, token exposto via variável de ambiente pública, `dangerouslySetInnerHTML` com entrada não sanitizada). Impede o merge.
- ⚠️ **Importante**: risco real que exige condições adicionais para exploração, ou defesa em profundidade ausente em área sensível (ex.: token em localStorage sem mitigações compensatórias, CSP ausente em área autenticada).
- 💡 **Sugestão**: endurecimento opcional.

## Formato da saída

Para cada achado:

1. Severidade e `arquivo:linha`.
2. Vulnerabilidade (ex.: XSS via innerHTML, token exposto via env var pública).
3. Cenário de exploração: o que um atacante faz e o que obtém.
4. Correção recomendada.

Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente e indicar o que foi verificado.

## Concluído quando

Todas as áreas aplicáveis foram verificadas e o relatório foi entregue no formato acima.

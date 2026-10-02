# agent-skills-front

Coleção pública de skills reutilizáveis para agentes de IA voltados ao desenvolvimento de software **frontend**.

## 🎯 Propósito

Este repositório reúne diretrizes, roteiros e checklists para orientar assistentes de código e agentes autônomos — como Claude Code, OpenAI Codex, GitHub Copilot, Cursor e Google Antigravity — em projetos frontend.

As skills cobrem desde preocupações universais de engenharia de software (bug fix, refatoração, testes, segurança) adaptadas ao contexto de interfaces, até disciplinas próprias do frontend (acessibilidade, performance de UI, arquitetura de estado) e skills específicas para React e Tailwind CSS.

As skills seguem estes princípios:

- **Independência**: skills genéricas são agnósticas de framework, bundler e infraestrutura. Regras específicas de tecnologia ficam em skills cujo nome deixa isso explícito.
- **Objetividade**: critérios claros e orientados a resultado, sem overengineering.
- **Foco em qualidade**: prioridade para código correto, acessível, testável, legível e seguro.
- **Documentação em pt-BR**: instruções em português do Brasil, com termos técnicos e comandos em inglês quando apropriado.

## 📂 Estrutura

Cada skill fica em um diretório próprio, cujo nome é igual ao campo `name` do frontmatter:

```
skills/
└── <nome-da-skill>/
    └── SKILL.md
```

O `SKILL.md` começa com um frontmatter YAML contendo `name` e `description`, seguido das instruções em Markdown. O corpo abre dizendo se a skill apenas relata ou se altera arquivos, traz as regras e o procedimento ou os pontos a verificar e termina com a seção **Concluído quando**. As skills de revisão também definem a escala de severidade e o formato da saída.

## 🧰 Skills Disponíveis

### Genéricas de frontend

| Skill | Propósito |
|---|---|
| `accessibility-review` | Revisar HTML semântico, ARIA, navegação por teclado, foco visível, rótulos de formulário e contraste de cores. |
| `bug-fix` | Investigar a causa raiz, reproduzir o erro (incluindo bugs visuais e de interação) e corrigir com teste de regressão. |
| `data-fetching` | Orientar e revisar a estratégia de busca de dados: onde buscar, loading/error states, cancelamento e race conditions. |
| `dependency-review` | Avaliar adição, atualização ou remoção de dependências: necessidade, bundle size, tree-shaking, licença, manutenção e vulnerabilidades. |
| `performance-review` | Revisar impacto em Core Web Vitals, carregamento de recursos, JavaScript no caminho crítico, lazy loading e re-renderizações evitáveis. |
| `pr-review` | Revisar pull requests de forma holística: correção, escopo, testes, acessibilidade, segurança e performance. |
| `refactoring` | Refatorar código frontend preservando o comportamento observável e os contratos de componentes, com validação por testes. |
| `security-review` | Identificar riscos de segurança concretos: XSS, dados sensíveis no cliente, tokens expostos, variáveis de ambiente vazadas, CORS e CSP. |
| `task-planning` | Planejar tarefas técnicas antes de implementar e salvar o plano aprovado no repositório em implementações não triviais. |
| `testing` | Criar e revisar testes focados em comportamento observável, determinísticos e pouco frágeis, em qualquer stack frontend. |

### Específicas de tecnologia — React

| Skill | Propósito |
|---|---|
| `react-component-review` | Revisar componentes React: anatomia, responsabilidades, uso correto de `useEffect`, tipagem de props, colocação de estado e contratos de componente. |
| `react-state-architecture` | Orientar e revisar a arquitetura de estado: colocation, estado derivado, distinção entre UI state e server state, imutabilidade. |
| `react-tailwind-standards` | Revisar o uso de Tailwind CSS em projetos React: design tokens vs. valores arbitrários, quando extrair componentes, organização de classes e composição dinâmica. |
| `react-testing-behavior` | Criar e revisar testes de componentes React focados no comportamento observável, com React Testing Library e consultas semânticas. |

### Genéricas de engenharia de software

| Skill | Propósito |
|---|---|
| `ai-integration` | Orientar e revisar integrações com modelos de IA: provedores substituíveis, saída estruturada, custo, testes e avaliação de qualidade. |
| `commit` | Validar alterações de forma proporcional à mudança e criar commits na convenção do repositório (padrão: Conventional Commits em pt-BR). |
| `pr-creation` | Criar pull requests fiéis às alterações da branch, com título e descrição baseados apenas no que mudou. |
| `readme` | Criar e manter READMEs fiéis ao código real, com pré-requisitos e comandos exatos de execução. |
| `scope-check` | Identificar e conter alterações fora do escopo da tarefa (scope creep). |
| `technical-documentation` | Elaborar documentação técnica e ADRs fiéis ao código, declarando o que não pôde ser verificado. |

## 🚀 Como usar em outros projetos

Para usar uma skill, copie o diretório dela (por exemplo, `skills/bug-fix/`) para um dos diretórios que a ferramenta lê. Skills de projeto ficam dentro do repositório de destino e podem ser versionadas com ele; skills pessoais ficam no diretório do usuário e valem para todos os projetos da máquina.

| Ferramenta | Skills de projeto | Skills pessoais |
|---|---|---|
| Claude Code | `.claude/skills/` | `~/.claude/skills/` |
| Claude Desktop e claude.ai (chat) | — | Upload de `.zip` em Customize > Skills |
| OpenAI Codex | `.agents/skills/` | `~/.agents/skills/` |
| GitHub Copilot | `.github/skills/`, `.claude/skills/`, `.agents/skills/` | `~/.copilot/skills/`, `~/.agents/skills/` |
| Cursor | `.cursor/skills/`, `.agents/skills/` | `~/.cursor/skills/`, `~/.agents/skills/` |
| Google Antigravity (IDE) | `.agents/skills/` | `~/.gemini/config/skills/` |
| Antigravity CLI | `.agents/skills/` | `~/.gemini/antigravity-cli/skills/` |

💡 **Interoperabilidade**: `.agents/skills/` na raiz do projeto é lido por Codex, Copilot, Cursor e Antigravity. O Claude Code não lê esse diretório; para ele, use `.claude/skills/`, que o Copilot também reconhece.

**Exemplo** — instalar todas as skills deste repositório no diretório interoperável:

```bash
git clone https://github.com/lucianaregi/agent-skills-front.git
mkdir -p meu-projeto/.agents/skills
cp -r agent-skills-front/skills/* meu-projeto/.agents/skills/
```

Para o Claude Code, substitua `.agents/skills` por `.claude/skills`.

**Referências**

Os caminhos acima foram conferidos na documentação oficial em setembro de 2026 e podem mudar entre versões:

- [Claude Code: Skills](https://docs.anthropic.com/claude-code/skills)
- [Claude: usando skills no app](https://support.anthropic.com/pt/articles/skills)
- [OpenAI Codex: Skills](https://platform.openai.com/docs/codex/skills)
- [GitHub Copilot: About agent skills](https://docs.github.com/en/copilot/customizing-copilot/about-agent-skills)
- [Cursor: Skills](https://docs.cursor.com/skills)
- [Google Antigravity: Skills](https://developers.google.com/antigravity/skills)

## 🤝 Contribuindo

Veja [CONTRIBUTING.md](CONTRIBUTING.md) para as diretrizes de contribuição e o processo de Pull Request.

Pull requests que alteram `skills/` são validados automaticamente pelo `skills-ref`, o validador de referência da especificação Agent Skills (workflow).

### Pré-requisitos para contribuir

- **Git**
- **Python 3.13 ou superior** — necessário para rodar o validador `skills-ref` localmente

### Como validar localmente antes de abrir o PR

O mesmo validador que roda no CI pode ser executado na sua máquina:

```bash
# 1. Clone este repositório
git clone https://github.com/lucianaregi/agent-skills-front.git
cd agent-skills-front

# 2. Clone o validador (commit fixo usado pelo CI)
git clone https://github.com/agentskills/agentskills.git .skills-ref
cd .skills-ref && git checkout 69ef37e9424c0a7ea9dd2293b559e43ec8176379 && cd ..

# 3. Instale o skills-ref
python -m pip install ./.skills-ref/skills-ref

# 4. Valide todas as skills
for dir in skills/*/; do
  skills-ref validate "$dir"
done
```

> No Windows (PowerShell), substitua o passo 4 por:
> ```powershell
> Get-ChildItem skills -Directory | ForEach-Object { skills-ref validate "skills/$($_.Name)/" }
> ```

Se a validação passar localmente, o check do CI também passará.

## 📄 Licença

Distribuído sob a licença MIT. Consulte o arquivo [LICENSE](LICENSE).

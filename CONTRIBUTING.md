# Contribuindo com o agent-skills-front

Obrigado pelo interesse em contribuir. Este documento explica como propor novas skills, o que uma contribuição deve respeitar e como funciona o processo de Pull Request.

## O que é uma skill

Uma skill é um arquivo `SKILL.md` com frontmatter YAML e instruções em Markdown que orientam um agente de IA durante uma tarefa específica. A especificação completa está em [Agent Skills](https://agentskills.dev).

Cada skill fica em um diretório próprio dentro de `skills/`, com o mesmo nome do campo `name` do frontmatter:

```
skills/
└── <nome-da-skill>/
    └── SKILL.md
```

## Tipos de skill

As skills deste repositório se dividem em dois tipos:

- **Genérica**: agnóstica de linguagem, framework, bundler e arquitetura. Não contém exemplos ou regras presos a uma stack específica. Aplicável a qualquer projeto frontend.
- **Específica de tecnologia**: assume a stack correspondente, que deve estar explícita no `name` e na `description` (ex.: `react-component-review`, `react-tailwind-standards`).

Não misturar: regras específicas de uma tecnologia não entram em skills apresentadas como genéricas.

## Escopo deste repositório

Este repositório cobre o domínio **frontend**: interfaces de usuário, componentes, acessibilidade, performance de UI, busca de dados no cliente, segurança de frontend, testes de componentes e disciplinas correlatas.

**Não pertencem a este repositório:**
- Skills de backend (APIs HTTP, banco de dados, migrations, servidores)
- Skills de infraestrutura (Kubernetes, Docker, IaC)
- Skills de plataforma específica de backend (.NET, Java, Python)
- Skills genéricas de engenharia de software sem relação com frontend

Skills genéricas de engenharia de software (commit, refatoração, planejamento de tarefa, criação de PR) já estão incluídas e vêm do repositório [`agent-skills`](https://github.com/lucianaregi/agent-skills). Duplicar essas skills com o mesmo conteúdo não agrega valor.

## Como estruturar um SKILL.md

```markdown
---
name: <nome-do-diretório>
description: <quando invocar esta skill, em uma frase objetiva>
---

# <Título>

<Declarar se a skill apenas relata ou se altera arquivos.>

## Regras

- ...

## Procedimento (ou "O que verificar")

...

## Concluído quando

<Critério verificável de conclusão.>
```

Regras adicionais de formato:

- O campo `name` deve ser idêntico ao nome do diretório (letras minúsculas, números e hífens).
- A `description` define **quando invocar** a skill, não o que ela faz. Ela responde à pergunta: "em que situação devo usar esta skill?".
- Skills de revisão definem a escala de severidade (🛑 Bloqueante / ⚠️ Importante / 💡 Sugestão) e o formato da saída.
- A skill abre declarando se **apenas relata** ou se **altera arquivos**. Skills que só relatam não corrigem código sem pedido explícito.
- **Autoria**: se a skill criar ou alterar artefatos, incluir a regra de autoria — o agente não insere por iniciativa própria atribuição, assinatura, crédito ou identificação de si mesmo, nem indicação de conteúdo gerado por IA, salvo pedido do usuário ou regra explícita do projeto.

## Princípios de qualidade

- **Verificável**: os critérios da skill produzem achados concretos, não opiniões vagas.
- **Proporcional**: uma skill não cobre dois domínios completamente distintos. Responsabilidade clara.
- **Sem sobreposição**: antes de propor uma skill nova, verificar se o que ela cobre não está já em outra skill com fronteiras bem definidas.
- **Sem inventar equivalências**: não criar uma skill apenas para manter simetria com outro repositório se não houver necessidade real.
- **Pragmática**: diretrizes objetivas e verificáveis. Sem overengineering.

## Processo de Pull Request

1. Faça um fork do repositório e crie uma branch a partir de `main`.
2. Crie o diretório `skills/<nome-da-skill>/` com um `SKILL.md` seguindo o formato acima.
3. Se for uma skill nova, adicione-a à tabela **Skills Disponíveis** no `README.md`.
4. Abra o Pull Request para `main` com:
   - título que descreva a skill proposta (ex.: `feat(skills): adicionar vue-component-review`);
   - descrição explicando o propósito da skill, o tipo (genérica ou específica), e por que ela não se sobrepõe a skills existentes.
5. O workflow de validação (`validate-skills.yml`) roda automaticamente e valida o formato de todas as skills em `skills/`. O PR não pode ser integrado enquanto a validação falhar.

## Validação automática

Pull requests que alteram arquivos em `skills/` disparam o workflow **Validar Agent Skills**, que usa o `skills-ref` — o validador de referência da especificação Agent Skills — para verificar o formato de cada skill.

O validador verifica:
- Presença e formato do frontmatter YAML.
- Campo `name` idêntico ao nome do diretório.
- Campo `description` presente.
- Arquivo nomeado `SKILL.md`.

Se a validação falhar, o output do workflow indica qual skill e qual campo apresenta problema.

## Proteção da branch `main`

A branch `main` requer:
- **Check obrigatório**: o job `Validar skills com skills-ref` do workflow `validate-skills.yml` deve passar antes do merge.
- **Pull Request obrigatório**: commits diretos na `main` não são aceitos.

> ⚠️ **Configuração externa necessária**: as regras de proteção de branch acima dependem de configuração no GitHub (Settings → Branches → Branch protection rules ou Rulesets) e não podem ser aplicadas por arquivo versionado. Veja a seção abaixo.

### O que configurar no GitHub

Para ativar a proteção completa, o mantenedor do repositório deve configurar no GitHub:

1. **Branch protection rule** (ou Ruleset) para `main`:
   - ✅ Require a pull request before merging
   - ✅ Require status checks to pass before merging
     - Status check obrigatório: `Validar skills com skills-ref`
   - ✅ Require branches to be up to date before merging (recomendado)
   - ✅ Do not allow bypassing the above settings (recomendado)

2. O status check `Validar skills com skills-ref` só aparece na lista de checks disponíveis depois que o workflow tiver rodado ao menos uma vez no repositório.

## Dúvidas

Abra uma issue descrevendo a skill que você quer propor antes de implementar, se quiser validar a ideia ou discutir o escopo antes de abrir um PR.

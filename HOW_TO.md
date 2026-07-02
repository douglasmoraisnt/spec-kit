# HOW TO — Guia prático do Spec Kit

Guia de uso do Spec Kit (Specify CLI) no dia a dia: projetos do zero, projetos existentes (bugs e features), boas práticas e uso centralizado/compartilhado entre vários projetos — incluindo a configuração para o Cursor.

## Índice

- [Conceito em 30 segundos](#conceito-em-30-segundos)
- [Instalação centralizada (uma vez, para todos os projetos)](#instalação-centralizada-uma-vez-para-todos-os-projetos)
- [Configurando o Cursor](#configurando-o-cursor)
- [Exemplo 1 — Projeto do zero (greenfield)](#exemplo-1--projeto-do-zero-greenfield)
- [Exemplo 2 — Projeto existente: nova feature (brownfield)](#exemplo-2--projeto-existente-nova-feature-brownfield)
- [Exemplo 3 — Projeto existente: corrigir bug](#exemplo-3--projeto-existente-corrigir-bug)
- [Boas práticas](#boas-práticas)
- [Uso compartilhado entre times e projetos](#uso-compartilhado-entre-times-e-projetos)

## Conceito em 30 segundos

O Spec Kit implementa **Spec-Driven Development (SDD)**: antes de codar, o agente de IA produz artefatos encadeados e verificáveis:

```text
constitution  →  specify  →  clarify  →  plan  →  tasks  →  analyze  →  implement
(princípios)     (spec.md)   (dúvidas)   (plan.md) (tasks.md) (auditoria) (código)
```

Cada comando é um "slash command"/skill no seu agente (`/speckit.specify`, `/speckit.plan`, ...). Os artefatos ficam versionados em `specs/<numero>-<feature>/` dentro do próprio repositório.

## Instalação centralizada (uma vez, para todos os projetos)

**Você NÃO copia este repositório para cada projeto.** O Specify CLI é um pacote Python que embute todos os templates, scripts e workflows. Instale a ferramenta uma única vez na máquina e use `specify init` em qualquer projeto — o bootstrap não precisa de rede, os templates vêm de dentro do pacote instalado.

### Opção A — Instalar do repositório oficial

```bash
# Requer uv (https://docs.astral.sh/uv/)
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@vX.Y.Z
```

### Opção B — Instalar do SEU fork/git privado (recomendado para times)

É assim que se distribui uma versão customizada (templates ajustados, presets e extensões da empresa) para todos os projetos e devs, sem copiar e colar nada:

```bash
# Fork público
uv tool install specify-cli --from git+https://github.com/<sua-org>/spec-kit.git@<tag-ou-branch>

# Git privado via SSH (funciona com GitHub, GitLab, Bitbucket, etc.)
uv tool install specify-cli --from git+ssh://git@github.com/<sua-org>/spec-kit.git@<tag-ou-branch>
```

Cada dev do time roda esse comando uma vez. Para atualizar todo mundo depois de mudar os templates no fork:

```bash
uv tool install specify-cli --force --from git+ssh://git@github.com/<sua-org>/spec-kit.git@<nova-tag>
```

> **E npm privado?** Não se aplica — o Spec Kit é um pacote **Python**, não npm. Os equivalentes são: git privado (opção acima, mais simples), um índice PyPI privado (Artifactory/Nexus/CodeArtifact, instalando com `uv tool install specify-cli --index-url ...`), ou wheels offline para ambientes air-gapped (ver `docs/install/air-gapped.md`).

### Verificação

```bash
specify version      # confirma a instalação
specify check        # lista integrações de agente disponíveis
specify self check   # avisa se há versão mais nova (somente leitura)
```

## Configurando o Cursor

Com o CLI instalado, dentro de qualquer projeto:

```bash
# Projeto novo
specify init meu-projeto --integration cursor-agent

# Projeto existente (na raiz do repo)
specify init . --force --integration cursor-agent
```

Isso cria:

- `.cursor/skills/speckit-*/SKILL.md` — as skills que o Cursor carrega automaticamente (specify, plan, tasks, implement, analyze, clarify, checklist, constitution, converge)
- `.specify/` — templates, scripts e memória do projeto (constitution)

No Cursor, invoque as skills no chat: `/speckit-specify`, `/speckit-plan`, etc. (ou peça em linguagem natural — "crie a spec para..." — que o agente usa a skill correspondente). Não é preciso instalar o CLI `cursor-agent`; a integração funciona só com o IDE.

**O que commitar:** commite `.cursor/skills/` e `.specify/` no repositório do projeto. Assim, qualquer dev (ou CI) que clonar o repo já tem o fluxo funcionando sem rodar `specify init` de novo.

## Exemplo 1 — Projeto do zero (greenfield)

```bash
mkdir minha-api && cd minha-api
specify init . --integration cursor-agent
git init
```

No chat do Cursor, na ordem:

```text
1. /speckit-constitution
   Defina os princípios: API REST em Python 3.12 + FastAPI, TDD obrigatório,
   cobertura mínima 80%, Conventional Commits, OWASP Top 10 para endpoints públicos.

2. /speckit-specify
   Sistema de cadastro de clientes com autenticação por e-mail/senha,
   perfis admin e operador, e exportação de relatórios em CSV.

3. /speckit-clarify
   (responda as até 5 perguntas que o agente fizer sobre pontos ambíguos)

4. /speckit-plan
   Use FastAPI + PostgreSQL + SQLAlchemy. Deploy em containers.

5. /speckit-tasks         # gera tasks.md rastreável (FR/SEC/AS -> tasks)
6. /speckit-analyze       # audita consistência spec x plan x tasks ANTES de codar
7. /speckit-implement     # executa as tasks, com gate de aceitação por user story
```

Resultado: `specs/001-cadastro-clientes/` com `spec.md`, `plan.md`, `tasks.md`, `research.md`, `data-model.md`, `contracts/` e o código implementado com testes.

## Exemplo 2 — Projeto existente: nova feature (brownfield)

```bash
cd projeto-legado
specify init . --force --integration cursor-agent   # --force: diretório não vazio
```

No chat do Cursor:

```text
1. /speckit-constitution
   Derive os princípios do que já existe no repo (CONTRIBUTING.md, configs de lint,
   padrões atuais). Não invente regras que conflitem com o código existente.

2. /speckit-specify
   Adicionar exportação assíncrona de pedidos para o módulo de relatórios existente.
```

O fluxo de spec/plan faz **grounding no código existente**: usa a terminologia real dos módulos, detecta sobreposição com features que já existem, e o plano registra convenções detectadas (guidelines, linters) e utilitários a reutilizar na seção *Codebase Context*.

```text
3. /speckit-clarify
4. /speckit-plan       # cita arquivos reais do repo, reusa em vez de reescrever
5. /speckit-tasks
6. /speckit-analyze
7. /speckit-implement
```

Para avaliar o quanto do que foi especificado já está coberto pelo código atual (útil em retomadas de trabalho):

```text
/speckit-converge      # compara codebase x artefatos e anexa as tasks restantes
```

## Exemplo 3 — Projeto existente: corrigir bug

Para bugs, o fluxo completo de spec é exagero. Use a extensão `bug` (empacotada no CLI):

```bash
specify extension add bug
```

Isso adiciona três comandos com guardrails (assess e test nunca alteram código; só o fix altera, e limitado ao escopo do assessment):

```text
# 1. Triagem — a partir de stack trace colado ou URL de issue
/speckit.bug.assess "TypeError: cannot read properties of undefined (reading 'token') at /auth/callback"
/speckit.bug.assess https://github.com/exemplo/repo/issues/1234 slug=callback-token

# 2. Aplicar a correção proposta no assessment
/speckit.bug.fix slug=callback-token

# 3. Validar (re-executa reprodução e testes, registra o veredito honesto)
/speckit.bug.test slug=callback-token
```

Cada bug gera `.specify/bugs/<slug>/` com `assessment.md`, `fix.md` e `test.md` — histórico auditável da correção.

## Boas práticas

**Fluxo**

- **Constitution primeiro, sempre.** É o contrato não-negociável que todos os comandos consultam. Sem ela, os gates de qualidade não têm referência.
- **Não pule o `clarify`.** Rodar `plan` com ambiguidade na spec é a maior fonte de retrabalho. Se for pular (spike descartável), assuma o risco explicitamente.
- **Rode `analyze` antes de `implement`, sempre.** É leitura pura e barata; pega requisito sem task, task sem requisito, cenário sem teste e violação de constitution antes de virar código.
- **Uma feature por `specify`.** User stories pequenas e independentes (P1 = MVP). Se a spec ficou gigante, quebre em duas features.

**Qualidade**

- **Testes são o padrão.** Cada cenário de aceitação (AS-###) deve ter task de teste; opt-out só com justificativa registrada no `tasks.md`.
- **Preencha a seção Security & Privacy da spec de verdade.** Classifique os dados, declare authn/authz e casos de abuso. "Not applicable" exige justificativa.
- **Use `/speckit-checklist` para domínios críticos** (segurança, UX, acessibilidade) antes do implement — o implement bloqueia se houver checklist incompleto.
- **Valide por story, não no final.** O implement tem gate de aceitação por user story: não deixe avançar para a próxima com cenário falhando.

**Versionamento**

- Commite `specs/`, `.specify/` e `.cursor/skills/` — os artefatos são documentação viva e permitem retomar o trabalho em qualquer máquina/sessão.
- Revise `spec.md` e `plan.md` no PR junto com o código: divergência entre spec e implementação é bug de processo.

## Uso compartilhado entre times e projetos

Estratégia recomendada para padronizar vários projetos sem copiar/colar:

1. **Fork privado do spec-kit** na organização (`git@github.com:<sua-org>/spec-kit.git`), com os ajustes de templates/comandos da empresa (como os desta branch).
2. **Tags de versão no fork** (`v1.0.0-org`, ...). Nunca aponte instalação de time para `main`.
3. **Instalação única por dev/CI** via `uv tool install specify-cli --from git+ssh://...@<tag>` (documente no onboarding).
4. **Bootstrap por projeto** com `specify init . --integration cursor-agent` e commit dos diretórios gerados.
5. **Customizações reutilizáveis** como [presets](presets/) (templates alternativos por domínio, ex.: compliance) e [extensões](extensions/) (comandos extras com hooks), em vez de editar projeto a projeto:

   ```bash
   specify init meu-projeto --integration cursor-agent --preset <preset-da-org>
   specify extension add bug
   ```

6. **Atualização controlada**: ao evoluir o fork, publique nova tag e peça `uv tool install --force ...@<nova-tag>`; nos projetos já inicializados, rode `specify init . --force` para re-sincronizar os templates (ver `docs/upgrade.md`).

| Alternativa | Quando usar |
|-------------|-------------|
| Git privado + `uv tool install` | Padrão. Simples, sem infra extra, suporta SSH/HTTPS |
| Índice PyPI privado (Artifactory/Nexus/CodeArtifact) | Empresa já tem registry Python e política de artefatos |
| Wheels offline (`docs/install/air-gapped.md`) | Ambiente sem acesso a git/PyPI |
| npm privado | **Não se aplica** — o projeto é Python, não Node |

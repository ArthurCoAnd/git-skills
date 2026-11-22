---
name: git-commit
description: 'Use esta skill quando o usuário quiser fazer um commit no Git ou dividir as alterações em mais de um commit. Use também quando o usuário digitar "/commit" ou pedir um conventional commit. A skill examina o diff, adiciona à área de staging os arquivos relacionados, escreve uma mensagem no padrão Conventional Commits em português do Brasil e executa git commit. Não use quando o usuário quiser apenas a mensagem de commit, sem fazer o commit. Não use para push, abrir pull request, rebase ou alterar o histórico de commits.'
license: MIT
compatibility: 'Requer Git. A opção `--resolved` do `git add` requer Git 2.56 ou superior. Versões mais antigas usam a alternativa do passo 3.'
allowed-tools: shell(git:*)
---

# Git Commit com Conventional Commits

Faça commits no Git que sigam a especificação [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/). Obtenha o tipo, o escopo e o resumo a partir do diff.

## Idioma dos commits

**Todas as mensagens de commit devem ser escritas em português do Brasil.** Isso vale para o resumo, o corpo e a descrição dos rodapés.

- Mantenha em inglês apenas os elementos fixos da especificação: o tipo (`feat`, `fix`, `docs` etc.) e os tokens de rodapé (`BREAKING CHANGE`, `Closes`, `Refs`).
- Escreva o escopo em minúsculas. Prefira o nome do módulo ou da pasta, como aparece no código (por exemplo, `auth`, `parser`).
- Não misture idiomas no resumo. Termos técnicos sem tradução usual (por exemplo, `token`, `endpoint`, `hook`) podem ser mantidos.
- Se uma regra do repositório exigir outro idioma, avise o usuário antes de commitar.

## Formato da mensagem

```text
<tipo>(<escopo>): <resumo>

<corpo>

<rodapé>
```

- O escopo, o corpo e o rodapé são opcionais. Sem escopo, escreva `<tipo>: <resumo>`.
- Escreva o resumo no modo imperativo, em português: "adicione", não "adicionado" nem "adiciona".
- Mantenha a primeira linha com no máximo 72 caracteres. Não coloque ponto final.
- No corpo, explique o que mudou e por quê.
- Escreva cada rodapé como `Token: valor` ou `Token #valor`, por exemplo `Refs: #456` ou `Closes #123`. O Conventional Commits aceita as duas formas, mas o Git só reconhece `Token: valor` como trailer. No token, use `-` no lugar de espaço.

## Tipos

O Conventional Commits define apenas `feat` e `fix`. Os outros tipos vêm da convenção do Angular ([`@commitlint/config-conventional`](https://github.com/conventional-changelog/commitlint/tree/master/%40commitlint/config-conventional)). Se o repositório tiver uma lista própria, use essa lista.

| Tipo       | Use para                                           |
| ---------- | -------------------------------------------------- |
| `feat`     | Uma nova funcionalidade                            |
| `fix`      | Correção de um bug                                 |
| `docs`     | Apenas documentação                                |
| `style`    | Apenas formatação, sem mudança na lógica           |
| `refactor` | Mudança de código que não é funcionalidade nem correção |
| `perf`     | Melhoria de desempenho                             |
| `test`     | Testes novos ou alterados                          |
| `build`    | O sistema de build ou as dependências              |
| `ci`       | A configuração de CI                               |
| `chore`    | Outras tarefas de manutenção                       |
| `revert`   | Reversão de um commit anterior                     |

## Mudanças que quebram compatibilidade

Coloque `!` antes dos dois-pontos ou adicione um rodapé `BREAKING CHANGE:`. Escreva o token em maiúsculas. `BREAKING-CHANGE:` também é válido.

```text
feat(api)!: remova os endpoints da v1

BREAKING CHANGE: os clientes devem usar os endpoints da v2.
```

## Fluxo de trabalho

### 1. Leia as regras do repositório

Procure as regras de commit antes de escrever uma mensagem:

- Uma configuração do commitlint, por exemplo `commitlint.config.js` ou `.commitlintrc.json`.
- O `CONTRIBUTING.md`.
- As mensagens recentes: `git log --oneline -10`.

Se uma regra do repositório for diferente desta skill, use a regra do repositório (exceto quanto ao idioma, conforme a seção "Idioma dos commits").

### 2. Examine as alterações

```bash
git status --porcelain
git diff --staged
git diff
```

Se não houver alterações, execute as verificações do início do passo 3. Se elas encontrarem `MERGE_HEAD`, continue. Se não, informe ao usuário o que `git status --long` mostra. Não faça um commit vazio.

### 3. Adicione os arquivos à área de staging

Primeiro, verifique se há uma operação em andamento. Execute cada comando separadamente. Não os encadeie com `&&`. Em um subdiretório, execute todos os comandos deste passo como `git -C <raiz> ...`. O comando `git rev-parse --show-toplevel` mostra a raiz.

```bash
git rev-parse -q --verify --symbolic-full-name MERGE_HEAD
git rev-parse -q --verify --symbolic-full-name CHERRY_PICK_HEAD
git rev-parse -q --verify --symbolic-full-name REVERT_HEAD
git diff --name-only --diff-filter=U --no-relative
git status --long
```

Um comando `rev-parse` mostra o nome de uma ref se a operação correspondente estiver em andamento. Caso contrário, o comando não mostra nada e termina com status 1. O comando `git diff` lista os arquivos não mesclados. Ele encontra conflitos que não têm ref, por exemplo os de `git stash pop`. `git status --long` mostra os demais casos.

**Se houver uma operação em andamento ou arquivos não mesclados**, não remova arquivos do staging. Não divida o commit. O índice guarda o resultado da operação. Se você remover um arquivo do staging, o Git descarta as alterações do outro lado ou remove uma entrada de conflito.

- Pare e avise o usuário se `git status --long` mostrar "rebase in progress", "You are in the middle of an am session", "Cherry-pick currently in progress" ou "Revert currently in progress".
- Execute `git status --porcelain=v2`. Uma linha que começa com `u` indica um arquivo não mesclado. Você só pode resolver o arquivo se todas estas condições forem verdadeiras:
  - A linha começa com `u UU N...` ou `u AA N...`.
  - Cada modo na linha é `100644`, `100755` ou `000000`.
  - `git diff --no-relative` não mostra `Binary files differ` para o arquivo.
- Pergunte ao usuário como resolver todos os outros arquivos não mesclados. O Git não consegue encontrar marcadores de conflito nesses arquivos. `git add --resolved` e as verificações abaixo adicionam esses arquivos sem avisar. Adicione cada um deles apenas depois que o usuário o resolver: `git add -- <caminho>`.
- Com Git 2.56 ou superior, adicione os arquivos que você pode resolver com `git add --resolved -- <caminhos>`.
- Com versões mais antigas, verifique esses arquivos e adicione-os:

```bash
git diff --check -- path/to/file1 path/to/file2
git add -- path/to/file1 path/to/file2
```

- Adicione os arquivos somente se `git diff --check` não encontrar nenhuma linha "leftover conflict marker". Ignore os avisos de espaço em branco vindos da outra branch.
- Depois, em qualquer versão do Git, execute `git diff --name-only --no-relative` e `git ls-files --others --exclude-standard`. Eles listam as alterações em outros arquivos. Ignore os arquivos que ainda não foram mesclados.
- Mostre esses caminhos ao usuário. Pergunte se eles pertencem ao commit. Adicione apenas os caminhos aprovados com `git add -- <caminhos>`.
- O Git coloca entre aspas alguns caminhos, por exemplo os que têm caracteres não ASCII. `git add` falha com um caminho entre aspas. Pergunte ao usuário sobre esses arquivos.

**Se não houver operação em andamento nem arquivo não mesclado**, coloque uma única mudança lógica em cada commit. Se os arquivos em staging contiverem mais de uma mudança, primeiro remova do staging os arquivos das outras mudanças. Depois, adicione os arquivos desta mudança:

```bash
git reset -q -- path/to/other-file1 path/to/other-file2
git add -- path/to/file1 path/to/file2
git add -- '*.test.*'
git add -- src/components/
```

- Coloque um glob entre aspas. Sem aspas, o shell expande o glob e não encontra os arquivos em subdiretórios.
- Não use `git add -p` nem `git add -i`. Esses comandos esperam uma entrada de uma pessoa.
- Examine a lista de arquivos em staging com `git diff --staged --name-only`. Não faça commit de segredos, por exemplo arquivos `.env`, `credentials.json` ou chaves privadas.

### 4. Escreva a mensagem

Obtenha cada parte a partir do diff:

- **Tipo**: o tipo da mudança.
- **Escopo**: o módulo ou a área que a mudança afeta.
- **Resumo**: o que mudou, em uma linha, **em português**.

### 5. Faça o commit

Quando o passo 3 encontrar `MERGE_HEAD`, `CHERRY_PICK_HEAD` ou `REVERT_HEAD`, execute `git commit --no-edit` sem caminhos e sem a opção `--cleanup`. Isso mantém a mensagem que o Git preparou. Não escreva uma nova mensagem com `-m`. A mensagem pode conservar as linhas de comentário `Conflicts:` do Git. Não as remova.

Em todos os outros casos:

Passe cada parágrafo com uma opção `-m` diferente. O Git coloca uma linha em branco entre os parágrafos. Este comando funciona no Bash, no PowerShell e em outros shells.

```bash
git commit -m "feat(auth): adicione renovação de token"
git commit -m "fix(parser): aceite entrada vazia" -m "Um arquivo vazio causava uma falha." -m "Closes: #123"
```

Para uma mensagem longa, escreva a mensagem em um arquivo temporário. Depois, execute `git commit -F <arquivo>`.

### 6. Confirme que o commit está correto

```bash
git log -1 --stat --first-parent
git status --porcelain
```

## Regras de segurança

- Não altere a configuração do Git.
- Não use `--no-verify`, a menos que o usuário peça. Os hooks podem estar em `.git/hooks` ou na configuração do Git.
- Se um hook falhar, o Git não faz o commit. Corrija o problema, adicione os arquivos ao staging novamente e execute o mesmo comando `git commit` outra vez. Não use `--amend`, porque ele altera o commit anterior.
- Não altere o histórico de commits, a menos que o usuário peça. Não use `git reset --hard`, `git rebase` nem o comando experimental `git history`.
- Não faça push, a menos que o usuário peça. Não use force push na branch padrão.

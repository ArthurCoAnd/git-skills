---
name: git-conventional
description: 'Prompt e fluxo de trabalho para gerar mensagens de commit no padrão Conventional Commits usando um formato XML estruturado. Orienta a criar mensagens de commit padronizadas e descritivas, de acordo com a especificação Conventional Commits, com instruções, exemplos e validação. As mensagens de commit devem ser escritas em português do Brasil.'
---

### Instruções

```xml
 <description>Este arquivo contém um modelo de prompt para gerar mensagens de commit no padrão Conventional Commits. Ele traz instruções, exemplos e diretrizes de formatação para ajudar a escrever mensagens de commit padronizadas e descritivas, de acordo com a especificação Conventional Commits.</description>
```

### Idioma dos commits

**Todas as mensagens de commit devem ser escritas em português do Brasil**: a descrição, o corpo e a explicação dos rodapés.

- Mantenha em inglês apenas os elementos fixos da especificação: o tipo (`feat`, `fix`, `docs` etc.) e os tokens de rodapé (`BREAKING CHANGE`, `Closes`, `Refs`).
- Escreva a descrição no modo imperativo, em português (por exemplo, "adicione", "corrija", "atualize").
- Termos técnicos sem tradução usual (por exemplo, `token`, `endpoint`, `hook`) podem ser mantidos.

### Fluxo de trabalho

**Siga estes passos:**

1. Execute `git status` para revisar os arquivos alterados.
2. Execute `git diff` ou `git diff --cached` para inspecionar as alterações.
3. Adicione as alterações ao staging com `git add <arquivo>`.
4. Monte a mensagem de commit, em português, usando a estrutura XML abaixo.
5. Depois de gerar a mensagem, execute o comando a seguir no terminal integrado (sem pedir confirmação):

```bash
git commit -m "tipo(escopo): descrição"
```

1. Basta executar este prompt e o commit será feito no terminal.

### Estrutura da mensagem de commit

```xml
<commit-message>
 <type>feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert</type>
 <scope>()</scope>
 <description>Um resumo curto, no imperativo e em português, da mudança</description>
 <body>(opcional: explicação mais detalhada, em português)</body>
 <footer>(opcional: por exemplo, BREAKING CHANGE: detalhes, ou referências a issues)</footer>
</commit-message>
```

### Exemplos

```xml
<examples>
 <example>feat(parser): adicione suporte à leitura de arrays</example>
 <example>fix(ui): corrija o alinhamento do botão</example>
 <example>docs: atualize o README com instruções de uso</example>
 <example>refactor: melhore o desempenho do processamento de dados</example>
 <example>chore: atualize as dependências</example>
 <example>feat!: envie e-mail no cadastro (BREAKING CHANGE: o serviço de e-mail passa a ser obrigatório)</example>
</examples>
```

### Validação

```xml
<validation>
 <type>Deve ser um dos tipos permitidos. Veja <reference>https://www.conventionalcommits.org/en/v1.0.0/#specification</reference></type>
 <scope>Opcional, mas recomendado para dar clareza.</scope>
 <description>Obrigatória. Escreva em português e use o modo imperativo (por exemplo, "adicione", não "adicionado").</description>
 <body>Opcional. Use para dar contexto adicional, em português.</body>
 <footer>Use para mudanças que quebram compatibilidade ou referências a issues.</footer>
</validation>
```

### Etapa final

```xml
<final-step>
 <cmd>git commit -m "tipo(escopo): descrição"</cmd>
 <note>Substitua pela mensagem que você montou, em português. Inclua corpo e rodapé, se necessário.</note>
</final-step>
```

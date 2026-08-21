# Guia completo do Git

## Do zero ao avançado

Este material apresenta o Git de forma progressiva: primeiro a ideia de controle de versão, depois o fluxo diário, a colaboração com GitHub e, por fim, recursos para trabalhar com mais segurança e produtividade.

> **Objetivo:** ao terminar, você deverá conseguir criar um repositório, registrar mudanças, trabalhar com branches, colaborar por pull requests, desfazer operações com segurança e investigar o histórico de um projeto.

## Sumário

- [1. O que são Git e GitHub](#1-o-que-sao-git-e-github)
- [2. Instalação e configuração](#2-instalacao-e-configuracao)
- [3. O ciclo de vida de um arquivo](#3-o-ciclo-de-vida-de-um-arquivo)
- [4. Primeiro repositório](#4-primeiro-repositorio)
- [5. Commits bem escritos](#5-commits-bem-escritos)
- [6. Branches e integração](#6-branches-e-integracao)
- [7. GitHub e colaboração](#7-github-e-colaboracao)
- [8. Desfazendo mudanças](#8-desfazendo-mudancas)
- [9. Histórico e investigação](#9-historico-e-investigacao)
- [10. Ferramentas avançadas](#10-ferramentas-avancadas)
- [11. Segurança e boas práticas](#11-seguranca-e-boas-praticas)
- [12. Referência rápida](#12-referencia-rapida)

---

## 1. O que são Git e GitHub

**Git** é um sistema distribuído de controle de versão. Ele guarda a evolução de arquivos em uma sequência de commits. Cada commit é um ponto identificado na história, com autor, data, mensagem e o conjunto de mudanças registrado.

**GitHub** é uma plataforma que hospeda repositórios Git e oferece colaboração, revisão de código, issues, permissões, automações e publicação de projetos. Git funciona localmente; GitHub é um dos lugares onde um repositório remoto pode viver.

### Por que usar controle de versão?

- Recuperar uma versão anterior sem duplicar pastas.
- Entender quem alterou cada trecho e por quê.
- Trabalhar em paralelo sem sobrescrever o trabalho de outras pessoas.
- Revisar mudanças antes de integrá-las.
- Automatizar testes, builds e deploys.

### Modelo mental

```text
working tree  ->  staging area  ->  repository local  ->  repository remoto
     editar          git add             git commit             git push
```

O Git não salva automaticamente tudo o que foi alterado. Você escolhe o que vai para o próximo commit, o que torna a história mais organizada.

## 2. Instalação e configuração

Instale o Git pelo site oficial: <https://git-scm.com/downloads>. Depois confira a instalação:

```bash
git --version
```

Configure sua identidade. Ela aparecerá nos commits:

```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu@email.com"
```

Confira as configurações:

```bash
git config --global --list
git config user.name
git config user.email
```

Você também pode definir o nome inicial da branch:

```bash
git config --global init.defaultBranch main
```

### Autenticação no GitHub

Para operações remotas, use HTTPS com o Git Credential Manager ou SSH. Nunca coloque senha, token ou chave privada em arquivos do projeto.

## 3. O ciclo de vida de um arquivo

Um arquivo pode passar por estes estados:

- **Untracked:** o Git ainda não acompanha o arquivo.
- **Modified:** o arquivo acompanhado foi alterado.
- **Staged:** a alteração foi selecionada para o próximo commit.
- **Committed:** a alteração já faz parte da história local.
- **Pushed:** o commit foi enviado ao repositório remoto.

Comandos principais:

```bash
git status                 # mostra o estado atual
git add arquivo.txt        # prepara um arquivo
git add .                  # prepara alterações da pasta atual
git diff                   # mostra mudanças ainda não preparadas
git diff --staged          # mostra mudanças preparadas
git commit -m "mensagem"  # cria um commit
git log --oneline          # resume o histórico
git push                   # envia commits ao remoto
git pull                   # baixa e integra atualizações
```

## 4. Primeiro repositório

### Criando um projeto local

```bash
mkdir meu-projeto
cd meu-projeto
git init
echo "# Meu projeto" > README.md
git status
git add README.md
git commit -m "docs: adiciona README inicial"
```

### Clonando um projeto existente

```bash
git clone https://github.com/usuario/projeto.git
cd projeto
```

### Ligando um projeto local ao GitHub

Crie um repositório vazio no GitHub e conecte-o ao projeto local:

```bash
git remote add origin https://github.com/usuario/projeto.git
git branch -M main
git push -u origin main
```

Comandos úteis para remotes:

```bash
git remote -v
git remote get-url origin
git remote set-url origin NOVA_URL
```

## 5. Commits bem escritos

Um bom commit é pequeno, completo e fácil de explicar. Ele deve representar uma intenção, não uma coleção aleatória de arquivos.

### Exemplos de mensagens

```text
feat: adiciona validação de e-mail
fix: corrige cálculo do carrinho
docs: explica instalação do projeto
refactor: separa serviço de autenticação
test: cobre cenário de usuário sem permissão
chore: atualiza dependências
```

Uma convenção comum é usar um prefixo curto, dois-pontos e uma descrição no presente. Evite mensagens vagas como `alterações`, `coisas novas` ou `final`.

### Atomicidade

Prefira isto:

```text
feat: adiciona campo de telefone
fix: trata resposta vazia da API
docs: adiciona exemplo de uso
```

A um único commit misturando feature, correção, formatação e documentação. Commits menores são mais fáceis de revisar, reverter e investigar.

## 6. Branches e integração

Uma branch é uma linha independente de desenvolvimento. A branch principal deve permanecer estável; novas ideias podem ser desenvolvidas em branches curtas.

```bash
git branch                         # lista branches
git switch -c feature/login         # cria e muda para uma branch
git switch main                    # volta para main
git branch -d feature/login        # remove branch local já integrada
git push -u origin feature/login   # publica a branch
```

### Merge

```bash
git switch main
git pull origin main
git merge feature/login
git push origin main
```

### Rebase

O rebase reaplica commits sobre uma nova base e deixa o histórico linear:

```bash
git switch feature/login
git fetch origin
git rebase origin/main
```

Não faça rebase de commits públicos compartilhados sem combinar com o time, pois ele reescreve a história.

### Conflitos

Quando duas alterações incompatíveis atingem o mesmo trecho, o Git marca o conflito:

```text
<<<<<<< HEAD
versão atual
=======
versão da branch
>>>>>>> feature/login
```

Resolva o arquivo manualmente, remova os marcadores e finalize:

```bash
git add arquivo-resolvido.txt
git commit
```

Durante um merge, `git merge --abort` cancela a operação. Durante um rebase, use `git rebase --abort`.

## 7. GitHub e colaboração

O fluxo mais comum é:

1. Atualizar a branch principal.
2. Criar uma branch curta para a tarefa.
3. Fazer commits pequenos.
4. Publicar a branch.
5. Abrir um Pull Request.
6. Receber revisão e ajustar o código.
7. Integrar a branch após aprovação.
8. Remover a branch que já foi encerrada.

```bash
git switch main
git pull origin main
git switch -c fix/menu-mobile
# editar, testar e registrar
git add .
git commit -m "fix: ajusta menu no celular"
git push -u origin fix/menu-mobile
```

### Pull Request

Um Pull Request deve explicar o problema, a solução, como testar e possíveis impactos. A revisão deve procurar comportamento incorreto, riscos, falta de testes e clareza, não apenas estilo.

### Issues e projetos

Use Issues para registrar bugs, tarefas e decisões. Use labels para organizar prioridade e tipo. Um bom título descreve o resultado esperado, e o corpo deve conter contexto suficiente para outra pessoa reproduzir o problema.

### `.gitignore`

Use `.gitignore` para impedir que arquivos locais ou secretos entrem no histórico:

```gitignore
node_modules/
.env
.env.*
!.env.example
*.log
.vscode/
.DS_Store
```

Não use `.gitignore` para esconder um segredo que já foi commitado. Nesse caso, remova o segredo, revogue a credencial e trate a limpeza do histórico com cuidado.

## 8. Desfazendo mudanças

Escolha o comando de acordo com o estado da alteração:

```bash
git restore arquivo.txt              # descarta alteração não staged
git restore --staged arquivo.txt     # tira arquivo do staging
git commit --amend                   # ajusta o último commit local
git revert HASH                      # cria novo commit que desfaz outro
git reset --soft HEAD~1              # remove último commit, mantém staging
git reset --mixed HEAD~1             # remove commit e staging, mantém arquivos
git reset --hard HEAD~1              # apaga commit e alterações locais
```

`git reset --hard` é destrutivo. Use-o somente quando tiver certeza de que não precisa dos dados. Para desfazer algo já publicado, prefira `git revert`.

## 9. Histórico e investigação

```bash
git log --oneline --graph --decorate --all
git show HASH
git diff HASH_A..HASH_B
git blame arquivo.txt
git log -S "texto procurado" -- arquivo.txt
git log -- arquivo.txt
```

### Bisect

`git bisect` usa busca binária para encontrar o commit que introduziu um problema:

```bash
git bisect start
git bisect bad                  # versão atual tem o problema
git bisect good HASH_ANTIGO     # versão antiga funciona
# teste cada versão indicada pelo Git
git bisect good                  # ou git bisect bad
git bisect reset
```

## 10. Ferramentas avançadas

### Stash

Guarde temporariamente mudanças que ainda não estão prontas para um commit:

```bash
git stash push -m "rascunho do menu"
git stash list
git stash show -p stash@{0}
git stash pop
```

Use stash como recurso curto, não como armazenamento permanente.

### Tags e versões

Tags marcam pontos importantes, como releases:

```bash
git tag -a v1.0.0 -m "primeira versão"
git push origin v1.0.0
git tag
```

### Cherry-pick

Aplique um commit específico em outra branch:

```bash
git cherry-pick HASH
```

É útil para transportar uma correção pontual, mas deve ser usado com rastreabilidade para evitar duplicação confusa.

### Hooks e automação

Hooks executam verificações em eventos do Git, como antes de um commit. Em projetos de equipe, ferramentas como Husky, lint-staged e pipelines de CI ajudam a padronizar essas verificações.

## 11. Segurança e boas práticas

- Nunca faça commit de tokens, senhas, chaves privadas ou arquivos `.env`.
- Use o menor nível de permissão necessário no GitHub.
- Revogue imediatamente uma credencial que foi exposta.
- Revise o diff antes do commit: `git diff --staged`.
- Mantenha branches pequenas e atualizadas.
- Faça pull antes de começar uma tarefa longa.
- Escreva documentação de instalação e execução.
- Automatize testes e lint no Pull Request.
- Evite forçar (`push --force`) em branches compartilhadas.
- Prefira `--force-with-lease` quando reescrita for realmente necessária.

## 12. Referência rápida

| Objetivo | Comando |
| --- | --- |
| Criar repositório | `git init` |
| Clonar projeto | `git clone URL` |
| Ver status | `git status` |
| Ver mudanças | `git diff` |
| Preparar arquivo | `git add arquivo` |
| Criar commit | `git commit -m "mensagem"` |
| Ver histórico | `git log --oneline` |
| Criar branch | `git switch -c nome` |
| Trocar branch | `git switch nome` |
| Atualizar projeto | `git pull` |
| Baixar referências | `git fetch` |
| Enviar commits | `git push` |
| Desfazer com segurança | `git revert HASH` |
| Guardar rascunho | `git stash` |
| Ver ajuda | `git help comando` |

## Exercício final

1. Crie uma pasta e inicialize um repositório.
2. Adicione um `README.md` com o objetivo do projeto.
3. Faça um commit usando uma mensagem clara.
4. Crie uma branch `feature/exercicio`.
5. Faça uma mudança, teste-a e abra um Pull Request no GitHub.
6. Simule um conflito em um arquivo e resolva-o.
7. Consulte o histórico com `log`, `show` e `blame`.
8. Crie a tag `v1.0.0` ao finalizar.

## Licença e contribuição

Este material é educacional. Adapte os exemplos ao seu projeto e revise os comandos destrutivos antes de executá-los. Sugestões e melhorias podem ser propostas por Pull Request.

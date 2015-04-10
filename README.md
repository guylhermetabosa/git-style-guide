# Guia de Estilo Git 

Este é um guia de estilo Git inspirado pelo [*How to Get Your Change Into the Linux
Kernel*](https://www.kernel.org/doc/Documentation/SubmittingPatches),
the [página do git](http://git-scm.com/doc) e várias práticas populares
dentro da comunidade.

Traduções do guia estão disponíveis nos seguintes idiomas :

* [Inglês Simplificado](https://github.com/agis-/git-style-guide)
* [Chinês Simplificado](https://github.com/aseaday/git-style-guide)

Se você se sentir confortável para contribuir, por favor, o faça! Dê um fork no projeto e abra um pull
request.

# Tabela de Conteúdo

1. [Branches](#branches)
2. [Commits](#commits)
  1. [Mensagens](#messages)
3. [Merging](#merging)
4. [Misc.](#misc)

## Branches

* Escolha nomes *curtos* e *descritivos*:

  ```shell
  # bom
  $ git checkout -b oauth-migration

  # ruim - muito vago
  $ git checkout -b login_fix
  ```

* Identificadores correspondentes de tickets de um serviço externo (Ex. uma Issue do GitHub
) também são bons candidatos para usar em nomes de branches. Por exemplo:

  ```shell
  # GitHub Issue #15
  $ git checkout -b issue-15
  ```

* Use *barras* para separar palavras.

* Quando várias pessoas estão trabalhando na *mesma* funcionalidade, pode ser conveniente
  ter um branch de funcionalidade *pessoal* e um branch de funcionalidade para a *equipe*.
  Use a seguinte convenção de nomenclatura:

  ```shell
  $ git checkout -b feature-a/master # team-wide branch
  $ git checkout -b feature-a/maria  # Branch pessoal da Maria
  $ git checkout -b feature-a/nick   # Branch pessoal do Nick
  ```

  Merge at will the personal branches to the team-wide branch (ver ["Merging"](#merging)).
  Eventualmente, o branch da equipe será integrado ao "master".

* Delete your branch from the upstream repository after it's merged (unless
  there is a specific reason not to).

  Dica: Use o seguinte comando quando estiver no "master" para listar os branches 
  que foram feitos merge:

  ```shell
  $ git branch --merged | grep -v "\*"
  ```

## Commits

* Cada commit deve ser uma *mudança lógica* simples. Não faça várias
  *mudanças lógicas* em um commit. Por exemplo, Se uma alteração corrige um bug e
  otimiza a performance de uma funcionalidade, o divida em dois commits separados.

* Não divida uma *mudança lógica* simples em vários commits. Por exemplo,
  a implementação de uma funcionalidade e os testes correspondentesa ela devem estar no mesmo commit.

* Commit *cedo* e *frequentemente*. Commits pequenos e autônomos são mais fáceis de entender e reverter 
  quando algo dá errado.

* Commits devem ser ordenados *logicamente*. Por example, se *commit X* depende
  de uma mudança feita no *commit Y*, então *commit Y* deve vir antes do *commit X*.

### Mensagens

* Use o editor, não o terminal, quando estiver escrevendo a mensagem do commit:

  ```shell
  # bom
  $ git commit

  # ruim
  $ git commit -m "Correção rápida"
  ```

  Committar do terminal encoraja uma ideia de ter que encaixar tudo em uma
  única linha, o que geralmente resulta em commits não informativos, mensagens ambíguas.

* O sumário (ie. a primeira linha da mensagem) deve ser
  *descritivo* ainda que *sucinto*. O ideal é que não seja maior que *50 caracteres*.
  Deve ser escrito com letra maiúscula e no modo imperativo.
  Não deve terminar com um ponto, uma vez que é efetivamente o título do *title*:

  ```shell
  # bom - modo imperativo, letr maiúscula, menos que 50 caracteres
  Marcar grandes registros como obsoleto quando insinuar falhas

  # ruim
  corrigido ActiveModel::Erros mensagens de depreciado falham quando o AR era usado fora do Rails.
  ```

* After that should come a blank line followed by a more thorough
  description. It should be wrapped to *72 characters* and explain *why*
  the change is needed, *how* it addresses the issue and what *side-effects*
  it might have.

  It should also provide any pointers to related resources (eg. link to the
  corresponding issue in a bug tracker):

  ```shell
  Short (50 chars or fewer) summary of changes

  More detailed explanatory text, if necessary. Wrap it to
  72 characters. In some contexts, the first
  line is treated as the subject of an email and the rest of
  the text as the body.  The blank line separating the
  summary from the body is critical (unless you omit the body
  entirely); tools like rebase can get confused if you run
  the two together.

  Further paragraphs come after blank lines.

  - Bullet points are okay, too

  - Use a hyphen or an asterisk for the bullet,
    followed by a single space, with blank lines in
    between

  Source http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
  ```

  Ultimately, when writing a commit message, think about what you would need
  to know if you run across the commit in a year from now.

* If a *commit A* depends on another *commit B*, the dependency should be
  stated in the message of *commit A*. Use the commit's hash when referring to
  commits.

  Similarly, if *commit A* solves a bug introduced by *commit B*, it should
  be stated in the message of *commit A*.

* If a commit is going to be squashed to another commit use the `--squash` and
  `--fixup` flags respectively, in order to make the intention clear:

  ```shell
  $ git commit --squash f387cab2
  ```

  *(Tip: Use the `--autosquash` flag when rebasing. The marked commits will be
  squashed automatically.)*

## Merging

* **Do not rewrite published history.** The repository's history is valuable in
  its own right and it is very important to be able to tell *what actually
  happened*. Altering published history is a common source of problems for
  anyone working on the project.

* However, there are cases where rewriting history is legitimate. These are
  when:

  * You are the only one working on the branch and it is not being reviewed.

  * You want to tidy up your branch (eg. squash commits) and/or rebase it onto
    the "master" in order to merge it later.

  That said, *never rewrite the history of the "master" branch* or any other
  special branches (ie. used by production or CI servers).

* Keep the history *clean* and *simple*. *Just before you merge* your branch:

    1. Make sure it conforms to the style guide and perform any needed actions
       if it doesn't (squash/reorder commits, reword messages etc.)

    2. Rebase it onto the branch it's going to be merged to:

       ```shell
       [my-branch] $ git fetch
       [my-branch] $ git rebase origin/master
       # then merge
       ```

       This results in a branch that can be applied directly to the end of the
       "master" branch and results in a very simple history.

       *(Note: This strategy is better suited for projects with short-running
       branches. Otherwise it might be better to occassionally merge the
       "master" branch instead of rebasing onto it.)*

* If your branch includes more than one commit, do not merge with a
  fast-forward:

  ```shell
  # good - ensures that a merge commit is created
  $ git merge --no-ff my-branch

  # bad
  $ git merge my-branch
  ```

## Misc.

* There are various workflows and each one has its strengths and weaknesses.
  Whether a workflow fits your case, depends on the team, the project and your
  development procedures.

  That said, it is important to actually *choose* a workflow and stick with it.

* *Be consistent.* This is related to the workflow but also expands to things
  like commit messages, branch names and tags. Having a consistent style
  throughout the repository makes it easy to understand what is going on by
  looking at the log, a commit message etc.

* *Test before you push.* Do not push half-done work.

* Use [annotated tags](http://git-scm.com/book/en/v2/Git-Basics-Tagging#Annotated-Tags) for
  marking releases or other important points in the history.

  Prefer [lightweight tags](http://git-scm.com/book/en/v2/Git-Basics-Tagging#Lightweight-Tags) for personal use, such as to bookmark commits
  for future reference.

* Keep your repositories at a good shape by performing maintenance tasks
  occasionally, in your local *and* remote repositories:

  * [`git-gc(1)`](http://git-scm.com/docs/git-gc)
  * [`git-prune(1)`](http://git-scm.com/docs/git-prune)
  * [`git-fsck(1)`](http://git-scm.com/docs/git-fsck)

# Licença

![cc license](http://i.creativecommons.org/l/by/3.0/88x31.png)

Este trabalho está sobre licença da Creative Commons Attribution 4.0
International license.

# Créditos

Agis Anastasopoulos / [@agisanast](https://twitter.com/agisanast) / http://agis.io

# Tradução
Guylherme Tabosa / [@tabosag](https://twitter.com/tabosag)

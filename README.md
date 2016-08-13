# Guia de Estilo Git

Este é um guia de estilo Git inspirado pelo [*How to Get Your Change Into the Linux
Kernel*](https://www.kernel.org/doc/Documentation/SubmittingPatches),
pela [página do git](http://git-scm.com/doc) e várias práticas populares
dentro da comunidade.

Traduções do guia estão disponíveis nos seguintes idiomas :

* [Coreano](https://github.com/ikaruce/git-style-guide)
* [Chinês Simplificado](https://github.com/aseaday/git-style-guide)
* [Francês](https://github.com/pierreroth64/git-style-guide)
* [Japonês](https://github.com/objectx/git-style-guide)
* [Inglês Simplificado](https://github.com/agis-/git-style-guide)
* [Ucraniano](https://github.com/denysdovhan/git-style-guide)

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

* Identificadores correspondentes de tickets de um serviço externo (Ex. Issues do GitHub
) também são bons candidatos para usar em nomes de branches. Por exemplo:

  ```shell
  # GitHub Issue #15
  $ git checkout -b issue-15
  ```

* Use *traços* para separar palavras.

* Quando várias pessoas estão trabalhando na *mesma* funcionalidade, pode ser conveniente
  ter um branch de funcionalidade *pessoal* e um branch de funcionalidade para a *equipe*.
  Use a seguinte convenção de nomenclatura:

  ```shell
  $ git checkout -b feature-a/master # Branch da equipe
  $ git checkout -b feature-a/maria  # Branch pessoal da Maria
  $ git checkout -b feature-a/nick   # Branch pessoal do Nick
  ```

  Realizar o merge nos branchs pessoais para o branch da equipe (ver ["Merging"](#merging)).
  Eventualmente, o branch da equipe será integrado ao "master".

* Apague seu branch do upstream do repositório depois de integrado (a menos
  que haja uma razão específica para não fazê-lo).

  Dica: Use o seguinte comando quando estiver no "master" para listar os branches
  que foram feitos merge:

  ```shell
  $ git branch --merged | grep -v "\*"
  ```

## Commits

* Cada commit deve ser uma *mudança lógica* simples. Não faça várias
  *mudanças lógicas* em um commit. Por exemplo, se uma alteração corrige um bug e
  otimiza a performance de uma funcionalidade, o divida em dois commits separados.

* Não divida uma *mudança lógica* simples em vários commits. Por exemplo,
  a implementação de uma funcionalidade e os testes correspondentes à ela devem estar no mesmo commit.

* Commit *cedo* e *frequentemente*. Commits pequenos e autônomos são mais fáceis de entender e reverter
  quando algo dá errado.

* Commits devem ser ordenados *logicamente*. Por exemplo, se *commit X* depende
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
  # bom - modo imperativo, letra maiúscula, menos que 50 caracteres
  Marcar grandes registros como obsoleto quando insinuar falhas

  # ruim
  corrigido ActiveModel::Errors mensagens de depreciado falham quando o AR era usado fora do Rails.
  ```

* Depois disso deve aparecer uma linha em branco seguido de mais descrição.
  Deve possuir *72 caracteres* e explicar *por que*
  a mudança é necessária, *como* está relacionado com a issue e o quais *efeitos colaterais*
  podem ter.

  Também deve fornecer qualquer referência aos recursos relacionados (eg. link para a issue correspondente em um bug tracker):

  ```shell
  Curto (50 chars ou menos) sumário de mudanças

  Texto explanatório mais detalhado, se necessário. Deve possuir
  72 caracteres. Em alguns contextos, a primeira linha
  é tratado como o assunto de um email e o resto
  do texto como o corpo. A linha vazia separando o sumário
  do corpo é crucial (a menos que você omita inteiramente
  o corpo); ferramentas como rebase podem se confudir se você roda os dois juntos.

  Parágrafos adicionais vem depois das linhas vazias.

  - Marcador circulares são permitidos também

  - Use um hífen ou asterisco para o marcador, seguido por um espaço simples, com linhas vazias entre eles

  Fonte http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
  ```

  Por fim, quando estiver escrevendo uma mensagem do commit, pense sobre o que você precisaria saber olhando para o commit daqui um ano.

* Se um *commit A* depende do *commit B*, a dependência deve ser evidenciada na mensagem
  do *commit A*. Use o hash do commit quando se referir a commits.

  Similarmente, se o *commit A* corrige um bug introduzido pelo *commit B*, deve ser evidenciada na mensagem
  do *commit A*.

* Se um commit sofrerá squash de outro commit use o `--squash` e
  `--fixup` sintaxes respectivamente, a fim tornar sua intenção clara:

  ```shell
  $ git commit --squash f387cab2
  ```

  *(Dica: Use a `--autosquash` marcação quando estiver realizando rebase. Os commits
  terão o squash realizado automaticamente.)*

## Merging

* **Não reescreva histórico publicado.** O histórico do repositório é valioso a sua maneira e muito importante para permitir dizer *o que realmente aconteceu*. Alterar histórico publicado é uma fonte comum de problemas para qualquer um que trabalhe no projeto.

* Contudo, há casos em que reescrever o histórico é legítimo. Estes são quando:

  * Você é o único trabalhando no branch e não está sendo inspecionado.

  * Você quer arrumar seu branch (eg. commits squash ) e/ou realizar rebase dele para o "master" para
    realizar o merge depois.

  Dito isso, *nunca reescreva o histórico do branch "master"* ou quaisquer
  branchs especiais (ie. usado em produção ou servidores de Integração Contínua).

* Mantenha o histórico *limpo* e *simples*. *Bem antes de realizar o merge* em seu branch:

    1. Tenha certeza que está em conformidade com o guia de estilo e realize qualquer ação necessária
       se não (squash/reordenar seus commits, refazer mensagens etc.)

    2. Rebase em outro branch em que será feito:

       ```shell
       [meu-branch] $ git fetch
       [meu-branch] $ git rebase origin/master
       # então merge
       ```

       Isto resulta em um branch que pode ser diretamente aplicado no final do
       branch "master" e resulta em um histórico bem simples.

       *(Nota: Esta estratégia é mais adequada para projetos com branches
        recentes. Caso contrário é melhor ocasionalmente realizar o merge do
        branch "master" em vez de fazer rebase nele.)*

* Se seu branch inclui mais de um commit, não faça merge como um branch avançado:

  ```shell
  # bom - garante que o commit de merge seja criado
  $ git merge --no-ff meu-branch

  # ruim
  $ git merge meu-branch
  ```

## Misc.

* Há vários fluxos de trabalho, e cada um tem suas forças e fraquezas.
  Se um fluxo de trabalho se encaixa para o seu caso, depende da equipe, do projeto e do seu
  processo de desenvolvimento.

  Dito isso, é importante escolher um fluxo de trabalho e permanecer com ele.

* *Seja consistente.* Isto é relacionado ao fluxo de trabalho, mas também se expande a coisas como, mensagens dos commits, nomes de branchs, tags. Ter um estilo consistente dentro do repositório torna as coisas mais fáceis para entender o que está acontecendo olhando no log, em uma mensagem do commit etc.

* *Teste antes de realizar o push.* Não suba trabalho não terminado.

* Use [Tags Anotadas](http://git-scm.com/book/en/v2/Git-Basics-Tagging#Annotated-Tags) para
  marcação de releases ou outros pontos importantes no histórico

  Prefira [lightweight tags](http://git-scm.com/book/en/v2/Git-Basics-Tagging#Lightweight-Tags) para uso pessoal, bem como para commit com marcações para referências futuras.

* Mantenha seu repositório em boa forma, realizando ocasionalmente tarefas de manutenção de performance em seu
  repositório local *e* remoto:

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

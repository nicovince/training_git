# Git training
## Config
```
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.br branch
git config --global color.ui true
git config --local user.name "John Doe"
git config --local user.email johndoe@sixpack.burp
```

## Telechargement du TP
```
git clone https://github.com/nicovince/training_git
cd training_git
git remote remove origin
```

## status

```
git status
```

Rien n'a été modifié, la copie de travail (working copy) est propre (clean).

Editez le fichier foo.txt et répondez à la première question, sauvegardez.

```
git status
```

le fichier apparait comme étant modifié, mais les modifications apportés ne sont pas encore portés dans la staging area

Mettez vos modifications apportés à foo.txt dans la stating area :

```
git add foo.txt
git status
```

Le fichier est toujours modifié, mais est désormais dans la staging area qui contient tout ce qui part dans le prochain commit

Si à cette étape vous modifiez à nouveau le fichier foo.txt, pour réagir à la 2ème phrase par exemple, git status vous dira alors qu'il est modifié en deux parties : une partie dans la staging area, une autre qui n'y est pas et ne fera pas parti du prochain commit.

Pour voir ce qui va partir dans le prochain commit :
```
git diff --cached
```

Pour voir ce que vous avez modifié depuis le dernier git add :
```
git diff
```


## commit
```
git commit -m "Vim Power, Bonjour Margueritte"
```
Dans ce cas là, le message passé entre guillemets formera le titre du commit, sans message.

```
git commit
```
Un editeur de texte s'ouvre et vous pourrez renseigner le titre du commit sur la première ligne et le détail du commit à partir de la 3ème ligne.
(git log permet de selectionner le titre ou le detail du commit)

```
git log -1
```
et vous verrez le dernier commit avec les infos de base (identifiant du commit, auteur, date, titre et message de commit)

oops, vous avez mis un message de commit pourri
```
git commit --amend
# ou bien
git commit --amend "un message de commit un peu mieux"
```

oops, j'ai oublié de commiter un fichier
```
git add foo.txt
git commit --amend
```

Attention, l'option `--amend`, modifie l'historique
Si vous avez fait un amend, faites un nouveau git log -1 et vous verrez que l'identifiant du commit n'est plus le même.


## grep
C'est dans quel fichier que je me suis mis à parler de vache ?
```
git grep vache
```
Par défaut, il cherche dans les fichiers indexés (tracked), il suffit d'ajouter --untracked pour chercher dans les fichiers non indexés.


## log
C'est dans quel commit que j'ai corrigé le bug 3615 ? Je sais que j'ai mis le numéro de bug dans le message de commit
```
git log --grep 3615
```

C'est quoi les 10 derniers commits ?
```
git log -10
```
et les 20 derniers ?
```
git log -20
```

C'est dans quel commit où je me suis mis à parler de vache dans un des fichiers indexés ?
```
git log -S vache
```

Il existe beaucoup d'autres options détaillées dans l'aide de la commande log
git help log
entre autres :
- afficher les logs à partir/avant/entre certaines dates
- afficher seulement certaines infos (sha1 reduit + titre) --oneline
- chercher par auteur `--author`
- voir les noms des fichiers modifiés `--name-only`
- voir les differences apportés `-p`


## branch et merge
### cas simple
Pour créer une branche, pour traiter un bug par exemple, sans déranger la branche main :
```
git branch hotfix_3617
```
Cette commande ne fait que créer la branche, vous êtes toujours sur la branche main, pour vous en assurer :
```
git branch # une petite étoile devant la branche courante
git status # sur la première ligne, votre branche courante est indiquée.
```

Pour aller sur cette branche :
```
git checkout hotfix_3617
```

La branche a été crée à partir de l'endroit où vous étiez.
Fixez le bug en répondant à la 3ème question
commitez le bugfix dans la branche hotfix_3617
```
git commit -am "bugfix 3617 : my boss could not figure that out"
```

Pour merger, on commence par aller dans la branche dans laquelle on veut merger, dans notre cas, on veut que la branche main contienne le bugfix 3617 :
```
git checkout main
```
et on merge la branche qui contient le bugfix :
```
git merge hotfix_3617
```
et voilà !
L'opération de merge a fait un fast forward car il n'y a eu aucune modification sur la branche main à partir du moment où la branche hotfix_3617 a été crée, le merge est donc trivial et consiste à déplacer le pointeur de branche main au niveau du pointeur de la branche hotfix_3617


### cas moins simple
Voyons le cas du merge où il y a eu des modifications sur la branche dans laquelle on merge depuis que la branche mergée a été créée.
Creation de la branche et positionnement sur cette branche, car je préfère taper une seule commande plutot que deux ( git branch + git checkout)
```
git checkout -b wtf_sncf
```
le flag `-b` permet de creer la branche, la commande checkout permet de se rendre sur la branche.
Répondez à la question 4, et commitez.

Retournez dans la branche main
```
git checkout main
```
Répondez à la question 5 et commitez (sur la branche main !)

pour voir la liste des branches qui n'ont pas encore été mergée sur la branche courante :
```
git branch --no-merged
```
pratique pour savoir ce qu'il reste à merger, du coup on merge wtf_sncf :
```
git merge wtf_sncf
```
un editeur se lance pour que vous validiez le message du commit de merge, sauvegardez, quittez, le merge est fait.

### cas avec un conflit
On crée une branche
On se déplace sur cette branche (bonus pour ceux qui font cette étape et la précédente en une commande)
On répond à la question 6
On commit
On retourne sur la branche main
On répond à la question 6 (une autre réponse)
On commit
Et on merge la branche créée dans main.

Si tout s'est bien passé, vous avez un conflit. Editez le fichier pour choisir la résolution (des marqueurs `<<<<<<`, `======`, `>>>>>>` sont présents pour aider à localiser le conflit), sauvegardez quittez.
Pour marquer le fichier comme résolu auprès de git on fait un `git add`.

Et on commit, le message de commit de merge indique les fichiers en conflits, vous pouvez indiquer comment vous avez résolu le conflit.

### nettoyage des branches
```
git branch -d le_nom_de_la_branche_a_effacer
```
Si la branche est déjà mergée dans la branche courante, l'opération est faite immédiatement. Si la branche n'est pas encore mergée, il faut mettre -D à la place de -d


## rebase
### rebase classique
on crée une branche sans s'y positionner
```
git branch test_rebase
```
Dans la branche main, on répond à la question 7, on commit.
On va dans la branche test_rebase
```
git checkout test_rebase et on répond à la question 8, et on commit.
```
Et on rebase.
```
git rebase main
```
Pour rappel, le rebase de la branche courante sur la branche main va modifier les commits pour faire comme si vous aviez travaillez sur la branche main (pas de divergence dans l'historique).
Du coup si on retourne dans main et que l'on merge on aura droit à un merge simple fast-forward
```
git checkout main
git merge test_rebase
```

### rebase interactif
L'idée est de reconstuire l'historique pour faire plein de choses exotiques, dans l'exemple que je donne je reste sobre et je me contente d'inverser les deux commits où l'on a répondu à la question 7 et à la question 8.

Tout d'abord il faut trouver l'identifiant du dernier commit que l'on ne veut pas toucher, si lors de la partie sur le rebase classique vous n'avez fait qu'un seul commit sur chaque branche (un sur main, un sur test_rebase) le dernier commit peut se trouver de façon suivante :
```
git log HEAD~2 -1
```
Explications :
- `log` : afficher les logs
- `HEAD~2` : HEAD est le dernier commit et `~2` désigne le 2ème ancêtre, la notation `~n` peut s'utiliser sur un nom de branche, un tag, un sha, on peut même faire `HEAD~1~1` (équivalent à `HEAD~2`)
- `-1` : n'afficher qu'un seul commit de résultat.

En résumé on n'affiche le 2ème ancêtre du dernier commit, soit le 3ème dernier commit.
On passe l'identifiant du commit à rebase
```
git rebase -i <sha_du_commit_qui_ne_sera_pas_modifié_par_le_rebase>
```
Pour info, on peut aussi passer à rebase l'expression utilisé dans la commande log :
```
git rebase -i HEAD~2
```
Dans l'editeur de texte qui apparait, le plus ancien commit se situe sur la première ligne (ordre inversé par rapport à `git log`).

Inversez les lignes 1 et 2, sauvegardez, quittez
```
git log -3
```
Les deux derniers commits ont été inversés.

Les options intéressantes du rebase sont fixup, squash pour merger plusieurs commits, et edit pour s'arrêter sur un commit et le compléter.


## Rollback
Pour défaire des commits, mais garder une trace dans l'historique :

Mettons que l'on veuille defaire les 2 derniers commits :
```
git revert HEAD~2..HEAD
```
l'editeur de texte se lance pour éditer le message de commit de revert pour chacun des commits à défaire


## Travail avec un dépôt distant
### Creation depot distant
On va créer un dépôt distant... sur votre machine, le principe reste le même où qu'il se situe
Mettez vous dans un répertoire qui n'est pas géré par git (ie: le répertoire parent de `training_git`), et on fait un clone "nu" (`--bare`)
```
git clone --bare training_git training_git_distant
```
un coup d'oeil dans `training_git_distant`, et vous verrez qu'il contient la même chose que `training_git/.git`.

On retourne dans `training_git` et on configure ce dépôt pour qu'il puisse accéder au dépôt distant :
```
git remote add jupiter ../training_git_distant
```
ici jupiter est le nom que j'ai donné au dépôt distant.

On peut lister les noms des dépôt distants déjà configurés avec :
```
git remote show
```
Pour récupérer les dernières modifications de jupiter :
```
git fetch jupiter
```
Que s'est il passé ?

git a récupéré les branches disponibles sur jupiter dans votre dépôt local :
```
git branch -a
```
Lorsque vous commitez, vous le faites dans vos branches locales, celles qui ne sont pas préfixés par le nom du remote.
On répond à la question 9, on commit
```
git log --oneline --decorate # On affiche l'historique en mettant les branches
```
La branche main de jupiter (jupiter/main) n'a pas bougé, c'est normal, avec git tout est local.
Pour pousser la branche main local vers la branche main de jupiter :
```
git push jupiter main:main
```
et jupiter a rattrapé son retard.
dans main:main, le premier main correspond au nom local de la branche que l'on veut pousser, et le deuxième au nom distant.


### collaboration
jupiter/main est une branche pour laquelle vous avez une copie locale (main), si jamais jupiter/main est en avance par rapport à votre copie locale, il faudra la merger avant de pouvoir la pousser.


Créons un clone du dépot distant dans lequel un martien va travailler (on se met au même niveau que `training_git_distant`)
```
git clone training_git_distant training_git_martian
```
Et dans cette copie on répond à la question 10, on commit et on pousse.

Petite subitlité, en clonant, le dépot origin est configuré pour pointer vers `training_git_distant`
```
git push origin main:main
# ou
git push
```
Du coup, dans le repertoire de départ on peut récupérer les dernières modifications apportées par le dépot `training_git_martian`
```
git fetch jupiter
```
A ce moment, les modifications ne sont pas encore visible sur les fichiers locaux mais ils sont présents sur le dépot local. Pour les récupérer :
```
git merge jupiter/main
```

### pousser une nouvelle branche vers le serveur
Rien de bien différent par rapport à ce qui a déjà été fait, mettons que vous avez une branche `bug_super_con_que_mon_collegue_ne_sait_pas_corriger`, ça fait un peut tâche de pousser cette branche avec ce nom sur le serveur :
```
git push jupiter bug_super_con_que_mon_collegue_ne_sait_pas_corriger:bugfix_offbyone
```
et la branche `bugfix_offbyone` apparaitra sur le serveur.
Le dépot `training_git` verra la branche `jupiter/bugfix_offbyone` après un `git fetch` et pourra se positionner dessus :
```
git checkout jupiter/bugfix_offbyone
```
qui a pour conséquence de se mettre sur l'état de la branche `jupiter/bugfix_offbyone`, vous n'êtes pas sur une branche, mais en mode _détaché_.

### Suppression des branches du serveur
Supprimer une branche locale, ne la supprime pas du serveur. On supprime une branche du serveur en poussant le néant dans la branche distante :
```
git push jupiter :bugfix_offbyone
```
Petite digression, on peut configurer des branches locales pour traquer des branches distantes (option `--track` lors du checkout d'une branche d'un remote, ou option `-u` quand on pousse une branche locale vers un dépôt distant). Du coup ça permet de simplifimer la commande de push en ne spécifiant que la branche locale que l'on souhaite pousser. Si d'aventure vos doigts rippent et vous placez un ":" avant le nom de la branche locale, vous vous retrouvez à supprimer la branche du serveur :s Ce n'est pas perdu car votre branche locale est toujours présente, mais c'est à savoir :)

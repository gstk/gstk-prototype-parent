# gstk-prototype-parent
### GIT UTILS todoo

# git bash
git config --global user.name "MON LOGIN"
git config --global user.name    (check = MON LOGIN)
git config --global user.email "MON MAIL"
git config --global user.email   (check)
git config --global --list  (check all infos)

git config --global --add remote.origin.proxy ""
git config --global --unset remote.origin.proxy

ssh-keygen -t rsa -b 4096 -C "MON MAIL"
ssh-add ~/.ssh/id_rsa   (~ remplace le user home)
clip < ~/.ssh/id_rsa.pub   (=> Copies the contents of the id_rsa.pub file to your clipboard)
# sur le site gitlab ou github => settings => barre de gauche clé ssh => copier la clé + clic add key


cd <PATH TO PROJET>
git clone git@ssh-<site gitlab ou github>.com:<nom repertoire projet>/<nom projet>.git


---------------------------------------------------
BASE
---------------------------------------------------

HEAD est le pointeur local sur la branche / instantané (=commit) courante


$ git init  => initialise un repo (local) git
$ git status 
$ git log  => historique des commits etc..
ignorer fichier : regarder dans fichier .gitignore
$ git diff  => visualiser ce qui a été modifié mais pas encore indexé
$ git diff --staged  (ou --cached) =>  compare les fichiers indexés et le dernier instantané (= commité)

$ git tag  => liste les tags
$ git tag -a v1.4 -m 'my version 1.4'  => faire un tag (de type annoté, vs leger)
$ git show v1.4  => detail du tag

$ git clean -f => remove untracked file ! clean A ETUDIER 



Etiqueté apres un commit precis (et anterieur) :
$ git log --pretty=oneline
166ae0c4d3f420721acbb115cc33848dfcc2121a début de l'ecriture support
9fceb02d0ae598e95dc970b74767f19372d61af8 mise à jour rakefile
$ git tag -a v1.2 -m 'version 1.2' 9fceb02   => mettre les 1er caracteres du sha du commit souhaité

Par défaut, la commande git push ne transfère pas les étiquettes vers les serveurs distants. 
Il faut explicitement pousser les étiquettes après les avoir créées localement. 
$ git push origin v1.2    => pour un tag précis
$ git push origin --tags  => pour pusher tout les tags

créer branche a partir d'un tag :
$ git checkout -b maBranche v1.0


---------------------------------------------------
LOCAL
---------------------------------------------------

$ git add fichier => ajoute fichiers a la zone d'index
$ git reset HEAD fichier => desindexe le fichier (mais garde les modifs locales) 
$ git checkout -- fichier =>  retour a l'etat initiale (dernier commit) du fichier (= revert en svn)
 
$ git rm  fichier => effacer + indexer l'effacement du fichier
$ git rm --cached fichier => indexer l'effacement du fichier MAIS garder la copie dans le repertoire de travail
$ git mv nom_origine nom_cible => indexer le renommage d'un fichier
         équivaut à :
         $ mv nom_origine nom_cible
         $ git rm nom_origine
         $ git add nom_cible

$ git commit -m message => commiter ce qui est indexé
$ git commit -a -m message => ajouter a l'index puis commiter (raccourci de add + commit)
$ git commit --amend  => "modifier" le dernier commit, = ajouter des modifs au dernier commit (ex: modif msg ou fichier oublié)

$ git revert HEAD~3 => Revert the changes specified by the 3 last commit in HEAD and create a new commit with the reverted changes.

$ git revert 444b1cff => annuler un commit



$ git checkout HEAD~2 => extrait le grand parent du commit courant  => HEAD détaché (ne pas commité ou creer une branche avant sinon commit orphelin)

$ git checkout hotfix
$ git revert HEAD~2  => crée un nouveau commit qui annule ces 2 changements et ajoute le nouveau commit au projet existant.

 git stash = mettre de coté et recuperer plus tard
$ git status  => il y a des modif, indexe et non encore indexe
$ git stash => mis de coté
$ git status => il y a n'y a plus de modif en cour

=> Les stashes ne sont pas transférés au serveur lors d'un push (ils sont locaux)
=> Git ne fera pas de stash des changements apportés aux fichiers non suivis ou ignorés.

$ git stash -u  => permet de stasher les fichier "untracked" egalement (ou mettre -a (all) pour avoir ne plus les fichier ignorés)


reappliquer les stash :
# git stash pop  (rceup les fichiers stashé et supprime le stash)
$ git status  => tout est revenu

$ git stash apply  => idem git stash pop mais conserve le stash, qui peut etre reappliqué apres (genre sauvegarde temporaire)


on peut faire plusieur stash, et les consulter en fesant :
$ git stash list
pour mieu les identifier, il faut les associé a un message comme ceci :
$ git stash save "add style to our site"


Par défaut, git stash pop réappliquera le stash créé le plus récemment : stash@{0}

sinon :
$ git stash pop stash@{2}  => choisir le stash à réappliquer en transmettant son identifiant 

$ git stash drop stash@{1} => supprimer un seul stash

$ git stash clear => supprimer tout les stash

$ git stash show -p  => infos


Créer une branche depuis votre stash => permet d'eviter les conflits
$ git stash branch add-stylesheet stash@{1}

---------------------------------------------------
BRANCHES
---------------------------------------------------

$ git branch  => liste les branches, met * devant la branche courante
$ git branch -v   => idem avec info sur le dernier commit
$ git branch --merged  => liste des branches qui ont ete fusionnées dans la branche courante
$ git branch --no-merged => liste des branches qui n'ont pas ete fusionnées dans la branche courante
$ git branch -d brancheName  => delete la branche brancheName (ERREUR si modifs faite et branche non fusionnée)
$ git branch -D brancheName  => delete la branche brancheName meme si elle n'a pas ete fusionnées

$ git branch nomBranche     => creer une branche
$ git checkout nomBranche   => bascule sur la branche existante nomBranche (= deplace le pointeur HEAD)
$ git checkout -b nomBranche  => git branch + git checkout : bascule une nouvelle branche nomBranche (creation depuis branche courante)

$ git checkout master
$ git merge correctif   => merge des modifs de correctifs dans la branche courante (ici master)
("Fast forward" si le dernier commit etait le meme,  "Merge made by recursive" sinon.)

raccourci de checkout + merge : # git merge correctif master


$ git checkout correctif
$ git merge master  => rapatrier des modifs de master dans correctif (svn update..)

merge conflict :
$ git status  => montre les fichiers unmerged
editer fichier en conflit
(outil : $ git mergetool)
puis $ git commit

---------------------------------------------------
REPO DISTANT
---------------------------------------------------

$ git clone git://github.com/<user>/<nom projet>.git  => copie toutes les branches / historique + lie le working directory a ce depot distant

$ git remote -v  => liste les depot distants du repertoire de travail (nom repo - "origin" par default + url)
$ git remote add nomNewRepo git://github.com/<user>/<nom projet>.git  => ajouter un repo distant au repertoire de travail
$ git remote rename oldRepoName newRepoName => renommer un repo distant en local
$ git remote rm repoName => supprimer repo distant en local
$ git remote show origin : inspecter repo distant origin (affiche les branches etc.)

$ git fetch nomNewRepo => recupere toutes les branches et infos de ce repo dans notre repertoire local (PAS DE MERGE - juste recup)
$ git fetch origin  (exemple de ci dessus - pour synchroniser votre travail)

$ git merge origin/brancheName  => merge de la branche distante vers la branche locale courante

$ git pull => fetch (rapatrier branche) + merge

$ git push [serveur repo distant] [branchedistante] (ex : git push origin master) 
=> pousser notre travail (no commits) de notre branche courante vers une branche du repo distant

__________________________________________________________________________________________

cas particulier : nouvelle branche "correctionserveur" dans le repo distant :

$ git fetch origin
remote: Counting objects: 20, done.
remote: Compressing objects: 100% (14/14), done.
remote: Total 15 (delta 5), reused 0 (delta 0)
Unpacking objects: 100% (15/15), done.
From git@github.com:<user>/<nom projet>
 * [new branch]      correctionserveur    -> origin/correctionserveur
 
Important : lorsque l'on récupère une nouvelle branche depuis un serveur distant, 
il n'y a pas de création automatique d'une copie locale éditable , donc
il n'y a pas de branche correctionserveur, seulement un pointeur 
sur la branche origin/correctionserveur qui n'est pas modifiable.

Pour fusionner ce travail dans votre branche actuelle de travail, 
vous pouvez lancer 

$ git merge origin/correctionserveur. 

Si vous souhaitez créer votre propre branche correctionserveur pour pouvoir y travailler, 
vous pouvez la baser sur le pointeur distant :

$ git checkout -b "correctionserveur" origin/correctionserveur

Branch correctionserveur set up to track remote branch refs/remotes/origin/correctionserveur.
Switched to a new branch "correctionserveur"

Cette commande vous fournit une branche locale modifiable basée sur l'état actuel de origin/correctionserveur.

branche de suivi : Les branches de suivi sont des branches locales qui sont en relation 
directe avec une branche distante

Lorsque vous clonez un dépôt, il crée généralement automatiquement une branche master 
qui suit origin/master. C'est pourquoi les commandes git push et git pull fonctionnent 
directement sans plus de paramétrage. Vous pouvez néanmoins créer d'autres branches 
de suivi si vous le souhaitez, qui ne suivront pas origin ni la branche master. 
Un cas d'utilisation simple est l'exemple précédent, en lançant 

$ git checkout -b [branche] [nomdistant]/[brancheDistante]. 

Si vous avez Git version 1.6.2 ou plus, vous pouvez aussi utiliser l'option courte --track :

$ git checkout --track origin/correctionserveur

Branch correctionserveur set up to track remote branch refs/remotes/origin/correctionserveur.
Switched to a new branch "correctionserveur"


Supprimer branche distante :

$ git push [nomRepoDistant] :[branchedistante]
= a lire comme  
$ git push [nomRepoDistant] [branchelocale]:[branchedistante]  avec branchelocale = vide (donc suppression)

__________________________________________________________________________________________

rebase : (attention, ne pas trop utiliser)
$ git checkout experience
$ git rebase master
=> vous prenez toutes les modifications qui ont été validées sur une branche et vous les rejouez sur une autre.




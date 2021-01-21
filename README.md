L'évaluation en Induction de Lexique Bilingue
========

TER M1, encadré par Emmanuel Morin et Martin Laville

Contexte
--------

L'Induction de Lexique Bilingue est une tâche qui consiste a produire des lexiques bilingues à l'aide de corpus eux aussi bilingues et parallèles ou comparables. Cette tâche attire de plus en plus de chercheurs, dont beaucoup venant du big data. Cela a pour conséquence que les résultats et la quantité de données traitées se retrouvent privilégiées vis à vis de la qualité de ces données (data scientist vs linguiste). Les données d'évaluations ont donc semble-t-il perdu en qualité et en sens. L'objectif de ce TER sera de récupérer certaines listes les plus mises en avant dans la recherche actuelle, pour pouvoir les évaluer, regarder leur contenu et finalement pouvoir mettre en place un protocole ou des règles qui permettraient d'obtenir des listes plus cohérentes.

Objectifs :
-Etudier des listes d'évaluation d'induction de lexique bilingue en évaluant la qualité de l'induction. Il faudra mettre en place une chaîne d'évaluation d'induction de lexique bilingue.
-Regarder ces listes plus en détails pour en retirer le surplus et l'incohérent, fixer un protocole de lissage.
-Evaluer de nouveau les résultats et analyser les différences.

Données
--------

- Un corpus comparable (Wiki) français / anglais pour commencer, du moins une fraction. Je vous ai normalement déjà fourni ça, sinon contactez moi.
- Des dictionnaires pour le mapping et l'évaluation, pareil, vous y avez déjà accès ici : https://github.com/facebookresearch/MUSE
Si vous n'avez qu'une seule liste, je vous conseille de prendre 80% pour le mapping et 20% pour l'évaluation. Mais n'utilisez pas la même liste pour les deux, c'est de la triche ! On aurait déjà la réponse dans nos données d'entrainement.


Entrainement des embeddings
--------

https://github.com/facebookresearch/fastText

Toutes les explications nécessaires sont sur leur git, la ligne de commande que vous utiliserez pour l'entrainement sera sûrement:
```
./fasttext skipgram -input <votreCorpus> -output <làOùVousVoulezQueCaAille> -dim 300 -epoch 5 -minCount 5 -ws 5 -minn 3 -maxn 6
```
 Il faut bien entrainer les embeddings sur les deux corpus séparémment. Et n'hésitez pas à tester d'autres paramètres !

Vous trouverez aussi sur ce github un lien vers des embeddings en 157 langues, ça pourra servir, mais je pense que ça sera plus intéressant pour vous si vous essayez d'abord d'entrainer vos propres embeddings.

Procédure de mapping
--------

Pour rappel, il est nécessaire de "mapper" les deux espaces d'embedding dans un seul espace pour pouvoir comparer les mots d'une langue avec ceux de l'autre et donc pouvoir chercher des traductions.

https://github.com/artetxem/vecmap

Le plus facile d'utilisation pour le moment, on testera peut être d'autres méthodes plus tard (MUSE par exemple), mais prenez au moins celle là en main.
Pareil, tout est expliqué sur le git, mais je vous met la commande qui vous concerne quand même :
```
python3 map_embeddings.py --supervised <votreDictionnaire> <vosEmbeddingsAnglais> <vosEmbeddingFrançais> <lesNouveauxEmbeddingsAnglais> <lesNouveauxEmbeddingsFrançais>
```

Faites bien attention d'avoir votre dictionnaire dans le même "sens" que l'ordre dans lequel vous donnez vos embeddings (ici, on donne l'anglais en langue source et le français en langue cible, il faut donc un dictionnaire anglais vers français). Et ça sera la même chose pour la liste d'évaluation.

L'évaluation de vos résultats
--------

Ca sera encore une fois sur le github https://github.com/facebookresearch/MUSE
Et voici la ligne de commande :
```
python evaluate.py --src_lang en --tgt_lang fr --src_emb <vosEmbeddingsAnglais> --tgt_emb <vosEmbeddingsFrançais --dico_eval <votreListeD'Evaluation>
```

Ca vous donnera le résultat en précision au top 1, 5 et 10. C'est assez simple à comprendre, la précision au top x correspond au fait qu'on a trouvé la traduction recherché pour un mot donné au moins au top X. Donc pour la précision au top 1, il faut avoir trouvé la bonne traduction immédiatement, au top 5 on pourra l'avoir en 1, 2, 3, 4 ou 5e position, etc.


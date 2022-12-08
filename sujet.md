# Practical Session #1: Introduction

1. Find in news sources a general public article reporting the discovery of a software bug. Describe the bug. If possible, say whether the bug is local or global and describe the failure that manifested its presence. Explain the repercussions of the bug for clients/consumers and the company or entity behind the faulty program. Speculate whether, in your opinion, testing the right scenario would have helped to discover the fault.

2. Apache Commons projects are known for the quality of their code and development practices. They use dedicated issue tracking systems to discuss and follow the evolution of bugs and new features. The following link https://issues.apache.org/jira/projects/COLLECTIONS/issues/COLLECTIONS-794?filter=doneissues points to the issues considered as solved for the Apache Commons Collections project. Among those issues find one that corresponds to a bug that has been solved. Classify the bug as local or global. Explain the bug and the solution. Did the contributors of the project add new tests to ensure that the bug is detected if it reappears in the future?

3. Netflix is famous, among other things we love, for the popularization of *Chaos Engineering*, a fault-tolerance verification technique. The company has implemented protocols to test their entire system in production by simulating faults such as a server shutdown. During these experiments they evaluate the system's capabilities of delivering content under different conditions. The technique was described in [a paper](https://arxiv.org/ftp/arxiv/papers/1702/1702.05843.pdf) published in 2016. Read the paper and briefly explain what are the concrete experiments they perform, what are the requirements for these experiments, what are the variables they observe and what are the main results they obtained. Is Netflix the only company performing these experiments? Speculate how these experiments could be carried in other organizations in terms of the kind of experiment that could be performed and the system variables to observe during the experiments.

4. [WebAssembly](https://webassembly.org/) has become the fourth official language supported by web browsers. The language was born from a joint effort of the major players in the Web. Its creators presented their design decisions and the formal specification in [a scientific paper](https://people.mpi-sws.org/~rossberg/papers/Haas,%20Rossberg,%20Schuff,%20Titzer,%20Gohman,%20Wagner,%20Zakai,%20Bastien,%20Holman%20-%20Bringing%20the%20Web%20up%20to%20Speed%20with%20WebAssembly.pdf) published in 2018. The goal of the language is to be a low level, safe and portable compilation target for the Web and other embedding environments. The authors say that it is the first industrial strength language designed with formal semantics from the start. This evidences the feasibility of constructive approaches in this area. Read the paper and explain what are the main advantages of having a formal specification for WebAssembly. In your opinion, does this mean that WebAssembly implementations should not be tested?

5.  Shortly after the appearance of WebAssembly another paper proposed a mechanized specification of the language using Isabelle. The paper can be consulted here: https://www.cl.cam.ac.uk/~caw77/papers/mechanising-and-verifying-the-webassembly-specification.pdf. This mechanized specification complements the first formalization attempt from the paper. According to the author of this second paper, what are the main advantages of the mechanized specification? Did it help improving the original formal specification of the language? What other artifacts were derived from this mechanized specification? How did the author verify the specification? Does this new specification removes the need for testing?

## Answers


# TP1 - VV - Quentin Le Lan

## Question 1
Vendredi 6 juillet vers 15h Orange qui est un des plus grands opérateurs en France constate un ralentissement sur son réseau. Vers 19h il n’y avait plus que 10% des communication qui étaient acheminés. Le réseau revient à la normale que vers 3h du matin le samedi. Près de 30 millions de personnes ont été touchés par ce bug pour un cout de plusieurs dizaines de millions d’euros. Ceci est dû à un bug logiciel sur un élément du cœur de réseau d’Orange. C’est une mise à jour sur le NG HLR (dès qu’un portable se déplace, tous les 500 mètres, il envoie des signaux HLR). Ce logiciel est composé de backend et de frontend. Une des requêtes était resté ouverte dans la mémoire tampon d’une des bases de données. Comme elle ne s’est pas fermé la mémoire à saturé au lieu de basculer sur d’autre base. Aucune alarme automatique n’a fonctionné pour prévenir de ce bug. Ce qui a eu effet de paralyser le réseau.

Ce bug est donc global (pas une simple ligne de code mais une interaction entre tout le logiciel)

Les clients n’ont donc pas eu de service pendant une dizaine d’heure et orange a subi des dégâts financiers important.

Cela aurait pu surement être évités si le fournisseur de logiciel NG HLR avait bien tester le logiciel sur un nombre de requête important pour voir les conséquences et si tout était correct. Orange aurait aussi pu faire des tests de non-régression du réseau après la mise à jour.

* [Source - 1](https://www.parismatch.com/Actu/Economie/Orange-revelations-sur-la-panne-geante-157766)
* [Source - 2](https://www.leparisien.fr/high-tech/panne-geante-d-orange-le-pdg-s-explique-11-07-2012-2086391.php)

## Question 2

*[Collection 813](https://issues.apache.org/jira/projects/COLLECTIONS/issues/COLLECTIONS-813?filter=doneissues)*

Ce bug est local causé par une mauvaise implémentation de la méthode par rapport à la spécification. La doc précise que la méthode doit soulever un NullPointerException lorsqu’un des paramètres est nulles, or ce n’est pas le cas. Le bug a été résolu en ajoutant une vérification sur les paramètres de la méthode. Maintenant il y a un test pour vérifier que l’exception est bien soulevée.


## Question 3

Netflix utilise le chaos engineering pour tester son système, grâce à cette méthode elle réalise des expériences concrètes comme par exemple :
* Eteindre des instances de machine virtuel
* Injecté de la latence dans les requêtes entre les services
* Erreur des requêtes entre les services
* Rendre une région entière indisponible.

Cependant cette méthode pour quelle soit efficace demande des exigences :
* Il faut varier les évènements réels
* Faire les tests dans l’environnement de production
* Automatisé ses tests
* Tester un système qui tourne en permanence et stable
* Effectuer les tests sur les heures de bureau en cas de problème

Pour vérifier que les tests soit bien passé Netflix utilise des métriques comme **SPS**. Celle-ci représente le nombre de personnes qui commence à lancer une vidéo toutes les secondes. Ils peuvent aussi utiliser le nombre de personne qui se connecte par seconde.

Leur technique pour montrer la réussite d’un test est d’effectuer une comparaison du SPS avec un **groupe de contrôle**. Ils prennent 2 populations d’utilisateur, effectue un élément perturbant le système sur un des groupes. L’autre groupe est dit « contrôle » car aucun évènement ne leur est fait. Il suffit de comparer le SPS des deux, s’il y a une trop grande différence c’est que le test est un échec

Netflix n’est pas la seule à utiliser se genre de pratique on peut trouver : Daylimotion, LinkeDin, …

## Question 4


WebAssembly est un langage bas niveau qui définit un format de code binaire. Basé sur des méthodes formelles il est sûr et portable. Les avantages d’avoir des méthodes formelles c’est que la validité des méthodes est basée sur des preuves mathématiques ce qui la rends très robuste face au bug. On peut prouver des spécifications du programme grâce à des preuves mathématique. Ce qui enlève l’ambiguïté de celui-ci. Cependant nous ne sommes jamais à l’abris d’un bug ou d’une mauvaise spécification du langage il est donc important de fournir un minimum de test.

## Question 5

La spécification mécanisée possède plusieurs avantages notamment que le code soit analysé et vérifier par un autre logiciel (ici utilisé avec Isabelle) ce qui permet de rendre le code beaucoup plus fiable et robuste.
 Ils ont pu améliorer le webAssembly en corrigeant des erreurs de la spécification de base, notamment sur l’exception Trap, le return ou encore les host fonction. Ils ont aussi corrigé le système de type qui était défectueux.

Grâce à la spécification mécanisé 2 artefacts ont été dérivés : interpréteur exécutable et un type checker.

Pour tester la spécification mécanisée ils ont utilisé les tests de conformité de webAssembly, ils ont aussi testé l’interpréteur exécutable par rapport à plusieurs moteur webAssembly. Ils ont essayé de tester la spécification en milieu réel en en convertissant des tests C généré en webAssembly.

Bien que cette méthode rajoute encore plus de fiabilité que le webAssembly classique il reste toujours important d’effectuer des tests car le risque 0 n’existe pas.

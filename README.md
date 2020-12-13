# TP : Introduction à la sécurité dans l'Internet des Objets (IoT)

Le but de ce TP est de nous offrir une petite introduction à la sécurité dans l'environnement IoT.

**Note : A la fin de la scéance, pensez à m'envoyer un compte-rendu (court) répondant aux différentes questions présentes dans ce TP (leo.mendiboure@univ-eiffel.fr)**


## 1. Petit retour sur les objets connectés

Si la sécurité des objets connectés apparait aujourd'hui comme un élément essentiel, c'est tout d'abord parce plusieurs centaines de millions d'objets circulent aujourd'hui et que ce nombre ne cesse d'augmenter. Il s'agit par conséquent d'un vecteur d'attaque important. C'est ensuite parce que ces objets rencontrent différents problèmes de sécurité :
- un manque de supervision : peu ou pas de mises à jour de sécurité, peu de moyen de supervisions/détection des attaques, accès physique potentiel à l'objet
- un manque de ressources : peu de ressources à consacrer à la sécurité (difficile déploiement de solutions cryptographiques, absence de sécurité pour les environnements d'exécution), peu de ressources (stockage, calcul, communication) dans l'absolu...
- une manque d'homogénéité : hétérogénéité architecturale (tant matérielle que logicielle), hétérogénéité des protocoles (tant en terme de communication que de sécurité)

Bien que ces objets connectés soient généralements associés à des gadgets dans l'imaginaire collectifs, comme l'on montré différentes attaquées passées ou actuelles (Jeep hack, Nest hack, Vtech hack, Mirai), la vulnérabilité de ces objets entraine des risques importants :
- attaques destructrices : ces attaques, potentiellement à grande échelle (Shodan), peuvent entraîner, par exemple, la déstabilisation de grands groupes industriels voire d'états (concurrents ?)
- espionnage : captation de données personnelles, détournement de l'usage de capteurs (micros, caméras, etc.)
- sabotage : perturbation du fonctionnement d'un service (ville intelligente, transport, énergie, etc.)
- détournement de l'utilisation d'objets connectés : Botnets (DDoS, Spam, etc.)


**Q.1** Rappelez quels sont les éléments présents dans l'architecture IoT. Déduisez-en à quel niveau peuvent se produire les attaques contre cette architecture.  

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://iot-analytics.com/understanding-iot-security-part-1-iot-security-architecture/

**Q.2** Expliquez rapidement ce qu'est l'OWASP et indiquez quelles sont les 10 principales vulnérabilités identifiées pour l'environnement IoT.

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://wiki.owasp.org/index.php/OWASP_Internet_of_Things_Project#tab=IoT_Top_10

Dans la suite de ce TP (par manque de temps), nous nous focaliserons sur les attaques au niveau réseau et au niveau de l'objet IoT lui même (logicielles et physiques).

Il est à noter que les mises en pratique proposées dans les sections **2** et **3** s'inspirent fortement des exercices pratiques proposés par le projet SEED (https://seedsecuritylabs.org/). Ce projet, mondial, vise à favoriser l'apprentissage de la sécurité informatique.


## 2. Une première mise en pratique : Attaques réseau

### 2.A Empoisenement du cache ARP

### 2.B 

## 3. Une seconde mise en pratique : Attaques logicielles

## 4. Une petite introduction théorique aux attaques par canaux auxilaires

## 5. Pour aller plus loin

Comme cela a déjà été indiqué, les expérimentations proposées dans les section **2** et **3** s'inspirent fortement des exercices pratiques proposés par le projet SEED (https://seedsecuritylabs.org/). 

**Q.** Indiquez, au delà des attaques réseau et logicielles, les questions auxquelles vous pourriez vous former grâce à ce projet.

Pour répondre à cette question, vous pourrez utiliser les informations présentées par : https://seedsecuritylabs.org/Labs_20.04/ 

Au delà des contremesures actuellement implémentées, de nombreux organismes travaillent à la définition de nouvelles solutions qui pourraient permettre de renforcer la sécurité des objets connectés. Parmi les solutions potentielles, une technologie que nous avons étudié dans le cadre d'un autre TP est fortement mise en avant : la Blockchain.

**Q.** Indiquez pourquoi la Blockchain, grâce à ses caractéristiques, pourrait sembler être une solution pertinente pour la sécurisation des objets connectés. Donnez également des exemples de solutions existantes basées sur la Blockchain.

Pour répondre à cette question vous pourrez utiliser les informations présentées par : https://www.itransition.com/blog/blockchain-iot-security 

Pour votre culture personnelle (ou votre divertissement ?) un outil, évoqué dans l'introduction de ce TP, qu'il pourrait vous être utile de connaître est Shodan.

**Q.** Qu'est ce que Shodan (https://www.shodan.io/) ? Comment cet outil semble-t-il fonctionner ? Comment pourrait-il être utilisé ?




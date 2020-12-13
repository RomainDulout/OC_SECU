# TP : Introduction à la sécurité dans l'IoT

## 1. Petit retour sur les objets connectés

Si la sécurité des objets connectés apparait aujourd'hui comme un élément essentiel, c'est tout d'abord parce plusieurs centaines de millions d'objets circulent aujourd'hui et que ce nombre ne cesse d'augmenter. Il s'agit par conséquent d'un vecteur d'attaque important. C'est ensuite parce que ces objets rencontrent différents problèmes de sécurité :
1. un manque de supervision : peu ou pas de mises à jour de sécurité, peu de moyen de supervisions/détection des attaques, accès physique potentiel à l'objet
2. un manque de ressources : peu de ressources à consacrer à la sécurité (difficile déploiement de solutions cryptographiques, absence de sécurité pour les environnements d'exécution), peu de ressources (stockage, calcul, communication) dans l'absolu...
3. une manque d'homogénéité : hétérogénéité architecturale (tant matérielle que logicielle), hétérogénéité des protocoles (tant en terme de communication que de sécurité)

Bien que ces objets connectés soient généralements associés à des gadgets dans l'imaginaire collectifs, comme l'on montré différentes attaquées passées ou actuelles (Jeep hack, Nest hack, Vtech hack, Mirai), la vulnérabilité de ces objets entraine des risques importants :
- attaques destructrices : ces attaques, potentiellement à grande échelle (Shodan), peuvent entraîner, par exemple, la déstabilisation de grands groupes industriels voire d'états (concurrents ?)
- espionnage : captation de données personnelles, détournement de l'usage de capteurs (micros, caméras, etc.)
- sabotage : perturbation du fonctionnement d'un service (ville intelligente, transport, énergie, etc.)
- détournement de l'utilisation d'objets connectés : Botnets (DDoS, Spam, etc.)





## 2. Une première mise en pratique : Attaques réseau

### 2.A Empoisenement du cache ARP

### 2.B 

## 3. Une seconde mise en pratique : Attaques logicielles

## 4. Une petite introduction théorique aux attaques par canaux auxilaires

## 5. Pour aller plus loin

Comme cela a déjà été indiqué, les expérimentations proposées dans les section *2* et *3* s'inspirent fortement des exercices pratiques proposés par le projet SEED (https://seedsecuritylabs.org/). Ce projet, mondial, vise à favoriser l'apprentissage de la sécurité informatique. 

Au delà des attaques réseau et logicielles présentées ici, le projet SEED pourrait également vous permettre de vous former à de nombreux autres types d'attaques et de contremesures) : 
- au niveau du système d'exploitation lui même (embarqué ou non) ;
- du système de chiffrement ; 
- des interfaces Web.

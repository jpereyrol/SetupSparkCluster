# Installation et Utilisation d'un cluster Spark

L'objectif de ce tutoriel est de vous apprendre a installer un cluster Spark ainsi qu'a l'utiliser. [EXPLICATION redaction francais]

**Table des matiéres**

* [Introduction](https://github.com/jpereyrol/SetupSparkCluster#1-introduction)
* [Configuration des esclaves](https://github.com/jpereyrol/SetupSparkCluster#2-configuration-des-slaves)
* [Configuration du maître](https://github.com/jpereyrol/SetupSparkCluster#3-configuration-du-maître)
* [SSH entre esclaves et maître](https://github.com/jpereyrol/SetupSparkCluster#4-configuration-de-openssh-entre-chaque-slave--master)
* [Utilisation du cluster](https://github.com/jpereyrol/SetupSparkCluster#5-lancement-dun-programme-spark-sur-le-cluster)

## 1. Introduction

Pour un cluster, on pense tout d'abord aux deux entités qui auront un rôle majeur dans celui-ci : Le maître (master) et les esclaves (slaves). Il nous faut donc une machine pour 'contrôler' ce cluster, et des esclaves pour lui obéir.

Plusieurs solutions s'offrent à vous pour ces 'nodes' :

  - Plusieurs ordinateurs (raspberry pi, ordinateur portable ...)
  - Amazon AWS
  - Installation d'une plateforme virtuelle avec plusieurs machines virtuelles pour simuler ce cluster

Afin que les différentes machines puissent communiquer entres elles il faut qu'elles soient dans le même sous-réseau (cablé ou non).

## 2. Configuration des slaves

En plus de devoir être dans le même sous-réseau il faut qu'elles aient toutes des IP différentes. Je précise cela car c'est un probléme récurrent lors des tests avec des machines virtuelles par exemple.

Pour le choix de l'OS, nous allons utiliser Ubuntu 16.04 pour sa stabilité et simplicité d'installation. Néanmoins j'assure qu'il est possible de faire la même chose sur Windows et MacOS, avec un peu plus de difficultées.

#### A. Installation de Java

**TODO**

#### B. Installation de Scala

Scala est dans les repositories xenial, il suffit donc d'exécuter la commande (dans le terminal) : 

```bash
sudo apt-get install scala
```

#### C. Installation de Spark

Vous pouvez télécharger la derniére version sur le [site](http://d3kbcqa49mib13.cloudfront.net/spark-2.1.0-bin-hadoop2.7.tgz), ou bien utilisez cette commande pour la version 2.1.0 (28 Décembre 2016) :

```bash
wget http://d3kbcqa49mib13.cloudfront.net/spark-2.1.0-bin-hadoop2.7.tgz
```

Il faut decompresser ce tgz pour ensuite placer cette librairie quelque part (/usr/share/ pour ma part, mais je ne sais pas si c'est le mieux ...) :

```bash
tar -xvzf spark-2.1.0-bin-hadoop2.7.tgz
sudo mv spark-2.1.0-bin-hadoop2.7 /usr/share/
```

#### D. Ajout des variables d'environnements

Une bonne méthode pour modifier des variables d'environement et s'assurer qu'elle sont initialisées a chaque démarrage est de les ajouter dans le *~/.bashrc* (ou zsh, fish ...)
Pour ouvrir votre fichier de configuration :

```bash
nano ~/.bashrc
```

Ensuite à la fin de votre fichier ajoutez : 

```bash
export SCALA_HOME=/usr/share/scala-2.11
export SPARK_HOME=/usr/share/spark-2.1.0-bin-hadoop2.7
export PATH=$PATH:$SPARK_HOME/bin
```

Pour rendre les changements effectifs :

```bash
source ~/.bashrc
```

## 3. Configuration du maître

#### A. Installation de Java

**[ Même chose que en 2.A ]**

#### B. Installation de Scala

**[ Même chose que en 2.B ]**

Scala est dans les repositories xenial, il suffit donc d'exécuter la commande (dans le terminal) : 

```bash
sudo apt-get install scala
```

#### C. Installation de Spark

**[ Même chose que en 2.C ]**

Vous pouvez vous même vous rendre sur le site de Spark pour télécharger la dernière version directement, ou bien utilisez cette commande pour la version 2.1.0 (28 Décembre 2016) :

```bash
wget http://d3kbcqa49mib13.cloudfront.net/spark-2.1.0-bin-hadoop2.7.tgz
```

Il faut decompresser ce tgz pour ensuite placer cette librairie quelque part (/usr/share/ pour ma part, mais je ne sais pas si c'est le mieux ...) :

```bash
tar -xvzf spark-2.1.0-bin-hadoop2.7.tgz
sudo mv spark-2.1.0-bin-hadoop2.7 /usr/share/
```

#### D. Ajout des variables d'environnements

**[ Même chose que en 2.D ]**

La meilleure méthode pour modifier des variables d'environement est de les ajouter dans le *~/.bashrc* (ou zsh, fish ...)
Pour ouvrir votre fichier de configuration :

```bash
nano ~/.bashrc
```

Ensuite à la fin de votre fichier ajoutez : 

```bash
export SCALA_HOME=/usr/share/scala-2.11
export SPARK_HOME=/usr/share/spark-2.1.0-bin-hadoop2.7
export PATH=$PATH:$SPARK_HOME/bin
```

Pour rendre les changements effectifs :

```bash
source ~/.bashrc
```

#### E. Configuation de /etc/hosts

**TODO**

#### F. Configuration des scripts pour lancer les slaves

- slaves.template -> slaves
  - TODO

- spark-env.sh.template -> spark-env.sh
  - TODO

## 4. Configuration de OpenSSH entre chaque slave / master

Pour chaque slave il faut faire l'action suivante :

Sur le MAÎTRE :

Générer une clé public : 

```bash
ssh-keygen -t rsa
```

Il faut ensuite ajouter cette clé public dans le fichier authorized_keys du slave, on peut le faire directement depuis le MAÎTRE

```bash
ssh-copy-id user@<ip-address or hostname>
```
*Il faudra entrer le mot de passe de la session slave pour se connecter :)*

Sur l'ESCLAVE :

Générer une clé public :

```bash
ssh-keygen -t rsa
```

Il faut ensuite ajouter cette clé public dans le fichier authorized_keys du maître, on peut le faire directement depuis l'ESCLAVE avec :

```bash
ssh-copy-id user@<ip-address or hostname>
```
*Il faudra entrer le mot de passe de la session maître pour se connecter :)*

## 5. Lancement d'un programme Spark sur le cluster

#### A. Spark-shell

#### B. Fat JAR (SBT)

**TODO**

_________________________________________________________________________________________________________________________________

#### Sources : 

- Spark GraphX in Action - *Michael S. Malak* and *Robin East* 

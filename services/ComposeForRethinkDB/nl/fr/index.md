---

copyright:
  years: 2016

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Initiation à {{site.data.keyword.composeForRethinkDB}}
{: #getting-started-with-compose-for-rethinkdb}

Dernière mise à jour : 20 septembre 2016
{: .last-updated}

{{site.data.keyword.composeForRethinkDB}} fournit une base de données répartie basée sur un document JSON avec une console d'administration et d'exploration. RethinkDB utilise le langage d'interrogation ReQL, construit autour d'un chaînage de fonctions et disponible dans des bibliothèques client pour Java, JavaScript, Python et Ruby. ReQL permet d'utiliser des fonctions côté serveur RethinkDB, par exemple, des jointures et des sous-requêtes réparties sur les noeuds du cluster. En outre, RethinkDB prend en charge des index secondaires permettant d'améliorer les performances des requêtes de lecture. La fonction la plus puissante de RethinkDB, changefeeds, permet de convertir un grand nombre de requêtes ReQL en flux en temps réel.
{:shortdesc}

**Remarque :** Les instances de service Compose qui ont été mises à disposition avant le 14 septembre 2016 et qui sont toujours actives peuvent encore être utilisées et sont directement accessibles sur le site [https://www.compose.com/](https://www.compose.com). Les instances de service Compose mises à disposition depuis cette date sont directement accessibles et utilisables dans votre compte Bluemix. 

Exécutez les étapes décrites ci-après pour commencer à utiliser {{site.data.keyword.composeForRethinkDB}}.

1. [Créez une instance {{site.data.keyword.composeForRethinkDB}}](https://console.ng.bluemix.net/catalog/services/compose-for-rethinkdb/).

  Lorsque vous créez une instance du service, prenez soin de sélectionner un nom pour votre service et un nom de données d'identification. Laissez le service non lié ; vous pourrez connecter une application à votre service ultérieurement en utilisant les données d'identification fournies lors de la mise à disposition du service. La section *Données d'identification disponibles* répertorie les différentes valeurs de données d'identification. 

2. Connectez-vous au service {{site.data.keyword.composeForRethinkDB}}. 

   Pour connecter une application à votre service, utilisez les données d'identification créées en même temps que le service. L'exemple d'application montre comment utiliser Node.js pour établir une connexion au service {{site.data.keyword.composeForRethinkDB}}. 

   Téléchargez l'exemple d'application [compose-rethinkdb-helloworld-nodejs](https://github.com/IBM-Bluemix/compose-rethinkdb-helloworld-nodejs) et suivez les instructions contenues dans le fichier Readme. Ensuite, cliquez sur **Afficher l'application** sur la page des détails d'application dans Bluemix.

## Données d'identification disponibles

Nom de zone|Description
----------|-----------
`uri`|URI à utiliser pour établir la connexion au service. Comprend le schéma (rethinkdb:), le nom d'utilisateur et le mot de passe administrateur, le nom d'hôte du serveur et le numéro de port auquel se connecter.
`uri_admin`|URI qui doit être consulté dans un navigateur pour pouvoir accéder à l'interface d'administration de la base de données. Cet accès requiert le nom et le mot de passe administrateur indiqués dans la zone `uri`.
`ca_certificate_base64`|Certificat autosigné utilisé pour confirmer qu'une application se connecte bien au serveur approprié. Le certificat est codé en base64. Vous devez décoder la clé avant de l'utiliser, comme illustré dans l'exemple d'application.
`deployment_id`|Identificateur interne du service, créé dans Compose.
`db_type`|Type de base de données fourni par le service, en l'occurrence, `rethink`.
`name`|Nom du déploiement de base de données.

# Liens connexes
{: #rellinks}

* [Compose](https://www.compose.com){:new_window}
* [Compose Articles](https://www.compose.com/articles/){:new_window}

## Tutoriels et exemples
{: #samples}
* [compose-rethinkdb-helloworld-nodejs](https://github.com/IBM-Bluemix/compose-rethinkdb-helloworld-nodejs){:new_window}

## Liens connexes
{: #general}
* [Aide sur Compose](https://help.compose.com/docs){:new_window}

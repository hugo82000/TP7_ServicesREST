# Compte Rendu TP7 Services REST

*Simon Le Guevel*
*Hugo Mathieux*

## Ecriture d'un serveur chat


Pour cette partie, nous avons récupéré les deux classes `Message` et `MessageList`, la première représentant les messages à manipuler et la seconde représentant la liste de ces messages pour les stocker sur le serveur.

Voici les méthodes à implémenter pour notre API :

| URL       | Type de requête     | Description|
| ------|-----|-----|
| /messages  	| **GET**	| *avoir la liste des messages* 	|
| /messages/after/{id}  	| **GET**	| *avoir les messages compris entre {id} et le dernier message du serveur* 	|
| /messages/create  	| **POST**	| *ajouter un message. Le message envoyé n'a pas d'id ni de date. C'est le serveur qui les initialise.* 	|
| /messages/delete/{id}  	| **DELETE** 	| *effacer le message d'ID {id}* 	|
| /messages/{id}	| **GET** 	| *avoir le message d'ID {id}* 	|


```jsx
ConnectionFactory factory = new ConnectionFactory();
factory.setUri("amqp://bkimztxi:kfDaEm6D0v51CY45fcsUgNB7vmMRM3a0@stingray.rmq.cloudamqp.com/bkimztxi");
Connection connection = factory.newConnection();
```
Cette étape permet de créer la connexion pour communiquer avec notre serveur.

```jsx
Channel channel = connection.createChannel();
```
Cette étape permet de créer le canal permettant d'échanger les messages.

**RabbitMq Manager**

Ensuite on utilise [RabbitMQ Manager](https://www.rabbitmq.com/) pour afficher les messages MQTT qui transitent sur le serveur.

## PARTIE 2 : Envoi d'une date

# Compte Rendu TP7 Services REST

*Simon Le Guevel*
*Hugo Mathieux*

## Ecriture d'un serveur chat


Pour cette partie, nous avons récupéré les deux classes `Message.java` et `MessageList.java`, la première représentant les messages à manipuler et la seconde représentant la liste de ces messages pour les stocker sur le serveur.

Voici les méthodes à implémenter pour notre API :

| URL       | Type de requête     | Description|
| ------|-----|-----|
| /messages  	| **GET**	| *avoir la liste des messages* 	|
| /messages/after/{id}  	| **GET**	| *avoir les messages compris entre {id} et le dernier message du serveur* 	|
| /messages/create  	| **POST**	| *ajouter un message. Le message envoyé n'a pas d'id ni de date. C'est le serveur qui les initialise.* 	|
| /messages/delete/{id}  	| **DELETE** 	| *effacer le message d'ID {id}* 	|
| /messages/{id}	| **GET** 	| *avoir le message d'ID {id}* 	|

### Question

**1. Faites en sorte que la classe Message puisse être passée en XML.**

Pour cela il suffit d'ajouter d'associer la classe annotée avec un nœud racine d'un document XML comme suivant, la ligne `@XmlRootElement` que nous avons ajouté à la classe `Message.java`

```jsx
@XmlRootElement
public class Message {

  private Long id;
  private String content;
  private String date;
  }
```
## Classe MessageRessource

Nous avons donc créé la classe et remplie avec la partie de code fournie.

### PING

Pour un premier test, nous avons commencé par créer une petite méthode `@GET` servant de ping comme suivant :

```jsx
@GET
@Path("/ping")
public String getServerTime() {
        System.out.println("RESTful Service 'MessageService' is running ==> ping");
        return "received ping on "+new Date().toString();
    }
```

<img src="https://user-images.githubusercontent.com/48157631/81414531-687dc580-9147-11ea-8e68-83554bc3597e.jpg" length="500">
<img src="https://user-images.githubusercontent.com/48157631/81414526-674c9880-9147-11ea-81e0-ae2b0e350df0.jpg" length="500">

Notre serveur est donc accessible et retourne la date à laquelle la requête a été envoyée.

### AFTER

```jsx
@Path("/after/{id}")
@GET
@Produces({MediaType.APPLICATION_JSON,MediaType.APPLICATION_XML})

public List<Message> getMessagesAfter(@PathParam("id") Long id){

  System.out.println("message after " + id);
  return MessageList.getInstance().getMessagesAfter(id);
}
```
<img src="https://user-images.githubusercontent.com/48157631/81415181-5fd9bf00-9148-11ea-85f3-b1109cecc983.png" length="800">
<img src="https://user-images.githubusercontent.com/48157631/81415179-5f412880-9148-11ea-82a6-8caa7f0a4c83.png" length="500">

Le code retourne les messages qui se trouvent après l'ID spécifié dans l'URL. Nous avons eu du mal au début car dans l'URL nous mettions un URL qui était supérieur à 3 et donc aucun message ne pouvait s'afficher.

### BETWEEN

```jsx
@Path("/between/{id1}/{id2}")
@GET
@Produces({MediaType.APPLICATION_JSON,MediaType.APPLICATION_XML})

public List<Message> getMessageBetween(@PathParam("id1") Long id1, @PathParam("id2") Long id2){
	
  System.out.println("message between : " + id1+ " and "+ id2 );
  return MessageList.getInstance().getMessagesBetween(id1,id2);
	}
```

<img src="https://user-images.githubusercontent.com/48157631/81415791-2f465500-9149-11ea-959c-0aecd0a3f7ee.png" length="500">

Cette méthode retourne les messages entre l'ID1 et l'ID2 spécifié dans l'URL comme sur la capture. On remarque ici que l'affichage est en XML comparé à la capture précédente qui était au format JSON.

### MESSAGE (pour récupérer le message souhaité via son ID)

```jsx
@Path("/message/{id}")
@GET
@Produces({MediaType.APPLICATION_JSON,MediaType.APPLICATION_XML})

public Message getMessage(@PathParam("id") Long id) {
	
  System.out.println("message number : " + id);
  return MessageList.getInstance().getMessage(id);
	}
  ```
<img src="https://user-images.githubusercontent.com/48157631/81422376-2a869e80-9153-11ea-8947-48eb37f36735.png" length="500">
<img src="https://user-images.githubusercontent.com/48157631/81422381-2b1f3500-9153-11ea-9bed-6317f2ecc5b4.png" length="500">

Cette méthode retourne le message choisi via le paramètre ID entré dans l'URL, on voit sur la capture qu'avec l'ID 2 on récupère bien le message correspondant.

### MESSAGES (pour récupérer tous les messages) 

```jsx
@Path("/messages")
@GET
@Produces({MediaType.APPLICATION_JSON,MediaType.APPLICATION_XML})

public List<Message> getMessages(){

  System.out.println("All messages");
  return MessageList.getInstance().getMessages();
	}
  ```
<img src="https://user-images.githubusercontent.com/48157631/81422743-b13b7b80-9153-11ea-9096-511484b3680b.png" length="500">
<img src="https://user-images.githubusercontent.com/48157631/81422755-b4cf0280-9153-11ea-8ed9-bdc21b4afc6f.png" length="500">

Cette méthode est utilisée pour afficher tous les messages du chat. Elle sera utile par la suite pour 

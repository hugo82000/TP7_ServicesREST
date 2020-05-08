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

### Questions

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
**2. Ecrire la classe MessageRessource.java**

Nous avons donc créé la classe et remplie avec la partie de code fournie.

***PING***

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

***after***

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

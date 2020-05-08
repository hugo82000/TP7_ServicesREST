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

Cette méthode est utilisée pour afficher tous les messages du chat comme on peut l'observer sur la capture.

 ## INSOMNIA

Pour tester les commandes de création et de suppression de message mais également pour nous affranchir des instructions curl nous avons utilisé le logiciel Insomnia. En effet, nous avons trouvé intéressant de tester cet outil servant à envoyer des requêtes HTTP aux services basés sur REST.

**CREATE**

Premièrement nous avons créé la méthode d'ajout avec la méthode d'envoi `@POST`, on a fixé les contenus acceptés (XML,JSON) et on récupère l'objet avec getValue() comme ci-dessous :

```jsx
@POST
@Path("/create")
@Produces({ MediaType.APPLICATION_XML, MediaType.TEXT_XML, MediaType.APPLICATION_JSON })
@Consumes({ MediaType.APPLICATION_XML, MediaType.TEXT_XML, MediaType.APPLICATION_JSON})

public Message createMessage(JAXBElement<Message> element) {	//On indique que l'on attend un message
	
	Message message = element.getValue();
	
	System.out.println("Message sent : " + message);
	MessageList.getInstance().createMessage(message);
	return message; 					//On obtient le message désérialisé avec getValue
	}
```

**DELETE**

Pour la suppression de message on a également créé une methode mais cette fois `@DELETE` afin de supprimer le message spécifié par un ID entré en paramètres.

```jsx
@DELETE
@Path("/delete/{id}")
@Produces({ MediaType.APPLICATION_XML, MediaType.TEXT_XML, MediaType.APPLICATION_JSON })
public Response delMessage(@PathParam("id") Long id){
	
	MessageList.getInstance().delMessage(id);
	System.out.println("Message deleted is number : " + id);
	return Response.ok().build();
	}
```

**Test d'ajout de message**

Une fois l'implémentation des méthodes terminées on a utilisé Insomnia pour les tester :

On envoie un message en spécifiant le champ `Content`
<img src="https://user-images.githubusercontent.com/48157631/81425754-77b93f00-9158-11ea-9901-22b70af6e7bb.png" length="500">

Puis lors de l'affichage de tous les messages, on observe que le message a bien été ajouté à la liste et qu'il a pris l'ID suivant
<img src="https://user-images.githubusercontent.com/48157631/81425750-7720a880-9158-11ea-8eb6-c8a204516e82.png" length="500">

**Test de suppression de message**

Après avoir ajouté la méthode DELETE dans Insomnia en utilisant l'adresse suivante `http://localhost:8080/pr.tp.services/api/ressource/delete/3`, on lance une requête. Ensuite en relançant une requête qui affiche tout les messages on observe bien que le dernier message a bien été effacé.

<img src="https://user-images.githubusercontent.com/48157631/81426269-40975d80-9159-11ea-9b73-305148aade0d.png" length="500">


## Client du chat avec Javascript

Nous avons ensuite créé un client en JavaScript à l'aide des fichiers `chat.html` et `mais.js` fournis dans le TP.

### chat.html

Commençons par le fichier HTML contenant les informations de l'affichage de notre page WEB.
La page est divisée en deux parties :

Une affichant le contenu du chat (tous les messages)
```jsx
<div id = "chatContent">
</div>
```
L'autre affichant le formulaire d'envoi de message avec le bouton d'envoi.
```jsx
<!-- Envoyer un message -->
<form name="chatForm" action="" method="POST">
    <input type="text" name="ligne" value="" id="ligne">
    <button type="button" id="submitButton">send</button>
</form>
```
Nous avons également ajouté un bouton pour la suppression des messages

```jsx
<!-- Supprimer un message -->
<form name="chatDelete" action="" method="DELETE">
    <input type="text" name="ligne2" value="" id="ligne2">
    <button type="button2" id="submitButton2">delete</button>
</form>
```

**Rendu de la page**

<img src="https://user-images.githubusercontent.com/48157631/81427671-66bdfd00-915b-11ea-82fe-e3bef976613b.png" length="500">

### main.js

Nous avons d'abord modifié le temps de rafraichissement de la Liste de message à 2 secondes pour pouvoir debugger plus facilement notre code dans le mode de développement du navigateur.

```jsx
console.log("call update again in 2s");
setTimeout(updateMessages, 2000);
```

Ensuite nous nous sommes attaqués à la partie suppression de messages qui n'était pas implémentée de base.
Nous avons créé une méthode `deleteMessage(messageId)`
```jsx
    function deleteMessage(messageId) {
    	 var token = $('meta[name="csrf-token"]').attr('content');
    	
        $.ajax({
            type : 'DELETE',
            url : rootURL + "/delete/" + messageId,
            data: { _method: 'delete',  _token :token },
            success : function(data) {
                console.log("deleted message " + messageId);
                $("ligne2" + messageId).remove();
            },
            error : function(jqXHR, textStatus, errorThrown) {
                console.log('deleteMessage error: ' + textStatus);
            }
        });
    }
```

La variable messageId est récupérée dans le second champ de la page WEB, le formulaire utilisé pour la suppression de message en passant en paramètre le numéro de message que l'on veut supprimer.
```jsx
function deleteContent() {
        var ligne2 = $("#ligne2").val();
        $("#ligne2").val("");
        deleteMessage(ligne2);
    }
    $("#submitButton2").click(function() {
        deleteContent();
    });
```
On lance la fonction `deleteContent()` à l'appui sur le bouton `delete` et la fonction récupère le numéro entré pour le passer en paramètre dans la fonction `deleteMessage` créé précédemment qui va supprimer le message selectionné.

## Test de la page WEB

Pour tester la page WEB, nous avons utilisé le navigateur Microsoft Edge car nous avons trouvé que son mode développeur était plus intuitif que celui de Chrome.

**Page au lancement du serveur**

<img src="https://user-images.githubusercontent.com/48157631/81428984-69215680-915d-11ea-8eb2-aa2c44f5375b.png" length="500">

On voit bien ici que la liste des messages est mise à jour toutes les deux secondes.

**Ajout de messages**

<img src="https://user-images.githubusercontent.com/48157631/81429306-e51b9e80-915d-11ea-8e3a-b1f2a4acc582.png" length="500">

Ici nos deux messages ont été envoyés et ont été ajoutés à la liste. On peut d'ailleurs observer dans la console le dernier message qui a été reçu par exemple avec les champs suivants en développant l'étiquette d'information :

```jsx
content: "Ah oui ça marche"
date: "Fri May 08 18:57:25 CEST 2020"
id: 4
```

**Suppression de messages**

Pour ce dernier exemple nous avons supprimmé les messages id:0 et id:3

<img src="https://user-images.githubusercontent.com/48157631/81429747-899de080-915e-11ea-9415-ee181a8efbdb.png" length="500">

On peut observer qu'ils ont bien été supprimés et la console affiche le retour de notre fonction `deleteMessage`

```jsx
console.log("deleted message " + messageId);
```

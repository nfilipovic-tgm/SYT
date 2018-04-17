# Synchronisation bei mobilen Diensten

Diese Übung soll die möglichen Synchronisationsmechanismen bei mobilen Applikationen anhand einer Einkaufsliste aufzeigen.

##  Ziele

Das Ziel dieser Übung ist eine Anbindung einer mobilen Applikation an ein Webservices zur gleichzeitigen Bearbeitung von bereitgestellten Informationen

##  Funktionalität

* Synchronisation der Datensätze
* CRUD Implementierung
* Replikationsansatz
* Offline-Verfügbarkeit
* System global erreichbar

## Gewählte Schnittstelle

Um die Einkaufsliste bestmöglich zu realisieren wurde [Firebase](https://console.firebase.google.com/) genutzt. 

* Firebase Realtime Database
* Firebase Hosting

### Firebase Realtime Database

Firebase Realtime Database ist eine Cloud-hosted realtime [document store](https://db-engines.com/en/article/Document+Stores). Da bei einer Einkaufsliste keine umfangreichen Abfragen oder komplexe relationale Daten benötigt werden, eignet sich
Firebase als Datenbank sehr gut. Um Firebase Realtime Database zu nutzen muss man lediglich diesen Schritten folgen :
[Getting started with Firebase Realtime Database ](https://firebase.google.com/docs/web/setup?authuser=0)



##### Regeln 

Solange einem User/Client keine bestimmten Rechte (read,write) gegeben werden, werden die serverseititgen Datensätze 
auf den Client sozusagn repliziert. 

```
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

So sieht die standart Regeln von Firebase aus. EIn Upgrade auf den Tarif "Blaze" ermöglicht automatische Backups.


<p align="center">
  <img src="screenshots/start.JPG" alt="Start der Applikation"
       width="650" height="335">
</p>

Die schon vorhandenen Datensätze werden in der Konsole bereits angezeigt (Datensätze repliziert) . 

### Einkaufsliste 
Nun kann man mit der Implementierung der Einkaufsliste beginnen. Dabei muss man zuerst die Initialisierung von Firebase hinzufügen:

```diff

<script src="https://www.gstatic.com/firebasejs/4.12.1/firebase.js"></script>
<script>
  // Initialize Firebase
  var config = {
    apiKey: "AIzaSyBGEY0KjdViMZPUwvM6HJ9CAGmJT4jDvPE",
    authDomain: "todolist-80418.firebaseapp.com",
    databaseURL: "https://todolist-80418.firebaseio.com",
    projectId: "todolist-80418",
    storageBucket: "todolist-80418.appspot.com",
    messagingSenderId: "57315669570"
  };
  firebase.initializeApp(config);
</script>
```

Das Skript ist nicht bei jedem gleich un kann unter https://console.firebase.google.com/project/todolist-80418/overview gefunden werden 

Nachdem man die Einkaufsliste beliebig im Body definiert hat widmet man sich den Firebase Funktionen 

#### Insert 
```javascript
var firebaseRef = firebase.database().ref();
  
	function subClick() {
		// Get Element 
		var newitem = document.getElementById("newitem");
		var obj = document.getElementById("Liste");
		// Referenziert auf Child Liste 
		var insert = firebaseRef.child("Liste");
		// Fügt hinzu 
		insert.push(newitem.value);	
	}
```
**newitem** = ID der Eingabe in die Einkaufsliste 

**.push()** =  Fügt Datensatz in eine Liste in die Datenbank hinzu


#### Log

```javascript
var roots = firebaseRef.child("Liste");
	
	// Loggt alle hinzugefuegte produkte 
	roots.on('child_added', snap => console.log(snap.val()));
	
	
	// Loggt geloescht produkte 
	roots.on("child_removed", snap => console.log("geloescht:"+snap.val()));
		
	}
```

**console.log** = Loggt die Eingabe in der Web-Konsole mit

Nach jedem hinzugefügtem Datensatz wir durch `child_added` die Eingabe auf die Konsole mit gelogt.

Durch `child_removed` wird nur jedes gelöschte Objekt angezeigt.


##### Log der gesamten Datenstruktur als POJO 
```javascript

firebaseRef.on("value", function(snapshot) {
    for (x in snapshot.val()) {
        var xRef = firebase.database().ref();
        xRef.once("value", function(xsnapshot) {
            var data = xsnapshot.val();
            console.log(data);
        });
    }
});
		
```
`.on` gibt die Datenstruktur nach jedem Insert-Update-Delete aus, im Gegensatz zu `once`
, wo in der Konsole die Datenstruktur nur einmal ausgegeben wird (Problem: Die Änderungen werden nicht angezeigt).

Wird nicht verwendet 

###### Testen der Synchronisation

Um die Synchronisation der Einkaufsliste testen zu können, muss man die Einkaufsliste 2 mal ausführen und die Web-Konsole öffnen .

Nun kann auf beiden Seiten Produkte eingegeben werden und in beiden Konsolen wird die Änderung angezeigt.

<p align="center">
  <img src="screenshots/autosync.JPG" alt="Testen der Synchronisation"
       width="650" height="335">
</p>

Im Screenshot erkennt man, dass in einem Browser Produkte hinzugefügt werden und in beiden Konsolen die Änderung angezeigt wird. Dies ist eine typische state synchronization welche durch `child_added` durchgeführt wird. 
###  Firebase Hosting

Mit [Firebase Hosting](https://firebase.google.com/docs/hosting/) kann man mobile Web-Apps ganz einfach bereitstellen. Dieser Hoster wurde zusammen mit Firebase 
Realtime Database gewählt, da es sehr einfach aufzusetzen ist (und es zusätzlich kostenlos ist). 

#### Installation 

Die Firebase CLI benötigt [Node.js](https://nodejs.org/en/) version 5.10.0 or higher  

**Firebase CLI**

`npm install -g firebase-tools`

**Firebase Login** 

`firebase login`
Hier muss man nur mehr noch auf der Firebase Website bestätigen (Google Anmeldung) 

**Firebase Initialisieren**

`firebase init`

Bei der Konfiguration wählt man "Hosting" aus und erstellt ein `public` Ordner. Falls kein Index.html vorhanden
ist wird Firebase eines bereitstellen. 

**Firebase Lokal Testen**
`firebase serve`

Hierbei hat man eine kleine "Vorschau" wie die Seite letzendlich aussehen wird.

**Website bereitstellen**
`firebase deploy`

Nun wird ein Link angezeigt - dieser ist die gehostete Website

---


Meine Website: [Einkaufsliste](https://todolist-80418.firebaseapp.com/)


---


Meine Datenbank: [Einkaufsliste Firestore DB](https://console.firebase.google.com/project/todolist-80418/database/todolist-80418/data)


---


Autor: Nemanja Filipovic

Datum:17.04.2018 


---


Quellen:


Allgemeine Informationen und Tutorials: [Firebase Documentation](https://firebase.google.com/docs/)

Bei Problemen:[Stackoverflow](https://stackoverflow.com/) :-) 

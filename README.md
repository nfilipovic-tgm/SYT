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

Nachdem man die Einkaufsliste beliebig erstellt hat widmet man sich den Firebase Funktionen 

#### Insert 
```javascript
var firebaseRef = firebase.database().ref();
  
	function subClick() {

		var newitem = document.getElementById("newitem");
		firebaseRef.child("Liste").push(newitem.value);
		<!-- Loggt hinzugefuegte produkte --> 
		console.log("Value added: " + newitem.value);	
	}
```
**newitem** = ID der Eingabe in die Einkaufsliste 

**.push()** =  Fügt Datensatz in eine Liste in die Datenbank hinzu

**console.log** = Loggt die Eingabe in der Web-Konsole mit

##### Log der gesamten Datenstruktur
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
###### Testen der Synchronisation

Um die Synchronisation der Einkaufsliste testen zu können, muss man die Einkaufsliste 2 mal ausführen und die Web-Konsole öffnen .

Nun kann auf beiden Seiten Produkte eingegeben werden und in beiden Konsolen wird die Änderung angezeigt.

<p align="center">
  <img src="screenshots/test.JPG" alt="Testen der Synchronisation"
       width="650" height="335">
</p>

###  Firebase Hosting

Mit [Firebase Hosting](https://firebase.google.com/docs/hosting/) kann man mobile Web-Apps ganz einfach bereitstellen. Dieser Hoster wurde zusammen mit Firebase 
Realtime Database gewählt, da es sehr einfach aufzusetzen ist (und kostenlos). 

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

Meine Website: 

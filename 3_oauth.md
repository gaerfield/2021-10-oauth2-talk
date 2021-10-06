<!--s-->
## Öffnung von s0ft-fit für Drittanwendungen

<!--v-->
### FitX-App will unsere Daten

* s0ft-fit ist riesig geworden
* europaweit verteilt, über 250 Studios, unzählige Angestellte
* die Tracker-App FitX möchte unsere Daten in ihre App für statistische Auswertungen integrieren
  * Besser: wir erlauben potentiell allen Fitness-Trackern die Nutzung unserer API um mehr Kunden zu gewinnen. <!-- .element: class="fragment" data-fragment-index="1" -->

<!--v-->
### Problemstellung

* Wie erlauben wir Drittanwendungen den Zugriff auf unsere Api?
* Wie beschränken wir den Zugriff der Drittanwendungen nur auf die Daten dessen Nutzer diese auch tatsächlich weiterreichen möchten?

<!--v-->
### OAuth

> Ein Endbenutzer kann mit Hilfe von OAuth einer Anwendung den Zugriff auf seine Daten erlauben, die von einem anderen Dienst bereitgestellt werden, ohne geheime Details seiner Zugangsberechtigung preiszugeben.

Quelle: [wikipedia](https://de.wikipedia.org/w/index.php?title=OAuth&oldid=212703586)

<!--v-->
#### Beispiel Github

* [Gitub - creating an oauth app](https://docs.github.com/en/developers/apps/building-oauth-apps/creating-an-oauth-app)

<!--v-->
#### OAuth Grundbegriffe I

* Resource Owner: Benutzer (der Zugriff auf seine Daten gewährt)
* Resource Server: Dienst auf dem die geschützten Resourcen liegen
* Client: Drittanwendung, die Zugriff auf die Daten möchte
* Authorization Server: der Server, der den _Resource Owner_ authentifiziert und _Access Token_ und _Refresh Token_  ausstellt
* Consent: die explizite Zustimmung des _Resource Owner_ ob sie dem _Client_ die angefragten Scopes gewähren

<!--v-->
#### OAuth Grundbegriffe II

* Access Token: kurzlebiges geheimes Token, mittels dessen Resourcen beim _Resource Server_ im Namen des _Resource Owner_ abgerufen werden können
* Refresh Token: "langlebiges" geheimes Token mittels dem neue _Access Token_ beim _Authorization Server_ abgerufen werden können ohne sich erneut authentifizieren zu müssen
* Scope: feingranulare Zugriffsrechte die durch den _Client_ angefragt werden

<!--v-->
#### Warum Access-Token und Refresh-Token?

* Access-Token werden für den Zugriff auf den Resource-Server verwendet und werden häufig für verschiedenste Anfragen wiederverwendet
* Refresh-Token verbleiben beim Resource Owner und werden ausschließlich für die Ausstellung neuer Access-Token verwendet
* Access-Token sind dadurch potentiell leichter abgreifbar als Refresh-Token, durch die kurze Gültigkeit ist der Schaden aber begrenzt

<!--v-->
#### der Oauth-Flow am Beispiel Github
```puml

Actor "Ich\n(Resource Owner)" as reso
participant "Postman\n(Client)" as client
participant "Github\n(Resource Server)" as ress
participant "Github Auth\n(Authorization Server)" as auth

reso -> client++ : get repositories
group wenn kein access token vorhanden
client -> auth++ : redirect
auth -> reso++ : bitte einloggen
return username+password
auth -> auth : authentifiziere
auth -> reso++ : Zugriff gewähren für scope "repo"?
return OK
return access-token
end
client -> ress++ : GET /users/gaerfield/repos\nRequest-Header: github-token
return repositories
return repositories
```

<!--v-->
### Nächster Schritt

* Kling gut! Wir implementieren die Login-Komponente als OAuth2-Server
  * Äh ... Nein! Auf gar keinen Fall! <!-- .element: class="fragment" data-fragment-index="1" -->

<!--v-->
### Warum nicht selber bauen?

* Kosten einer eigenen Implementation zu hoch
  * gängige Sicherheitsanforderungen berücksichtigen (salted Passworthashes)
  * Schutz gegen Brute-Force und Phising (zweiter Faktor)
  * Skalierbarkeit
  * und das OAuth2 - Protokoll korrekt implementieren: [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749) [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750)<!-- .element: class="fragment" data-fragment-index="1" --> [RFC 7523](https://datatracker.ietf.org/doc/html/rfc7523)<!-- .element: class="fragment" data-fragment-index="2" --> [RFC 7522](https://datatracker.ietf.org/doc/html/rfc7522)<!-- .element: class="fragment" data-fragment-index="3" -->

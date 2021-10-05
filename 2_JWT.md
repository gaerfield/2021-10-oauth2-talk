<!--s-->
## Modularisierung von s0ft-fit

<!--v-->
### Problemstellung

* wir haben expandiert, auf tausende Fitnessstudios
* Login und Historie sind unterschiedlich stark frequentiert und sollen unabhängig skalieren um Ressourcen zu sparen
* Wie überprüft die Historie:
  * zu welchem Nutzer die Anfrage gehört?
  * ob der Nutzer sich korrekt authentifiziert hat?
  * klasssicher Ansatz: sessions <!-- .element: class="fragment" data-fragment-index="1" -->

<!--v-->
#### Login erzeugt Session

```puml
actor user
participant "webclient"
participant "login"
database "session-management" as ses

user -> webclient++ : login bei "s0ftf1t.de"
webclient -> login++ : login
login -> login : validiere
login -> ses++ : loggedIn(User)
ses -> ses : lege session an und speichere User
return sessionId
return sessionId
return willkommen
```

<!--v-->
#### SessionId

Die `sessionId` ist ein inhaltsloses zeitlich eingeschränkt gültiges Token welches das Backend zu einem konkreten Nutzer zugeordnet hat.

<!--v-->
#### Abruf der Historie

```puml
actor user
participant "webclient"
participant "bankdruecken-historie" as his
participant "login"
database "session-management" as ses

user -> webclient++ : Klick auf Historie
webclient -> his++ : getFor(sessionId)
his -> login++ : getUserForSession(sessionId)
login -> ses++ : sessionId
return User
return User
his -> his : getFor(User)
return history
return tabelle
```

<!--v-->
#### Nachteile der SessionId

* Backend fragt erneut immer wieder die Login-Komponente an
  * Auslastung der Login-Komponente vervielfacht sich mit Auslastung Backends
  * Antwortzeiten des Backends erhöhen sich um Antwortzeit der login-Komponente
  * gute Skalierung, schnelle Datenbank und evtl. globales Caching notwendig

<!--v-->
#### stateless Tokens

* Sessions sind ideal für temporäre Datenbestände wie Warenkörbe
* eine `UserId` ändert sich nicht während seiner Zugriffs
* Können wir die `UserId` statt der `sessionId` liefern und sicherstellen, dass diese nicht vom Nutzer verändert wurde?

<!--v-->
### Token

```puml
actor user
participant "webclient"
participant "login"
participant "bankdruecken-historie" as his

user -> webclient++ : login bei "s0ftf1t.de"
webclient -> login++ : login
login -> login : validiere
return User=Achim
return willkommen
...
user -> webclient++ : Klick auf Historie
webclient -> his++ : getFor(user = Achim)
return history
return tabelle
```

<!--v-->
#### JSON Web Token (JWT)

JWT ist ein RFC-Standard ([RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519)) zum Austausch von **verifizierbaren** Informationen.

> JWT's eignen sich [...] zur Implemen-tierung von "stateless sessions", da sämtliche authentifizierungsrelevanten Informationen im Token übertragen werden können und die Sitzung nicht zusätzlich auf einem Server gespeichert werden muss.

Quelle: [wikipedia](https://de.wikipedia.org/w/index.php?title=JSON_Web_Token&oldid=212574521)

<!--v-->
#### JWT

* Aufbau eines JWT-Token:
  * `base64(Header).base64(Payload).signature`
  * Header: unter anderem den Signatur-Algorithmus
  * Payload: sog. "claims", die verifizierbaren Entitäten
  * Signatur: hash des Klartexts aus header und payload entsprechend des im Header angegebenen Algorithmus
  * Beispiel: [jwt.io](https://jwt.io)

<!--v-->
### JWT Ausstellung

```puml
actor user
participant "webclient"
participant "login"

user -> webclient++ : login bei "s0ftf1t.de"
webclient -> login++ : login
login -> login : validiere
login -> login : erzeuge JWT
login -> login : signiere JWT mittels\n**private-key**
return --User-- **JWT**
return willkommen
```

<!--v-->
### JWT Nutzung

```puml
actor user
participant "webclient"
participant "bankdruecken-historie" as his

user -> webclient++ : Klick auf Historie
webclient -> his++ : getFor(--User-- **JWT**)
his -> his : validiere Signatur mittels\n**public-key**
return history
return tabelle
```

<!--v-->
### Fazit

In einem verteilten System **ohne public API** ist eine zentrale Authentifikations-Instanz welche JWT-Tokens ausstellt hinreichend.

```puml
left to right direction

component webclient

cloud "s0ft-fit" {
  component Login
  note right of [Login]
    erstellt und signiert
    Auth-Token (JWT)
  end note
  component "bankdruecken-historie" as his
  note right of [his]
    prüft Signatur
    des Auth-Token

  end note

  webclient --> Login: einloggen
  webclient --> his : Historie abrufen\nmit JWT im Header
  webclient <-- Login : liefert Auth-Token (JWT)

  his -> Login : ruft public key ab
}



```

<!--s-->
## Ziel

Wie kann in einem verteilten System (z.B. im Fall von Microservices) geprüft werden ob ein Nutzer sich erfolgreich authentifiziert hat.

<!--v-->
## Geschichte I

* 2005 OpenID Vorstellung des offenen Single-Sign-On Protokolls
* 2006 Entwicklung von OAuth 1.0 während der Integration von OpenID in Twitter (Delegation der Authentifizierung)
* 2007 Vorstellung von OAuth 1.0
* Juni 2007 Gründung der OpenID Foundation zur Sicherung von Markenrechten

<!--v-->
## Geschichte II

* Dezember 2007 OpenID 2.0
* November 2008 Vorschlag an die IETF zur Standardisierung von OAuth
* 2012 OAuth 2 durch die IETF als RFC 6749 und RFC 6750 veröffentlicht.
* irgendwann OpenID Connect als Schicht oberhalb des OAuth Protokolls


<!--v-->
## Startup s0ft-fit

* 1 Fitnessstudio
* USP: bieten online Training-Tracker

> Gehe Trainieren wann immer du willst!
> Rufe deine Trainings-Historie ab wann immer du willst!

<!--v-->
### Architektur

```puml
node server {
  component "s0ft-fit.de"
}
```

<!--v-->
### Typischer Flow der Authentifizierung
```puml
actor user
participant "webclient"
participant "server"
user -> webclient++ : login bei "s0ft-fit.de"
webclient -> server++ : login
server -> server : validiere
return ok
return willkommen
```

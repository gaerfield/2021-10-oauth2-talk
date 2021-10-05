<!--s-->
## OpenID

<!--v-->
### neue Anforderung

User sollen sich mit ihren bestehenden Google-Konto oder Github-Konto anmelden können (Single Sign On).

<!--v-->
### Lösung 1

Wir integrieren alle provider für jede Komponente im Backend:

```puml
left to right direction
cloud google
cloud github
cloud s0ft-fit {
  component login
  component "bankdruecken-history" as his
  component client

  client --> login : login falls s0ft-fit-account
  client --> github : login falls github-account
  client --> google : login falls google

  client ---> his : GET /history\nHeader enthält\ngoogle-token || github-token || s0ftfit-token
  login <-- his : get public key
  google <-- his : get public key
  github <-- his : get public key

  his -> his : validiere token je nach typ
}
```

<!--v-->
### OpenId Connect

> OAuth 2.0 is designed only for authorization, for granting access to data and features from one application to another. [...] OpenID Connect enables scenarios where one login can be used across multiple applications, also known as single sign-on (SSO)

[An Illustrated Guide to OAuth and OpenID Connect](https://developer.okta.com/blog/2019/10/21/illustrated-guide-to-oauth-and-oidc)

<!--v-->
### OpenID Connect upgrade

* OpenId Connect (OIDC) erweitert OAuth 2.0
  * Endpoints für dynamic discovery:
    * [Google](https://accounts.google.com/.well-known/openid-configuration)
    * [Auth0](https://s0ft-fit.eu.auth0.com/.well-known/openid-configuration)
  * dynamic client registration
  * fügt Token Informationen über eingeloggten Nutzer hinzu

<!--v-->
### Konfiguration von alternativen Anmeldungen

* registrieren von s0ft-fit als Anwendung in google bzw. github für den Abruf von Profilinformationen
* konfigurieren unseren Identity Provider mit Client ID und Client Secret von google bzw. github

```puml
left to right direction
cloud google
cloud github
cloud s0ft-fit {
  component "Identity Provider\n(Auth0)" as login
  component "bankdruecken-history" as his
  component client

  client --> login : redirect to login
  google <-- login  : rufe ID Token ab
  login --> github  : rufe ID Token ab
  login --> login : validiere Token
  client <-- login : liefere s0ft-fit\nAccess Token


  login <-- his : rufe public key ab
  client --> his : rufe history mit\ns0ft-fit Token ab
  his -> his : validiere token je nach typ
}
```

<!--v-->
#### veränderter Login Flow

```puml
Actor "Ich\n(Resource Owner)" as reso
participant "Postman\n(Client)" as client
participant "login\n(Authorization Server)" as auth
participant "github\n(externer Authorization Server)" as gauth
participant "bankdruecken-history\n(Resource Server)" as ress

reso -> client++ : /login
client -> auth++ : /login
auth -> reso++ : bitte einloggen\n**oder social login wählen**
return einloggen mit github
auth -> gauth++ : /authorize
gauth -> reso++ : bitte einloggen
return Authentifizierung und Autorisierung
gauth -> gauth : Validierung
return Access Token + ID Token
auth -> auth : validiere token
auth -> auth : finde und synchronisiere Account
return s0ft-fit Access Token
client -> ress++ : GET /history\nRequest-Header: s0ft-fit token
return history
return history
```

<!--v-->
#### weitere Vorteile

* automatische Synchronisation von Account-Informationen aus dem ID-Token
* verwenden verschiedener Accounts für denselben Account (Account Linking)
  * passwortlose Registrierung mit Mobiltelefon
  * später Verlinkung eines Kontos mit erweiterten Profilinformationen des Google- oder Apple-Kontos

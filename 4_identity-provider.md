<!--s-->
## s0ft-fits Identity-provider

<!--v-->
### Warum nicht selber bauen?

* Risiko und Kosten einer eigenen Implementation zu hoch
  * Passwörter hashen
  * Anwendung skalierbar implementieren
  * Schutz vor Attacken auf Applikationsebene
  * Schutz gegen Brute-Force und Phishing
  * und das OAuth2 - Protokoll korrekt implementieren: [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749) [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750)<!-- .element: class="fragment" data-fragment-index="1" --> [RFC 7523](https://datatracker.ietf.org/doc/html/rfc7523)<!-- .element: class="fragment" data-fragment-index="2" --> [RFC 7522](https://datatracker.ietf.org/doc/html/rfc7522)<!-- .element: class="fragment" data-fragment-index="3" -->

<!--v-->
### Identity Provider

* Authentifikation und Autorisierung sind ein querschnittlicher Belang, entsprechend gibt es fertige Produkte
* on-premise:
  * z.B. Keycloak
  * [zertifizierte OpenId Server](https://openid.net/developers/certified/)
* als Service:
  * [Okta](https://www.okta.com/de/)
  * [Auth0](https://auth0.com/de)

<!--v-->
### login-komponente durch einen Authorization Server ersetzen

```puml
left to right direction

component webclient

cloud "s0ft-fit" {
  component "**Authorization Server**" as Login
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

<!--v-->
### login-komponente durch Identity Provider ersetzen

```puml
left to right direction

component webclient
component "**Authorization Server**" as Login
note right of [Login]
erstellt und signiert
Auth-Token (JWT)
end note

cloud "s0ft-fit" {
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

<!--v-->
### Beispiel Auth0

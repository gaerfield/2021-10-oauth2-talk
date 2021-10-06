<!--s-->
## s0ft-fits Identity-provider

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

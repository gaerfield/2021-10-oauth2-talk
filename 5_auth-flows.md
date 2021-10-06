<!--s-->
## OAuth Flows

<!--v-->
### Authentifikationprozesse

* verschiedene Anwendungsfälle benötigen verschiedene Authentifikationprozesse:
  * Machine-2-Machine Kommunikation ohne Nutzer
  * Webapps die das Backend serverseitig anfragen
  * Applikationen die clientseitig betrieben werden (native oder webapps)

<!--v-->
### Machine-2-Machine Kommunikation ohne Nutzer

* Client ist gleichzeitig auch Resource Owner (fragt eigene Daten ab)
* bspw. CronJobs die keinen Nutzer haben
* keine Nutzer-Authentifizierung möglich
* Client-Id und Client-Secret zur Erlangung eines Access-Tokens
* Client Credentials Flow zur Erlangung M2M-Token

<!--v-->
### Client Credentials Flow

```puml
participant "client" as app
participant "authorization server" as auth
participant "resource server" as backend

app -> auth++ : Anfrage an /token\ngrant_type=client_credentials\nclient_id & client_secret
auth -> auth : validiere Client ID & Client Secret
return Access Token
app -> backend++ : Anfrage mittels Access Token
return geschützte Resource
```

<!--v-->
### auf dem Server ausgeführte web app

* Access Token wird direkt an den Webserver übermittelt auf dem der client betrieben wird
* kein Risiko des ungewollten Verlusts, weil keine Tokens in den Browser übertragen werden

<!--v-->
### Authorization Code Flow

```puml
actor "resource owner" as user
participant "client" as app
participant "authorization server" as auth
participant "resource server" as backend

user -> app++ : login
app -> auth++ : /authorize\nresponse_type=code\nclient_id
auth -> user++ : redirect zu login\nAutorisierung
return Authentifizierung und Autorisierung
return Authorization Code
app -> auth++ : Anfrage an /oauth/token\nAuthorization Code + Client ID + Client Secret
auth -> auth : validiere Authorization Code\n+ Client ID + Client Secret
return Access Token
app -> backend++ : Anfrage mittels Access Token
return geschützte Resource
return geschützte Resource
```

<!--v-->
### Single-Page oder native App

* Client Secrets werden per Client (also per App) ausgestellt
* identisch für alle Nutzer und App-Instanzen
* Native oder WebApps können das Client Secret nicht sicher speichern (Dekompilierung bzw. im Browser enthalten)
* ein Angreifer könnte den `Authorization Code` abfangen (insbesondere durch registrierte custom urls "softfit://") und mittels des ausgelesen secrets Access-Tokens abfragen


<!--v-->
### Authorization Code Flow (PKCE)

* Erweiterung um Proof Key for Code Exchange (PKCE)
* Client:
  * erstellt Code Verifier und Code Challenge
  * sendet Code Challenge um Authorization Code zu erhalten
  * sendet Authorization Code und Code Verifier um Access Token zu erhalten
* Attacken können nur den Authorization Code abfangen der ungenügend zur Ausstellung von Access Tokens ist

<!--v-->
### Authorization Code Flow (PKCE)

```puml
actor "resource owner" as user
participant "client" as app
participant "authorization server" as auth
participant "resource server" as backend

user -> app++ : login
app -> app : **generiere Code Verifier**\n**und Code Challenge**
app -> auth++ : /authorize\nClient ID & **Code Challenge**
auth -> user++ : redirect zum Login und Autorisierung
return Authentifizierung und Autorisierung
return Authorization Code
app -> auth++ : /access_token\nClient ID, Client Secret,\nAuthorization Code & **Code Verifier**
auth -> auth : validiere Code Verifier\nund Code Challenge
return ID Token und Access Token
app -> backend++ : Anfrage mittels Access Token
return geschützte Resource
return geschützte Resource
```

<!--v-->
### falls redirect nicht möglich

* Resource Owner Password Flow
  * User und Passwort werden in der Applikation eingetragen
* ist **ausschließlich** dann zu verwenden:
  * wenn client absolut vertrauenswürdig
  * redirect-basierte Flows nicht funktionieren

<!--v-->
### Resource Owner Password Flow

```puml
actor "resource owner" as user
participant "client" as app
participant "authorization server" as auth
participant "resource server" as backend

user -> app++ : login
app -> auth++ : Anfrage an "/oauth/token"\nAuthentifikation mit\nUser und Passwort
auth -> auth : validiere Username\nund Passwort
return Access Token
app -> backend++ : Anfrage mittels Access Token
return geschützte Resource
return geschützte Resource
```

<!--v-->
## Zusammenfassung

* Client Credentials Flow: für M2M-Szenarien
* Authorization Code Flow (PKCE): für Web und Native Apps

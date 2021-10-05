<!--s-->
## Zusammenfassung

* Java Web Tokens erlaubt in verteilten Systemen den Austausch verifizierbarer Informationen
* OAuth ermöglicht es Nutzern den Zugriff auf die eigenen Daten für Drittanwendungen zu steuern (ohne Weitergabe des Passworts)
* OpenId ermöglicht Single-Sign-On

<!--v-->
## Nachteile

* Logout nur eingeschränkt möglich
  * Refresh Tokens können invalidiert werden
  * Access Tokens behalten ihre Gültigkeit auch nach Logout

<!--v-->
## Fazit I

* niemals selber implementieren

<!--v-->
## Fazit II

* selbst für monolithische Architekturen sinnvoll:
  * vereinfacht die Architektur (nur permissions werden geprüft)
  * verlagert Sicherheitsrisiken zum Identity Provider
  * getrennter Betrieb erhöht Wartbarkeit

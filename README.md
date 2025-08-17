**📅 DateKeeper**

DateKeeper ist eine mobile Kalender-App, entwickelt mit Ionic Vue 3, Capacitor und einer nativen Kalender-Integration.
Ziel: Termine einfach erstellen, ansehen, bearbeiten und löschen – übersichtlich in einer modernen Oberfläche.
________________________________________
**🚀 Features**
________________________________________

**🏠 Startseite (Home)**

•	Begrüßungsseite mit Branding und Logo.
•	Drei Hauptfunktionen über Buttons erreichbar:
o	Neuen Termin hinzufügen
o	Termine ansehen (Liste)
o	Kalenderübersicht (Monatsansicht)
Besonderheit: Die Statusbar ist auf der Home-Seite im hellen Mintgrün (#e8f6f1) eingefärbt.
________________________________________
**✍️ Termin erstellen / bearbeiten**

•	Eingabefelder:
o	Titel (Pflichtfeld)
o	Ort
o	Start- & Enddatum mit integriertem DateTime-Picker (deutsche Lokalisierung)
o	Ganztägig-Umschalter
o	Beschreibung (mehrzeilig, Auto-Grow)
•	Layout:
o	Einheitliche Hintergrundfarbe (Mintgrün)
o	Abgerundete Inputfelder
o	Buttons klar in Mintgrün hervorgehoben
•	Speichern legt den Termin im nativen Kalender (Google / iOS) an.
•	Abbrechen führt zurück zur Event-Liste.
________________________________________
**📖 Termin-Details**

•	Zeigt:
o	Titel, Ort, Datum/Zeit, Beschreibung
•	Buttons:
o	Ort kopieren
o	Termin bearbeiten / als Vorlage verwenden
o	Termin löschen (löscht aus dem nativen Kalender)
________________________________________
**📋 Terminliste**

•	Übersicht aller gespeicherten Termine.
•	Einzelne Termine anklickbar → Detailansicht.
•	Termine lassen sich neu laden (Pull-to-refresh oder Button).
________________________________________
**📆 Kalenderübersicht**

•	Monatsansicht via Vue Cal.
•	Termine werden farbig dargestellt.
•	Klick auf Termin → Detailansicht.
•	Button "Termine neu laden" aktualisiert die Anzeige.
________________________________________
**🔙 Navigation & Banner**

•	BannerHeader-Komponente:
o	Enthält Seiten-Titel.
o	Links: Home-Button (zurück zur Startseite).
o	Daneben: Zurück-Button (zurück zur vorherigen Ansicht).
•	Einheitlich auf allen Seiten außer der Home-Seite.

________________________________________
**⚙️ Technische Besonderheiten**

•	Capacitor Calendar Plugin für native Kalenderintegration (Create, List, Delete).
•	Styling durch Mintgrün (#e8f6f1) als Hauptfarbe und dunkleres Grün (#4D9B85) für Buttons.
•	Responsive Design (getestet auf Android).

________________________________________
**📲 How to use**

1.	App starten → Home-Seite.
2.	Über „Neuen Termin hinzufügen“ neuen Termin erstellen.
3.	In Termine ansehen oder Kalenderübersicht vorhandene Termine aufrufen.
4.	Detailansicht erlaubt Bearbeiten, Löschen, Kopieren, Ort Kopieren und auf Karte Anzeigen 


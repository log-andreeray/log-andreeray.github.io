---
date: '2017-10-03 12:07 +0200'
published: true
title: BackboneJS
---

Backbone.js ger strukturen till webbapplikationer genom att tillhandahålla **modeller** med nyckel-värde par bindningar och anpassade händelsehanteringar, och samlingar med ett rikt API av otaliga funktioner, Vyer med deklarativ händelsehantering och kopplar alla till ditt befintliga API över ett RESTful JSON-gränssnitt.

### Beroenden
* Underscore.js (> = 1.8.3). 
* För RESTful persistens och DOM manipulation med Backbone.View, jQuery (> = 1.11.0) 
* för äldre Internet Explorer support, json2.js

## Getting Started
Med Backbone representerar du dina data som Modeller, dessa kan skapas, förstöras, valideras och sparas på servern. 

När en UI-åtgärd orsakar att en modell av en modell ändras, utlöser modellen en "förändring" -händelse; alla synpunkter som visar modellens tillstånd kan meddelas om ändringen, så att de kan svara i enlighet med det, återförvisa sig med den nya informationen. I en färdig Backbone-app behöver du inte skriva den limkod som tittar på DOM för att hitta ett element med ett visst ID och uppdatera HTML manuellt. När modellen ändras uppdateras visningarna helt enkelt.
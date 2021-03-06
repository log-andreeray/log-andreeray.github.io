---
date: '2017-11-05 19:00 +0100'
published: true
title: JavaScript - AJAX
---
*Denna artikel är en ren översättning och ett försök att förkorta ned original artikeln [Getting Started](https://developer.mozilla.org/en-US/docs/AJAX/Getting_Started) på MDN.*

Denna artikel guidar dig genom AJAX-grunderna och ger dig två enkla praktiska exempel för att komma igång.

## Vad är AJAX

AJAX står för asynkron JavaScript och XML. I ett nötskal är det användningen av XMLHttpRequest-objektet för att kommunicera med servrar. Den kan skicka och ta emot information i olika format, inklusive JSON, XML, HTML och textfiler. AJAX mest tilltalande egenskap är dess "asynkrona" natur, vilket innebär att den kan kommunicera med servern, byta data och uppdatera sidan utan att behöva uppdatera sidan.

De två huvuddragen i AJAX:

* Gör förfrågningar till servern utan att ladda om sidan
* Ta emot och arbeta med data från servern

## Steg 1 - Hur man gör en HTTP-begäran

För att kunna göra en HTTP-begäran till servern med JavaScript behöver du en förekomst av ett objekt med nödvändig funktionalitet. Det här är där XMLHttpRequest kommer in. Dess föregångare har sitt ursprung i Internet Explorer som ett ActiveX-objekt som heter XMLHTTP. Sedan följde Mozilla, Safari och andra webbläsare, genom att implementera ett XMLHttpRequest-objekt som stödde metoderna och egenskaperna hos Microsofts ursprungliga ActiveX-objekt. Under tiden implementerade Microsoft XMLHttpRequest också.

Efter en förfrågan får du ett svar tillbaka. Vid detta tillfälle måste du berätta för XMLHttp-förfrågan, vilken JavaScript-funktion som hanterar svaret, genom att ställa in objektets onreadystatechange-egenskap och namnge det efter att funktionen ska åberopas när förfrågan ändras, så här:

```js
httpRequest.onreadystatechange = nameOfTheFunction;
```

Observera att det inte finns några parenteser eller parametrar efter funktionsnamnet, eftersom du tilldelar en referens till funktionen istället för att faktiskt åberopa den. Alternativt kan du istället för att ge ett funktionsnamn använda JavaScript-tekniken för att definiera funktioner i på en gång (kallade "anonyma funktioner") för att definiera de åtgärder som ska hantera svaret, gör så här:

```js
httpRequest.onreadystatechange = function(){
    // Process the server response here.
};
```

Efter att ha förklarat vad som händer när du får svaret måste du faktiskt göra begäran genom att anropa metoderna för `open()` och `send()` för HTTP-förfrågan, så här:

```js
httpRequest.open('GET', 'http://www.example.org/some.file', true);
httpRequest.send();
```

* Den första parametern för anropet open() är HTTP-förfrågan - GET, POST, HEAD, eller en annan metod som stöds av din server. Håll metoden all-capitals enligt HTTP-standarden, annars kan vissa webbläsare (som Firefox) inte behandla förfrågan. För mer information om möjliga HTTP-förfrågningsmetoder, kolla [W3C-specifikationerna](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

* Den andra parametern är den webbadress du skickar begäran till. Som säkerhetsfunktion kan du inte åberopa URL-adresser på tredje partens domäner som standard. Var noga med att använda det exakta domännamnet på alla dina sidor, eller så du kommer att få ett "behörighetsförnekat" fel när du anropar call(). Ett vanligt fel är att tillgå din webbplats via domain.tld, men istället örsöker anropar dem med www.domain.tld. Om du verkligen behöver skicka en begäran till en annan domän, se [HTTP access control](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

* Den valfria tredje parametern anger om begäran är asynkron. Om true (standard), fortsätter JavaScript-körningen och användaren kan interagera med sidan medan serverns svar ännu inte har kommit fram. Detta är det första A i AJAX.

Parametern för metoden send() kan vara vilken data som helst som du vill skicka till servern om POST begäran. Formdata ska skickas i ett format som servern kan analysera, som en frågesträng:

```js
"name=value&anothername="+encodeURIComponent(myVar)+"&so=on"
```

eller andra format, som multipart/form-data, JSON, XML, och så vidare.

**Observera att om du vill använa `POST` data måste du kanske ställa in begärans MIME-typen. Till exempel, använd följande innan du anropar `send()` för formulärdata som skickas som en frågesträng:**

```js
httpRequest.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
```

## Steg 2 - Hantera serverns svar

När du skickadar förfrågningar, anger du namnet på en JavaScript-funktion för att hantera svaret:

```js
httpRequest.onreadystatechange = nameOfTheFunction;
```

Vad ska den här funktionen göra? Först måste funktionen kontrollera förfrågans tillstånd. Om state har värdet av XMLHttpRequest.DONE (motsvarande 4) betyder det att hela serverresponsen mottogs och det är OK för dig att fortsätta bearbeta det.

```js
if (httpRequest.readyState === XMLHttpRequest.DONE) {
    // Everything is good, the response was received.
} else {
    // Not ready yet.
}
```

Den fullständiga listan över readyState-värdena dokumenteras på [XMLHTTPRequest.readyState](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest#Properties) och är som följer:

* 0 (uninitialized) eller (förfrågan inte initialiserad)
* 1 (laddning) eller (serverns anslutning etablerad)
* 2 (laddad) eller (begäran mottagen)
* 3 (interaktiv) eller (bearbetningsförfrågan)
* 4 (komplett) eller (förfrågan är klar och svaret är klart)

Kontrollera sedan [svarskoden](https://developer.mozilla.org/en-US/docs/Web/HTTP#HTTP_Response_Codes) för HTTP-svaret. De möjliga koderna är listade på [W3C](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). I det följande exemplet skiljer vi mellan ett framgångsrikt och misslyckat AJAX-samtal genom att söka efter en `200 OK` svarskod.

```js
if (httpRequest.status === 200) {
    // Perfect!
} else {
    // There was a problem with the request.
    // For example, the response may have a 404 (Not Found)
    // or 500 (Internal Server Error) response code.
}
```
Efter att du har kontrollerat tillståndet för begäran och HTTP-statuskoden för svaret kan du göra vad du vill med de data som servern skickade. Du har två alternativ för att få tillgång till data:

* **httpRequest.responseText** - returnerar serverns svar som en textsträng.
* **httpRequest.responseXML** - returnerar svaret som ett `XMLDocument` objekt som du kan travasera med JavaScript DOM-funktioner.

*Observera att stegen ovan är giltigt endast om du använde en asynkron förfrågan (den tredje parametern för open() var ospecificerad eller satt till `true`). Om du använde en synkron förfrågan behöver du inte ange en funktion, men det är ej att rekommender eftersom det ger en hemsk användarupplevelse.*

## Steg 3 - Ett enkelt exempel

Låt oss sammanföra det med en enkel HTTP-begäran. Vår JavaScript kommer att begära ett HTML-dokument, `test.html`, som innehåller texten "Jag är ett test." Sedan kommer vi att alert() innehållet i svaret. Observera att det här exemplet använder vanilla JavaScript - ingen jQuery är inblandad. HTML, XML och PHP-filerna ska också placeras i samma katalog.

```js
<button id="ajaxButton" type="button">Make a request</button>

<script>
(function() {
  var httpRequest;
  document.getElementById("ajaxButton").addEventListener('click', makeRequest);

  function makeRequest() {
    httpRequest = new XMLHttpRequest();

    if (!httpRequest) {
      alert('Giving up :( Cannot create an XMLHTTP instance');
      return false;
    }
    httpRequest.onreadystatechange = alertContents;
    httpRequest.open('GET', 'test.html');
    httpRequest.send();
  }

  function alertContents() {
    if (httpRequest.readyState === XMLHttpRequest.DONE) {
      if (httpRequest.status === 200) {
        alert(httpRequest.responseText);
      } else {
        alert('There was a problem with the request.');
      }
    }
  }
})();
</script>
```

I det här exemplet:

* Användaren klickar på knappen "Make a request"
* Eventhanteraren kallar funktionen `makeRequest()`
* Förfrågan görs och sedan (onreadystatechange) vidarebefodras exekveringen till `alertContents()`
* `alertContents()` kontrollerar om svaret mottogs och OK, varnar sedan med `alert()` visas innehållet i test.html-filen.

**Anm. 1 Om du skickar en begäran till ett stycke kod som returnerar XML, istället för en statisk XML-fil, måste du ange svarhuvuden för att fungera i Internet Explorer. Om du inte anger header Content-Type: application/xml, kommer IE att kasta ett JavaScript "Object Expected" efter linjen där du försökte komma åt ett XML-element.**

**Anm. 2: Om du inte anger rubrik Cache-Control: no-cache kommer webbläsaren cache svaret och skickar aldrig in begäran när den väl en gång är gjord, vilket gör felsökning utmanande. Du kan också lägga till en unik GET-parameter, som en tidsstämpel eller ett slumpmässigt nummer (se [bypassing the cache](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest#Bypassing_the_cache))**

**Not 3: Om httpRequest-variabeln används globalt kan konkurrerande funktioner som kallar makeRequest() skriva över varandra, vilket orsakar ett tävlingsförhållande. Deklarera httpRequest-variabeln lokal i en "[closure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)" som innehåller funktionerna undviker detta.**

I händelse av ett kommunikationsfel (som servern går ner), kommer ett undantag att kastas i onreadystatechange metoden vid åtkomst av svarstatusen. För att mildra detta problem kan du pakka om din if...then uttalandet i ett try...catch:

```js
function alertContents() {
  try {
    if (httpRequest.readyState === XMLHttpRequest.DONE) {
      if (httpRequest.status === 200) {
        alert(httpRequest.responseText);
      } else {
        alert('There was a problem with the request.');
      }
    }
  }
  catch( e ) {
    alert('Caught Exception: ' + e.description);
  }
}
```









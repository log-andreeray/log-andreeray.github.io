---
date: '2017-11-02 07:34 +0100'
published: true
title: JavaScript - this
---
*Denna artikel är en förkortning och försök till förenkling av orginal artikeln [Understand JavaScript’s “this” With Clarity, and Master It](http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/)*

`this` nykelordet i JavaScript förvirrar nya och erfarna JavaScript-utvecklare. Denna artikel syftar till att belysa detta i sin helhet. Vi kommer att förstå hur man använder `this` korrekt i alla scenarier, inklusive de tjusiga situationer där det vanligtvis visar sig vara mest oroväckande.

Vi använder `this` här på samma sätt som vi använder pronomen på naturliga språk som engelska och franska. Vi skriver, "John löper fort för att "han" försöker fånga tåget."

Notera användningen av pronomen "han". Vi kunde ha skrivit det här: "John springer snabbt, eftersom John försöker fånga tåget." På ett liknande graciöst sätt, i JavaScript använder vi `this` nyckelordet som en genväg, en referent; det hänvisar till ett föremål; det vill säga ämnet i sammanhang eller föremålet för exekveringskoden. Tänk på detta exempel:

```js
var person = {
    firstName: "Penelope",
    lastName: "Barrymore",
    fullName: function() { // observera att vi använder "this" precis som vi använde "han" i tidigare
        console.log(this.firstName + " " + this.lastName); // Vi kunde också ha skrivit this
        console.log(person.firstName + " " + person.lastName);
    }
}
```

Om vi använder person.firstName och person.lastName, som i det sista exemplet blir vår kod tvetydig.

Tänk på att det kan finnas en annan global variabel (som vi kanske eller kanske inte är medveten om) med namnet "person". Därefter kan referenser till person.firstName försöka komma åt egenskapen firstName från den globala variabelen, vilket kan leda till till svåra att felsöka fel.

Så vi använder `this` nyckelordet inte bara för estetik (det vill säga som referent), men också för precision; dess användning gör faktiskt vår kod mer entydig, 

Precis som pronomen "han" används för att hänvisa till antecedenten (antecedent är det substantiv som en pronomen refererar till), används `this` nyckelordet också på ett objekt som funktionen (där den här används) är bunden till.

## JavaScript this nyckelordets grunder

Först, vet att alla funktioner i JavaScript har egenskaper, precis som objekt har egenskaper.

Och när en funktion körs får den `this` egenskapen - en variabel med **värdet på objektet som anropade funktionen där `this` används**.

`this` referensen hänvisar ALLTID till (och håller värdet av) ett objekt, ett singulärt objekt, och det brukar användas inom en funktion eller metod, även om det kan användas utanför en funktion i det globala scopet.

**Observera att när vi använder strikt läge, `this` håller värdet av 'undefined' i globala funktioner och i anonyma funktioner som inte är bundna till något objekt.**

`this` används inom en funktion (låt oss säga funktion A) och den innehåller värdet på objektet som åberopade funktion A. Vi behöver 'this' för att komma åt metoder och egenskaper på objektet som åberopade funktion A, särskilt eftersom vi inte alltid vet namnet på det uppkallande objektet, och ibland finns inget namn för att hänvisa till det åberopade objektet.

`this` är egentligen bara en genvägsreferens för "föregångsobjektet" - det uppkallande objektet.

Grubbla på detta grundläggande exempel som illustrerar användningen av `this` i JavaScript:

```js
var person = {
    firstName: 'Andree',
    lastName: 'Ray',
    //Eftersom 'this' sökordet används i metoden ShowFullName nedan och metoden
    // ShowFullName definieras på personobjektet,  'this' kommer att ha värdet
    // på personobjektet eftersom personobjektet anropar showFullName()
    showFullName: function () {
        console.log (this.firstName + " " + this.lastName);
    }
}

person.showFullName(); // => Andree Ray
```

Och överväga det här grundläggande jQuery-exemplet:

```js
$("button").click(function(event) {
    // $(this) kommer att ha värdet på knappen ($("button")) objektet.
    // eftersom knappobjektet anropar metoden click()
    console.log($(this).prop("name"));
});
```

Jag ska redogöra för det föregående jQuery-exemplet: Användandet av `$(this)`, vilket är jQuerys syntax för `this` nyckelord i JavaScript, används inom en anonym funktion, och den anonyma funktionen exekveras i knappens click() metod. Anledningen till att `$(this)` är knutet till knappobjektet beror på att jQuery-biblioteket binder `$(this)` till objektet som anropar klickmetoden. Därför kommer `$(this)` att ha värdet på jQuery-knappen `($("button"))` objektet, även om `$(this)` definieras inuti en anonym funktion som inte själv kan komma åt `this` variabeln på den yttre funktionen.

*Observera att knappen är ett DOM-element på HTML-sidan, och det är också ett objekt; i det här fallet är det ett jQuery-objekt eftersom vi förpackade det i funktionen jQuery $().*

## Den största Gotcha med JavaScript "this" 

Om du förstår den här principen med JavaScript's `this`, kommer du att förstå `this` nyckelordet med tydlighet:

**`this` tilldelas inte ett värde förrän ett objekt åberopar funktionen där `this` definieras.**

Låt oss kalla funktionen där det här definieras "this funktionen".

Även om det verkar som `this` hänvisar till objektet där det definieras, är inte förrän ett objekt åberopar "this funktionen" som detta faktiskt tilldelas ett värde och värdet det tilldelas baseras uteslutande på objektet som anroper funktionen. 

*`this` har värdet av det uppkallande objektet under de flesta omständigheter. Det finns emellertid några scenarier `this` inte har värdet på det uppkallande objektet. Jag berör senare scenarierna.*

## "this" i det globala scopet

I den globala scopet, när koden körs i webbläsaren, definieras alla globala variabler och funktioner på window objektet. Därför, när vi använder `this` I en global funktion hänvisar den till (och har värdet av) det globala windows objektet. (inte i `strict mode` som tidigare noterat) som är huvud-container för hela JavaScript-applikationen eller webbsidan. 

```js
var firstName = "Andree"
var lastName = "Ray"

function showfullname() {
    // "this" inuti denna funktion kommer att ha värdet av window objektet
    // eftersom funktionen showFullName() definieras i det globala scopet, precis som firstName och lastName
     console.log (this.firstName + " " + this.lastName)
}

var person = {
    firstName: "Ilona",
    lastName: "Ray",
    showfullname: function () {
        // "this" inuti denna funktion kommer att ha värdet av person objektet
        // eftersom funktionen showFullName() anropas i person objektet
         console.log (this.firstName + " " + this.lastName)
    }
}

showfullname() // => Andree Ray
window.showfullname() // => Andree Ray
person.showfullname() // => Ilona Ray
```

## När 'this' är mest missförstått och blir knepigt

* `this` nyckelordet är mest missförstått när vi lånar en metod som använder det `this` 
* när vi tilldelar en metod som använder `this` till en variabel
* när en funktion som använder `this` skickas som en callback funktion
* när `this` används inom en closure - en inre funktion


### Lite om kontext innan vi fortsätter

Kontextet i JavaScript liknar ämnet för en mening på engelska: "John är vinnaren som återvände pengarna." Ämnet i meningen är John, och vi kan säga att kontexten med meningen är John eftersom fokuseringen av meningen är på honom vid denna tidpunkt i meningen och precis som vi kan använda en semikolon för att ändra ämnet för meningen, kan vi ha ett objekt som är aktuellt sammanhang och byta sammanhanget till ett annat objekt genom att åberopa funktionen med ett annat objekt.

```js
var p1 = {
    fname: "Ilona",
    lname: "Ray",
    showfullName: function () {
        // "this" inuti denna funktion kommer att ha värdet av person objektet
        // eftersom funktionen showFullName() anropas i person objektet
         console.log (this.fname + " " + this.lname)
    }
}

// "Kontext", när man anropar showFullName, är personobjektet, när vi
// anropar metoden showFullName () på personobjektet.
// Användningen av "this" i metoden showFullName() har värdet av personobjektet,
p1.showfullName() // => Ilona Ray

var p2 = {
    firstName: 'Andree',
    lastName: 'Ray'
}

// Vi kan använda apply metoden för att ange "this" värdet uttryckligt.
// "this" får värdet av vilket objekt som åberopar "this" funktionen, följaktligen:

p1.showFullName.apply(p2)

// Så kontextet nu anotherPerson eftersom anotherPerson påkallade
// person.showFullName() metoden genom att använda metoden apply()
```

Takeaway är att objektet som åberopar "this funktionen" är i kontext, och vi kan ändra sammanhanget genom att åberopa "this functionen" med ett annat objekt; då är det här nya objektet i kontext.

Här är scenarier när "this" nyckelordet blir komplicerat. Exemplen nedan inkluderar lösningar för att åtgärda fel med "this":

### "this" när det används med en callback

Sakerna blir håriga när vi skickar en metod (som använder "this") som en parameter som ska användas som callback function. Till exempel:

```js
var user = {
    data: [{
        name: 'a.ray',
        age: 36
    },{
        name: 'i.i.ray',
        age: 40
    }],

    clickHandler: function(e) {
        var randomNumber = ((Math.random() * 2|0) + 1) -1 // slumptal mellan 0 och 1
        // Den här raden skriver ut en slumpmässig persons namn och ålder från datarrayen
        console.log(this.data[randomNumber].name + " " + this.data[randomNumber].age)
    }
}

// user.clickhandler skickas som en callback och 'this' context blir nu knappen
// Och utsignalen blir odefinierad eftersom det inte finns någon data-attribut på knappobjektet
document.getElementById('btn').addEventListener('click',user.clickHandler)
```

I koden ovan, eftersom knappen `($("btn"))` är ett objekt på egen hand, och vi skickar `user.clickHandler` metoden till dess `click()` metod som en callback, vet vi att `this` inom vår `user.clickHandler` metoden inte längre att referera till user objektet. `this` kommer nu att referera till objektet där `user.clickHandler` metoden exekveras, eftersom `this` definieras i `user.clickHandler` metoden. Och objektet som påkallar `user.clickHandler` är knappen objektet. `user.clickHandler` kommer att köras inuti knappobjektets `click` metod.

Observera att även om vi anropar metoden `clickHandler()` med `user.clickHandler` (som vi måste göra, eftersom `clickHandler` är en metod som definieras på användaren), kommer metoden `clickHandler()` att exekveras med knappobjektet som kontext där `this` nu hänvisas till. Så `this` hänvisar nu till är knappen `($("btn"))` objektet.

När kontexten ändras, när vi utför en metod på något annat objekt än var objektet ursprungligen definierades, refererar `this` nyckelordet inte längre till det ursprungliga objektet där `this` ursprungligen definierades, utan hänvisar nu till det föremål som åberopar metoden där `this` definierades.

Eftersom vi verkligen vill att `this.data` ska hänvisa till dataegenskapen på user objektet, kan vi använda metoden `Bind()`, `Apply()` eller `Call()` för att specifikt ange värdet av `this`.

För att åtgärda detta problem i föregående exempel kan vi använda `bind()` metoden:

```js
document.getElementById('btn').addEventListener('click',user.clickHandler.bind(user))
```

### "this" i en closure

En annan instans när `this` är missförstått är när vi använder en inre metod (en closure).

Det är viktigt att notera att stängningar inte kan nå den yttre funktionens variabel genom att använda "this" nyckelord eftersom denna variabel endast är tillgänglig för funktionen själv, inte av inre funktioner. Till exempel:

```js
var user = {
    title: 'title',
    data: [{
        name: 'a.ray',
        age: 36
    },{
        name: 'i.i.ray',
        age: 40
    }],

    // användningen av this.data här är okey, eftersom "this" avser user
    // objektet och data är en egenskap på user objektet.
    clickHandler: function(e) {          
        this.data.forEach(function (person) {
            // Men här inne i den anonyma funktionen (som vi överför till metoden forEach)
            // hänvisar "this" inte längre till user objektet.
            // Denna inre funktion kan inte nå den yttre funktionens "this"
            console.log ("What is This referring to? ", this); //[object Window]
            console.log (person.name + " is playing")
        })
    }
}

document.getElementById('btn').addEventListener('click',user.clickHandler.bind(user))
```

"this" inom den anonyma funktionen kan inte nå den yttre funktionen, så den är bunden till det globala fönsterobjektet när strikt läge inte används.

För att lösa problemet med att använda "this" inom den anonyma funktionen som passerat till metoden förEach använder vi en vanlig praxis i JavaScript och ställer in "this" värdet till en annan variabel innan vi går in i forEachmetod:

```js
...
var self = this;
this.data.forEach(function (person) {
    console.log (person.name + " has the title " + self.title)
})
```

**Det är praxis att använda `self` eller `that` när man tilldelar `this` en variabel för att använda inom en inre funktion, men, med lite eftertanke så håller jag med original författaren (något jag uteslöt först). Det bästa är nog att använda sig av en namn konvention där du istället för att använda `self` eller `that`, använder ett namn som mer anger det faktiska objekt som "this funktionen" är en del av. I exemplet ovan kunde man ha ett namn som "theUserObject".**

### "this" när metod tilldelad en variabel

`this` värdet undviker vår fantasi och är bunden till ett annat objekt om vi tilldelar en metod som använder `this` till en variabel.

```js
// data i det globala scopet (window)
var data = [{
    name: 'andree',
    age: 36
},{
    name: 'ilone',
    age: 40
}]

// data i ett user object
var user = {

    data: [{
        name: 't.woods',
        age: 37
    },{
        name: 'p.Mickelson',
        age: 43
    }],

    showData: function(event) {
        var randomNumber = ((Math.random() * 2|0) +1) -1 // => random 0 - 1
        console.log(this.data[randomNumber].name + " " + this.data[randomNumber].age)
    }
}

// Tildela user.showData till en variabel
var showUserData = user.showData

showUserData()
```


Vi kan lösa detta problem genom att specifikt ställa in `this` värdet med `bind()` metoden:

```js
// Tildela user.showData till en variabel
var showUserData = user.showData.bind(user)
```

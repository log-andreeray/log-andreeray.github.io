---
date: '2017-10-30 14:57 +0100'
published: true
title: JavaScript - Function.prototype.apply()
---
*Denna artikel är en svensk översättning av [Function.prototype.apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) på MDN.*

Metoden `apply()` kallar en funktion med ett givet värde och argument som tillhandahålls som en array (eller ett array-liknande objekt).

**Obs! Medan syntaxen för denna funktion är nästan identisk med den för `call()`, är den grundläggande skillnaden att `call()` accepterar en argumentlista, medan `apply()` accepterar en enda grupp av argument.**

### Syntax

```js
func.apply(thisArg, [argsArray])
```

### Parametrar

`thisArg` - Värdet av this anges som anrop till func. Observera att detta kanske inte är det verkliga värdet som ses av metoden. Om metoden är en funktion i none-strict läget, kommer null och undefined att ersättas med det globala objektet och primitiva värden kommer att boxas.

`argsArray` - Ett array-liknande objekt, med angivande av argumenten som func ska kallas, eller null eller undefined om inga argument ska tillhandahållas till funktionen. Från och med ECMAScript 5 kan dessa argument vara ett generiskt array-liknande objekt istället för en array. Se nedan för information om webbläsarkompatibilitet.

### Retur värde

Resultatet av att anropa funktionen med det angivna this värde och argument.

## Beskrivning

Du kan tilldela ett annat `this` objekt när du anropar en befintlig funktion. `this` hänvisar till det aktuella objektet, det anropande objektet. Med `apply`, kan du skriva en metod en gång och sedan ärva den i ett annat objekt, utan att behöva skriva om metoden för det nya objektet.

`apply` är mycket likt `call()`, förutom typen av argument som den stöder. Du använder en argumentmatris istället för en lista med argument (parametrar). Med apply kan du också använda en array literal, till exempel `func.apply(this, ['eat', 'bananas'])` eller ett Array-objekt, till exempel `func.apply(this, new Array('eat', 'bananas))`.

Du kan också använda `arguments` för argsArray-parametern. `arguments` är en lokal variabel för en funktion. Den kan användas för alla ospecificerade argument för det åberopade objektet. Således behöver du inte veta argumenten för det åberopade objektet när du använder `apply` metoden. Du kan använda `arguments` för att överföra alla argument till det åberopade objektet. Det åberopade objektet ansvarar då för att hantera argumenten.

Sedan ECMAScript 5:e upplagan kan du också använda alla typer av objekt som är arrayliknande, så i praktiken betyder det att den kommer att ha egenskapen length och heltal egenskaper i intervallet (0 ... length-1). Som exempel kan du nu använda en [NodeList](https://developer.mozilla.org/en-US/docs/Web/API/NodeList) eller ett anpassat objekt som {'length': 2, '0': 'eat', '1': 'bananas'}.

**De flesta webbläsare, inklusive Chrome 14 och Internet Explorer 9, accepterar fortfarande inte arrayliknande objekt och kommer att kasta ett exception.**

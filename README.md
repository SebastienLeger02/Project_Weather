# Weather App (Fetch)

En aquest repositori faràs servir _fetch_ per obtenir dades de forma asíncrona des d'una API.

## Objectius

- Fer servir Fetch per interactuar amb una API.
- Fer servir l'_state_ de Vue per gestionar dades asíncrones.

## Configuració

- Executa `npm install` per instal·lar els paquets (packages).
- Executa `npm run dev` per executar el servidor de desenvolupament.

## Requisits bàsics

### 1. Crea un formulari.

- Aquest formulari hauria de tenir un input per "location" i un botó de "submit".
  - Quan fem click a "submit" hem de cridar el mètode "getWeather" del teu component.
  - Quan fem click a "submit" també hem d'actualitzar l'_state_ "loading" a true.

### 2. Cridar a la Open Weather Map API.

- Crida la Open Weather API des de la funció "getWeather".
  - Necessitaràs crear un compte a [Open Weather Map API](https://openweathermap.org/). Necessitaràs la teva clau API (API key) per fer les peticions (requests).
  - Fes servir "fetch" dins de la funció "getWeather" i crida a la Open Weather API per obtenir la informació del clima basada en la localització (_location_).
  - Emmagatzema les dades del clima que has obtingut a l'_state_.

### 3. Mostra a l'usuari informació útil (fes-ho atractiu visualment!)

- Crea un element visual per quan les dades estan carregant (_loading_).
- Mostra les dades del clima un cop s'hagin carregat.
- Mostra un error si les dades no s'han carregat correctament.

### 4. Opcional: Afegeix un botó addicional per canviar a la vista de la previsió del temps de 5 dies.

- Per fer això, necessitaràs crear un segon botó que faci una crida a la API una mica diferent.
  - Pots fer servir la mateixa funció; simplement cal alterar una mica la URL que estàs fent servir amb _fetch_ per obtenir les dades a 5 dies vista.

## Recursos

- [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)
- [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [Open Weather Map API](https://openweathermap.org/)
- [Vue Documentation](https://vuejs.org/guide/introduction.html)

## Notes

_Aquest és un projecte d'estudiant creat a [CodeOp](http://CodeOp.tech), al bootcamp de Front End Development a Barcelona._

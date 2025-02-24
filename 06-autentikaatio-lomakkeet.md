# Viikon 6 Fokus - JWT Autentikaatio, Formit ja sovelluksen julkaisu

## Autentikaatio sekä Tokenit

JWT (JSON Web Token) autentikaatio on menetelmä, joka mahdollistaa turvallisen ja tehokkaan käyttäjän tunnistamisen ja valtuuttamisen web-sovelluksissa ja REST-rajapinnoissa. Se perustuu tokenien käyttöön, jotka ovat pieniä datayksiköitä, jotka sisältävät tietoja käyttäjän tunnistamiseksi ja valtuuttamiseksi. JWT-avainta käytetään usein lähettämään käyttäjän tunnistetietoja, kuten käyttäjätunnus ja rooli, ja se voi myös sisältää muita metatietoja. JWT:t ovat turvallisia, koska ne ovat allekirjoitettuja, mikä tarkoittaa, että niiden alkuperä voidaan varmistaa ja tietoja ei voi muuttaa ilman avainta. REST-rajapinnoissa JWT-tokenit usein välitetään HTTP-otsakkeiden kautta pyyntöjen autentikoimiseksi ja käyttöoikeuksien varmistamiseksi.

JWT-tokenit voidaan lähettää HTTP-pyynnön otsakkeissa. Yleisimmin käytetty otsake on Authorization, joka sisältää JWT-tokenin. Esimerkiksi:

```js
Authorization: Bearer <JWT-tokeni>
```

JWT-tokenin vastaanottaminen tapahtuu vastaavasti. Sovellus tarkistaa saapuvan pyynnön otsakkeista, evästeistä tai pyyntöparametreista JWT-tokenin, joka sisältää tarvittavat käyttäjän tunnistetiedot ja käyttöoikeudet. Sen jälkeen sovellus voi tarkistaa tokenin aitouden ja käyttöoikeudet sen allekirjoituksen avulla. Jos tokeni on validi, sovellus voi antaa käyttäjälle pääsyn pyydettyyn toiminnallisuuteen tai resursseihin.

1. **Käyttäjä kirjautuu sisään:**
   Käyttäjä syöttää käyttäjätunnuksen ja salasanan login-formiin ja lähettää ne backend-palvelimelle.

2. **Backend validoi tunnukset:**
   Palvelin tarkistaa tunnukset ja jos ne ovat oikeat, se generoi JWT-tokenin, joka sisältää allekirjoitetun datan, kuten käyttäjän ID:n ja roolit.

3. **Token palautetaan:**
   Palvelin palauttaa JWT-tokenin frontendiin (yleensä JSON-muodossa).

4. **Token tallennetaan selaimeen:**
   Frontend tallentaa tokenin selaimen muistiin.

   [Logal Storage](https://www.w3schools.com/jsref/prop_win_localstorage.asp)

```js
const response = await fetchData(url, options);
localStorage.setItem('token', response.token);
```

5. **Autentikoidut pyynnöt:**
   Jokaisessa suojatussa API-kutsussa frontend lisää tokenin Authorization-otsikkoon. Palvelin tarkistaa saapuvan tokenin allekirjoituksen ja voimassaolon. Jos token on kelvollinen, pyyntö hyväksytään.

```http
# Get all users (requires token)
  GET http://127.0.0.1:3000/api/users
```

Edellisten viikkojen users.js koodiin verrattuna, haetaan kutsun yhteydessä nyt selaimen localstorageen tallennettu tokeni. Tämä tokeni lisätään kutsun otsikkoon.

Koodiesimerkki:

```js
const url = 'http://localhost:3000/api/users';

let headers = {};
let token = localStorage.getItem('token');

if (token) {
	headers = {
		Authorization: `Bearer ${localStorage.token}`,
	};
}
const options = {
	headers: headers,
};

const users = await fetchData(url, options);
```

6. **Uloskirjautuminen:**
   Frontend poistaa tokenin selaimesta.

```js
localStorage.removeItem('token');
```

### Tokenien tallentaminen

Paras tapa tallentaa JWT (JSON Web Token) -autentikointitunniste frontendissä riippuu tarkoituksistasi ja turvallisuusnäkökohdista. Tässä muutamia yleisiä menetelmiä:

- Selaimen evästeet

- Paikallinen tallennustila (Local Storage) tai sessiotallennustila (Session Storage). Käytä localStoragea pysyvään tallennukseen yli selainistuntojen ja sessionStoragea istunto-kohtaiseen tallennukseen.

- Muistivarasto: Tallenna JWT-tunniste muistiin käyttämällä JavaScript-muuttujia. Vaikka tämä menetelmä voi olla turvallinen, se vaatii huolellista hallintaa varmistaaksesi, ettei tunniste vuoda XSS-hyökkäysten kautta.

- IndexedDB: Tallenna JWT-tunniste selainmen omaan tietokantaan.

Jokaisella menetelmällä on omat etunsa ja haittansa turvallisuuden, toteutuksen helppouden ja yhteensopivuuden suhteen eri käyttötapausten kanssa. Harkitse sovelluksesi vaatimuksia ja turvallisuustarpeita valitessasi sopivan tavan tallentaa JWT-tunnisteet frontendissä. Kurssilla käytämme LocalStoragea.

## Viikon harjoitustehtävä - Kirjaantuneen käyttäjän apikutsut ja niiden käsittely

Hae koko projektin pohja TAI pelkästään authtest.html jolla harjoittelemme sisäänkirjautumista sekä tokenien tallentamista täältä:

1. [Koko vite projekti](https://github.com/UllaSe/vite-project)
1. [Pelkkä authtest.html](https://github.com/UllaSe/vite-project/blob/main/src/pages/authtest.html)

## HTML5 lomakkeet ja validointi

HTML5-lomakkeet ovat osa HTML5-standardia, ja ne tarjoavat kehittyneitä tapoja luoda ja hallita interaktiivisia lomakkeita verkkosivuilla. Ne mahdollistavat käyttäjien tietojen syöttämisen ja lähettämisen palvelimelle sekä tarjoavat käytännöllisiä ominaisuuksia, kuten tietojen validointi ja selainpuolella tapahtuva tarkistus.

HTML5-lomakkeet sisältävät erilaisia lomake-elementtejä, kuten tekstikenttiä, salasanakenttiä, valintanappeja, pudotusvalikoita ja muita. Niiden avulla käyttäjät voivat syöttää erilaisia tietoja, kuten tekstiä, numeroita, päivämääriä, sähköpostiosoitteita ja muita tietotyyppejä.

[Forms](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form)

- **Tietojen validointi:** HTML5-lomakkeet tarjoavat mahdollisuuden asettaa validointiehtoja suoraan lomakekenttiin. Esimerkiksi voit määrittää, että sähköpostiosoitteen on oltava oikean muotoinen tai että tiettyjä kenttiä ei saa jättää tyhjiksi.

[Form validation](https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation)

- **Tyylikäs käyttöliittymä:** HTML5-lomakkeet voivat sisältää uusia käyttöliittymäelementtejä, kuten kalenterin, värivalitsimen ja aikavalitsimen. Tämä tekee lomakkeista visuaalisesti houkuttelevampia ja käyttäjäystävällisempiä.

- **Tallentaminen selaimessa:** HTML5-lomakkeet voivat tallentaa osan käyttäjän syöttämistä tiedoista selaimen muistiin väliaikaisesti. Tämä voi auttaa käyttäjiä välttämään tietojen menetyksen esimerkiksi sivun päivittämisen tai vahingossa sivulta poistumisen yhteydessä.

Lomakkeita voi tyylitellä suhteellisen helposti, tosin osa, kuten radio-button kentät ovat hieman haastteellisempia. Useimmiten tyyylittelyyn käytetään myös **:valid** ja **:invalid** pseudoluokkia.
https://www.w3schools.com/css/css_form.asp

**_Varmista että sisäänkirjatumislomakkeessasi toimii validointi, joka vastaa taustapalvelun vaatimuksia. Näin saat minimoitua käyttäjän tekemät virheet kirjaantumisessa._**

# 📖 Vite.js - FE Julkaisuohjeet

Valmis Vite-projekti julkaistaan dist-kansioon, koska se toimii lopullisena tuotantoversiona. Vite kokoaa ja optimoi projektin tiedostot (HTML, CSS, JS) ja tallentaa ne tähän kansioon. Näin saat:

- 🚀 Optimoidun suorituskyvyn: Koodi minifioidaan ja jaetaan tehokkaasti.
- 📁 Staattiset tiedostot: dist-kansio sisältää vain staattisia tiedostoja, jotka ovat helposti jaettavissa palvelimelle.
- 💡 Selkeä erottelu: Kehityskoodi (src/) ja julkaistava koodi (dist/) pysyvät erillään.

Koko sovelluksen julkaisusta löydät kattavat ohjeet BE osuudesta:

[Deployment](https://github.com/mattpe/hyte-web-dev/blob/main/10-deployment.md#publishing-front-end-application-client)

## 📁 Hakemistorakenne

Varmista ensin että sinulla on järkevä hakemistorakenne sovelluksessasi. Alla näet yleisen rakenteen monisivuiselle web-projektille. Sinulla voi olla toisenkinlainen rakenne, kunhan saat järkevästi julkaistua projektisi dist kansioon ja polut toimivat.

Julkaisua sekä testaamista varten löydät vite-demoprojektin täältä. Mikäli olet epävarma omasta sovelluksesi toiminnasta, asenna tämä demoprojekti ja kokeile alla olevia kohtia ensin demolla. Katso myös miten tiedostoissa on viitattu erilaisiin tiedostojen relatiivisiin polkuihin.

```
my-vite-project/
│
├── public/               # Julkiset tiedostot (suoraan käytettävissä)
│   ├── favicon.ico       # Esim. favicon tai staattiset kuvat
│   └── img/
│       └── logo.png
│
├── src/                  # Lähdekoodi
│   │
│   ├── css/              # Tyylitiedostot
│   │   └── main.css
│   │
│   ├── js/               # JavaScript-tiedostot
│   │   ├── main.js       # Pääsivun JS
│   │   └── about.js      # About-sivun JS
│   │
│   └── pages/            # Kurssin testaussivut, ei julkaista
│       └── apitest.html  # Esim. apitest, ym. projektin testaus
│
├── dist/                 # Rakennettu projekti (automaattisesti luotu)
│
├── index.html            # Etusivu
├── bmi.html              # Toinen sivu
├── vite.config.js        # Vite-konfiguraatio
├── package.json          # Projektin riippuvuudet ja skriptit
└── README.md             # Projektin esittely

```

**1. Projektin valmistelu**
Varmista, että projektisi toimii paikallisesti ennen julkaisemista:

```bash
npm run dev
```

Jos kaikki toimii, siirry seuraavaan kohtaan.

**2. Vite Build -komento**

Vite tarjoaa sisäänrakennetun komennon projektin rakentamiseen dist-kansioon:

```bash
npm run build
```

Tämä luo dist/-kansion, joka sisältää optimoidut HTML-, CSS- ja JS-tiedostot.
Oletuksena tiedostot minifioidaan ja jaetaan tehokkaasti.

**3. Vite.config.js -asetukset**

Voit muokata rakennusprosessia vite.config.js -tiedostosta:

```js
// vite.config.js
import { resolve } from 'path';
import { defineConfig } from 'vite';

export default defineConfig({
	build: {
		rollupOptions: {
			input: {
				// List your html files here, e.g:
				main: resolve(__dirname, 'index.html'),
				bmi: resolve(__dirname, 'bmi.html'),
				diary: resolve(__dirname, 'diary.html'),
			},
		},
	},
	base: './',
});
```

**base:** Aseta suhteellinen polku, jos avaat projektin ilman palvelinta.<br>
**input:** Kaikki html tiedostot jotka haluat että julkaistaan

**4. Julkaisun testaaminen**

Voit testata dist-kansion sisältöä paikallisesti:

```bash
npm run preview
```

Tämä avaa kevyen palvelimen dist/-kansiosta.
Varmista, että kaikki toimii odotetusti. Mikäli sivustosi ei toimi, esim. kuvat eivät lataannu on yleensä kysessä tilanne, jossa polku tiedostoon on määritelty väärin.

**Linkkien polut**: Käytä suhteellisia polkuja (./about.html) html sivujesi sisäisessä linkityksissä.

## Viikkopalautus

Tämän viikon palautus linkittyy vahvasti BE julkaisuun. Julkaiskaa sivunne, sekä apitestaustiedostot dist kansioon. Voitte siirtää tämän jälkeen sivustonne joko users.metropolia.fi palvelimelle tai Azureen. Muistakaa ennen viimeistä vaihetta ja julkaisua muokata kaikki urlit localhostin sijaan viittaamaan oikeaan taustapalveluun.

Palauttakaa linkki sovelluksenne frontendiin.

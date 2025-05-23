# Workshop

## Intro

### Formål

Internett er ikke så komplisert og uangripelig som man kanskje skulle tro. I sin enkleste form handler det om å la maskiner prate med hverandre på forhåndsdefinert vis for å utveksle filer. 

Målet for sesjonen er å legge ut sitt helt egne innhold på Internett. Åpent og tilgjengelig for alle som vet hvor de skal lete. 

### Praktisk

Alle har fått utdelt påloggingsinformasjon til en såkalt virtuell maskin (VM). En VM oppfører seg for alle praktiske formål som en helt vanlig datamaskin. Det største skillet fra den til maskinen du sitter på nå vil være at VM-en kjører en variant av Linux, samt at den ikke er satt opp med et såkalt "skrivebordsmiljø" eller GUI (Graphical User Interface) – det betyr at vi i noen tilfeller må bruke kommandolinjeverktøy for å interagere med den. 

Gjennom dette dokumentet vil vi referere til VM-en din med sitt "hostname", eller domenet som peker til den. Eksempelvis `fagdagX.dgp.st` der `X` må byttes med tallet du fikk utdelt nå i stad. 

### Gjennomføring

Siden del 1, oppgave 2 ikke vil la seg gjennomføre uten videre om man ikke har administratortilgang på sin maskin ber vi dere om å gå sammen to og to – én teknisk og én ikke-teknisk. Jeg foreslår at den ikke-tekniske følger instruksjonene under veiledning av den tekniske gjennom i alle fall del 1. 

Du kan godt lese veiledningen i denne filen på Github, men du skal laste ned en kopi av dette prosjektet lokalt. Det kan gjøres ved trykke på den grønne "Code" knappen, og velge "Download ZIP". Denne pakkes ut til valgt mappe. Deretter anbefaler vi å åpne mappen i din foretrukne "editor" for å kunne redigere litt der det bes om. 

### Struktur

Alle filer som trengs for å løse en oppgave vil være å finne i egne mapper på strukturen `filer/delX/oppgX`

## Del 1 – det er bare en fil

Vi har gjort noen forberedelser som gjør at det allerede kjører en _webserver_ (caddy) på VM-en din. Det betyr at alle filer som blir lagt i mappen `/var/www/html` blir servert besøkende som går til `fagdagX.dgp.st`. Som vi lærte tidligere er det konvensjon å servere eventuelle besøkende en såkalt `index` fil – i vårt tilfelle `index.html`


### Oppgave 1

Vi skal altså rett og slett flytte en fil fra vår maskin til VM-en på Internett. Til dette kan vi bruke verktøyet `scp` som står for `secure file copy`. Her oppgir vi en kildefil, og en destinasjon denne skal kopieres til. 

Først kan du besøke `fagdagX.dgp.st` i nettleseren din og se at du ikke får opp noe. Du får en feilmelding. Nærmere bestemt har webserveren vår returnert HTTP koden 404 som betyr `Not Found`. Vi har altså ingenting på serveren vår. 

Kommandoen ser rett og slett slik ut: 

> [!IMPORTANT]  
> Denne kommandoen, og alle påfølgende, forutsetter at du står i samme mappe som denne filen du leser nå. Om dette ikke gir mening for deg, huk tak i en utvikler eller en av oss. 


```sh
scp filer/del1/oppg1/index.html dgpadmin@fagdagX.dgp.st:/var/www/html/index.html
```

Du vil bli spurt om passordet til brukeren din. 

Du kan nå besøke domenet ditt i nettleseren og du skal nå se følgende forhåpentligvis få opp en "Hello World!" melding. 

Du står nå fritt til å endre, legge til tekst og annet fjas du måtte ønske å bedrive på din helt nye nettside. For å se endringene må du kopiere filen på nytt. 

Mozilla (MDN) har en [fin guide](https://developer.mozilla.org/en-US/docs/Learn_web_development/Getting_started/Your_first_website/Creating_the_content) for å komme i gang. 

### Oppgave 2

I oppgave 1 så vi på å få en enkelt fil opp på serveren. Det er jo en god start, men ikke noe særlig til nettside å dele ut til verden. 

Vi kunne dratt i gang noe sånt som Wordpress[^1] eller et annet fullverdig CMS, men siden vi ønsker å holde oss til basics skal vi kikke på [Hugo](https://gohugo.io/) – en såkalt Static Site Generator. 

Hugo tar etter litt oppsett dine håndskrevne blogginnlegg (i [Markdown](https://markdownlivepreview.com/) format) og genererer all HTML, CSS (og eventuelt JavaScript) for å gi deg en fullverdig nettside/blogg å legge ut på Internett. Bonusen er at statiske HTML-filer er så ekstremt simple at det er praktisk talt umulig å kompromittere deg for en angriper på Internett. Bonusbonus for at siden din nærmest er dømt til å være rask så lenge du ikke fyller den med masse tunge bilder. 

«No har me juksa litt» som Ingrid Espelid Hovig ville sagt[^2] – og vi har laget et minimalt Hugo-prosjekt med et "tema" i `filer/del1/oppg2`. Det er fra den mappen de neste kommandoene må kjøres fra (se cheat-sheet i eksempler-mappen eller kjør `cd filer/del1/oppg2`). 

#### Sjekke ståa lokalt

Hugo kommer med sin egne webserver for å la deg se hvordan ting ser ut lokalt[^3]. 

> [!IMPORTANT]  
> På grunn av instillinger på Macen vil ikke Hugo tillates å kjøre før du har gjort følgende: 
>   1. Aktiver administratortilgang med privileges appen
>   2. kjør `sudo xattr -rd com.apple.quarantine ./bin/hugo`


```sh
./bin/hugo serve --buildDrafts
```

Om du navigerer til [http://localhost:1313](http://localhost:1313) i nettleseren så vil du se din helt nye nettside i all sin prakt. 

"Hello World" posten som du kan se finnes i mappen `content/posts/`. Om du ønsker å lage ditt neste blogginnlegg kan du lage en fil på samme format selv, eller la Hugo hjelpe deg litt på vei med 

```sh
./bin/hugo new content content/posts/my-next-post.md
```

Endrer du innhold i disse to postene vil du se at bloggen din oppdateres automatisk (lokalt). 

> Tips: draft statusen satt til `true` i filen gjør at Hugo ignorerer den med mindre du bruker `--buildDrafts` i kommandoene dine. Vi bruker det som standard nå så alt blir med mens vi tester. 

#### Få bloggen ut på nettet

Trikset til Hugo er at den bare genererer et sett med filer i riktig struktur basert på postene dine som gjør at de kan serveres direkte av en webserver akkurat som i oppgave 1. Vi skal altså laste opp denne filstrukturen til vår VM. 

Først skal vi bare endre `baseUrl` i `hugo.toml` så den matcher domenet til VM-en vår. 

Eksempelvis vil det se sånn her ut: 
```toml
baseURL = 'https://fagdagX.dgp.st'
```

Så vil vi la Hugo bygge filene for "produksjon". 

```sh
./bin/hugo --buildDrafts
```

Deretter kopierer vi alle filene i `public` mappen til VM-en vår: 

```sh
scp -r public/* dgpadmin@fagdagX.dgp.st:/var/www/html/
```

Nå kan vi sjekke ut bloggen på domenet til VM-en vår. 

Vipps så har du publisert en blogg på Internett.
 
## Del 2

Del 2 av oppgavene er splittet ut som [egne instruksjoner](filer/del2/oppg1/readme.md). 


[^1]: Wordpress er rett nok på vei ned i popularitet, men driver ifølge Wikipedia fortsatt drøyt en fjerdedel av topp 100 nettsider på nettet i Desember 2024. 
[^2]: [https://no.wikipedia.org/wiki/Ingrid_Espelid_Hovig](https://no.wikipedia.org/wiki/Ingrid_Espelid_Hovig)
[^3]: Den eneste grunnen til at en webserver trengs for å vise frem denne typen statiske filer lokalt på maskinen er stort sett for å håndtere "paths" eller filbaner korrekt. 
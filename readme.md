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
 
## Del 2 – Avansert

Om du har kommet så langt håper vi at du har fått pirret nysgjerrigheten noe. Hvis dette var såpass enkelt – hva mer går an å gjøre? Vi har allerede kikket litt på Hugo for å lage en en statisk netside, men det finnes hundrevis av tjenester man kan kjøre på en server og tilgjengeliggjøre på Internett. 

Herfra og inn blir det marginalt mindre håndholding. Noen ting vil man finne svar på i mappen `eksempler`, og lurer man på noe mer kan man spørre. 

### Oppgave 1 – Mealie

Mealie er en oppskriftsbok på nett. Her kan du legge inn oppskrifter, lage ukesplaner og handlelister. Dele tilganger med familie, venner og bekjente eller hele Internett om du så ønsker. Mealie støtter avanserte funksjoner som å lese ut en oppskrift fra et bilde av en kokebok ved hjelp av AI (krever konto hos OpenAI eller andre). 


Noen av kommandoene vi skal kjøre vil måtte kjøres på VM-en vår. Da må vi logge inn på den ved hjelp av ssh
```sh
ssh dgpadmin@fagdagX.dgp.st
```

Vi står nå i vår brukers hjemmemappe. Vi foreslår å opprette en egen mappe for det vi driver med nå. 

```sh
mkdir docker && mkdir docker/mealie && cd docker/mealie
```

> Protip: du kan kjøre `mkdir -p docker/mealie && cd docker/mealie` istedet om du vil lage begge mapper i en operasjon. 

Vi står nå i mealie-mappen. 

I et lokalt terminalvindu (altså på denne maskinen du leser dette på), må vi kopiere over filene fra `del2/oppg1` (navigér hit i terminalen din – husk `cd ..` tar deg ett nivå opp i mappehiarkiet). 

> MERK: Først må du endre verdien til `BASE_URL` i `.env`-filen til å matche din VM. F.eks `mealie.fagdag2.dgp.st`

```sh
scp docker-compose.yml .env dgpadmin@fagdagX.dgp.st:~/docker/mealie/ 

```

Vi har nå overført de to filene `docker-compose.yml` og `.env` som sammen inneholder informasjonen docker trenger for å kjøre en såkalt container med programmet `Mealie`. Du må gjerne åpne `docker-compose.yml` om du er nysgjerrig på hvordan det hele ser ut. 

Tilbake i terminalen der vi er tilkoblet VM-en så kan vi kjøre (fortsatt stående i `~/docker/mealie`-mappen): 
```sh
sudo docker compose up -d 
```

Dette spinner opp Mealie og en tilhørende database og gjør alt klart til ditt første besøk. 

> Merk: Caddy som vi nevnte tidligere er også her forhåndskonfigurert med litt juks. Om du er nysgjerrig kan du ta en titt ved å kjøre `cat /etc/caddy/Caddyfile` i terminalen som er tilkoblet VM-en din. 

Om du nå besøker `mealie.fagdagX.dgp.st` vil du nå komme til din helt egne Mealie-instans. Logg gjerne inn og lek deg med din nye oppskriftsbok. Bon appetit! 

### Oppgave 2 – Freestyle

Det såkalt "selfhosted" -communitiet er etterhvert ganske så populært – og ikke minst [i vinden for tiden](https://arstechnica.com/gadgets/2025/05/self-hosting-is-having-a-moment-ethan-sholly-knows-why/). Ta en titt på hva som finnes og prøv å [spinn opp](https://selfh.st/apps/) noe som ser gøy ut! 

Eksempelfilen fra oppgave 1 dekker ganske mye av den grunnleggende oppbyggingen til docker-compose, men det kan være litt krøkkete likeså. Min anbefaling er å se om prosjektenes github-sider har ferdige `docker-compose.yml` (eller `compose.yml` som er den nye konvensjonen) klare til å spinnes opp – mange har det! 

Mine stalltips for programvare som gir verdi rett ut av boksen er: 

  - [Miniflux](https://miniflux.app/docs/docker.html) - rss leser
  - [Wallabag](https://github.com/wallabag/docker) - spar på og arkiver artikler du vil lese senere

Mange tjenester det er aktuelt å hoste på egenhånd vil det være aktuelt å populere med egne data. For eksempel er [Immich](https://immich.app/) og [Paperless-ngx](https://docs.paperless-ngx.com/) veldig populære som henholdsvis bildegalleri og dokumentarkiv (tenk alle kvittering, kontrakter ++ som komplement til Digipost), men da bør en ta et par ekstra steg når det kommer til å passe på dataene i tjenestene man kjører. Både for å hindre at uvedkommende får tilgang, og for å ikke miste data man er avhengig av. 

"Sunne" vaner, som sikring, oppdateringsrutiner, backup osv. er utenfor scope, men vit at det er nødvendig. Er man usikker er det sterkt anbefalt å hoste tjenestene på en maskin lokalt på nettverket uten å eksponere de på Internett før man er selvsikker nok på at man vet hva man driver med. 

Lykke til! 

[^1]: Wordpress er rett nok på vei ned i popularitet, men driver ifølge Wikipedia fortsatt drøyt en fjerdedel av topp 100 nettsider på nettet i Desember 2024. 
[^2]: [https://no.wikipedia.org/wiki/Ingrid_Espelid_Hovig](https://no.wikipedia.org/wiki/Ingrid_Espelid_Hovig)
[^3]: Den eneste grunnen til at en webserver trengs for å vise frem denne typen statiske filer lokalt på maskinen er stort sett for å håndtere "paths" eller filbaner korrekt. 
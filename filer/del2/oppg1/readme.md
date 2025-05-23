# Del 2 – Avansert

Om du har kommet så langt håper vi at du har fått pirret nysgjerrigheten noe. Hvis dette var såpass enkelt – hva mer går an å gjøre? Vi har allerede kikket litt på Hugo for å lage en en statisk netside, men det finnes hundrevis av tjenester man kan kjøre på en server og tilgjengeliggjøre på Internett. 

Herfra og inn blir det marginalt mindre håndholding. Noen ting vil man finne svar på i mappen `eksempler`, og lurer man på noe mer kan man spørre. 

## Oppgave 1 – Mealie

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

## Oppgave 2 – Freestyle

Det såkalt "selfhosted" -communitiet er etterhvert ganske så populært – og ikke minst [i vinden for tiden](https://arstechnica.com/gadgets/2025/05/self-hosting-is-having-a-moment-ethan-sholly-knows-why/). Ta en titt på hva som finnes og prøv å [spinn opp](https://selfh.st/apps/) noe som ser gøy ut! 

Eksempelfilen fra oppgave 1 dekker ganske mye av den grunnleggende oppbyggingen til docker-compose, men det kan være litt krøkkete likeså. Min anbefaling er å se om prosjektenes github-sider har ferdige `docker-compose.yml` (eller `compose.yml` som er den nye konvensjonen) klare til å spinnes opp – mange har det! 

Mine stalltips for programvare som gir verdi rett ut av boksen er: 

  - [Miniflux](https://miniflux.app/docs/docker.html) - rss leser
  - [Wallabag](https://github.com/wallabag/docker) - spar på og arkiver artikler du vil lese senere

Mange tjenester det er aktuelt å hoste på egenhånd vil det være aktuelt å populere med egne data. For eksempel er [Immich](https://immich.app/) og [Paperless-ngx](https://docs.paperless-ngx.com/) veldig populære som henholdsvis bildegalleri og dokumentarkiv (tenk alle kvittering, kontrakter ++ som komplement til Digipost), men da bør en ta et par ekstra steg når det kommer til å passe på dataene i tjenestene man kjører. Både for å hindre at uvedkommende får tilgang, og for å ikke miste data man er avhengig av. 

"Sunne" vaner, som sikring, oppdateringsrutiner, backup osv. er utenfor scope, men vit at det er nødvendig. Er man usikker er det sterkt anbefalt å hoste tjenestene på en maskin lokalt på nettverket uten å eksponere de på Internett før man er selvsikker nok på at man vet hva man driver med. 

Lykke til! 

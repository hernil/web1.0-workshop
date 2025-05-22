# Terminal cheat sheet

Liste ut filer og mapper i en mappe: 
```sh
ls
```

Liste ut alle filer, inkludert "skjulte" filer som starter med punktum: 

```sh
ls -la
```

Lage mapper: 

```sh
mkdir mappenavn
```

> Alle mapper ligger et sted under "rota" (root, eller `/`). Som oftest skal du nok legge filer i `/var/www/html` for denne workshoppen. 

> mapper og filer har navn. Man kan også referere til de med `..` som betyr "mappen over", og `.` som betyr mappen jeg står i nå. `~` er en referanse til din brukers hjemmemappe – som regel finnes denne på `/home/username/`.

Navigere i filsystemet: 

```sh
cd mappe1 				# <- Change Directory til mappe
cd ..					# <- cd "opp et nivå", altså i mappen du var i
cd mappe1/undermappe 	# <- cd til undermappe, en mappe i mappen mappe1
cd ../mappe2			# <- cd til mappe2 fra undermappe. mappe2 er på samme nivå som mappe1
```

Flytte fil / endre navn på fil: 

```sh
mv her/ligger/filen.txt her/skal/filen/ligge.txt
```

Kjøre kommando som superbruker (sudo - Super User DO): 

```sh
sudo hvilkensomhelstkommando parametre
```

f.eks 
```sh
sudo mv her/ligger/filen.txt her/skal/filen/ligge.txt
```

Kopiere en fil: 

```sh
mkdir mappenavn
```
Om dette skal gjøres for en hel mappe så pek til mappen istedet for filen og husk "flagget" `-r`
Kopiere en fil over nettverk: 
```sh
scp her/ligger/filen.txt brukernavn@domenet-til-serveren.no:/var/www/her/skal/filen/ligge.txt
```

Om dette skal gjøres for en hel mappe så pek til mappen istedet for filen og husk "flagget" `-r`. Dette gjelder både `cp` og `scp`.

```sh
scp -r her/ligger/mappen brukernavn@domenet-til-serveren.no:her/skal/mappen/ligge
```


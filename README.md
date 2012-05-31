fagmøte-git
=============
* Git er er distribuert versjonskontrollsystem for kildekode. Istedet for en sentralisert server, så kan man ha flere repositorier, som man kan hente ned kildekoden fra. I motsetning til subversjon, som kun har en sentralisert server. Dette gjør det veldig nyttig i open source prosjekter, hvor hvem som helst kan lage sin egen fork av et prosjekt, og eieren/løytnaten kan hente de endringene de vil fra tilgjenglige fork, og merge dette inn i hovedprosjektet. Man kan fortsatt velge å kjøre med en sentralisert server struktur, slik at alle sjekker inn i samme remote server.

* Mens andre versjonskontrollsystem, som subversjon, lagrer et sett med filer og endringer som har skjedd på disse filene, så lagrer Git fulle snapshots av alle filene, og lagrer heller en referanse av hvilke filer som blir brukt. 

* Første gang man henter ned et prosjekt så vil all historien bli lastet ned. Så ved bytting av branch, så er det kun en referansetabell som blir omskrevet. Dette gjør det veldig raskt å endre og lage nye brancher.

* Alt i git er sjekksummet med SHA-1. Kun en fil har samme sjekksum, så blir en kopi lagret.



Klon et prosjekt fra en remote server
=====================================

	git clone git@github.com:stigebil/fagmote-git.git
	cd fagmote-git

Konfigurer git
==============
Alle revisjoner og endringer blir merket med ditt navn, så kjør dette for å sette det globalt

	git config --global user.name "Your Name"
	git config --global user.email foo@bar.com
	git config --global color.ui auto

Lag en ny git alias

	git config --global alias.l "log --graph --pretty=oneline --abbrev-commit --decorate"

Dagligdagse kommandoer
======================

####1. Lag en ny fil med navn 1.txt
####2. Sjekk status på filen. Filen er "untracked"
	git status
####3. For å legge en fil til staging så skriv:
	git add 1.txt
####4. Sjekk status på fil. Sammenlign med forrige staus
####5. commit
	git commit -m "Added 1.txt."
####6. Sjekk historien
	git l

Fil 1.txt er nå sjekket inn i ditt lokale repository

####7. Gjør en endring på 1.txt
	git diff
	git add 1.txt
	git diff
	git diff --staged

####8. Unstage 1.txt.
	git reset 1.txt
	git status

####9. Sjekk inn fil igjen, men kjør nå følgende kommando
	git commit -am "Updated 1.txt"

Når man kjører git commit -a så automatisk stager man alle filer som er tracket av git, som det har skjedd en endring på

####8. Gjør en ny endring på 1.txt, og rull tilbake endringene
	git diff
	git checkout 1.txt
	git diff

####9. Slett filen fra git
	rm 1.txt
	git rm 1.txt
	git commmit -m"Deleting 1.txt"


Branching
=========

Det er kjapt å lage nye brancher i git. master tilsvarer trunk i subversion. Siden det er såpass lett og kjapt å lage branchers, så anbefales det å lage topic brancher fot hver enkelt ny feature, og så merge inn i utviklingsbranchen. Slik kan man enkelt jobbe med flere ting samtidig.

### 1. For å se dine brancher
	git branch

#### 2. Lag en ny branch
	git checkout -b ny-branch
	git branch -v

git checkout -b er det samme som å kjøre:

	git branch ny-branch
	git checkout ny-branch

#### 3. Sjekk inn en fil med navn nyfil.txt i ny-branch
#### 4. Bytt tilbake til branch master. Sjekk inn nyfil2.txt i master
#### 5. Prøv å slett branch ny-branch
	git branch -d ny-branch

#### 6. Merge inn ny-branch, see the history
	git merge ny-branch
	git l
#### 7. Bytt tilbake til ny-branch og gjør en endring på nyfil.txt. Sjekk filen inn
#### 8. Bytt tilbake til master og gjør en annen endring på nyfil.txt. Sjekk filen inn.
#### 9. Merge ny-branch inn i master
#### 10. Sjekk konfliktene
	git status

For å se orginal fil.

	git show :1:nyfil.txt

For å se master sin versjon

	git show :2:nyfil.txt

For å se ny-branch sin versjon

	git show :3:nyfil.txt

#### 11. Resolve konfliktene. Editer nyfil.txt. Fjern linjer med <<<<< ==== og >>>>
	git add nyfil.txt
	git commit -m "Merged"
	git l

#### 8. Slett ny-branch
	git branch -d ny-branch
	git branch -v

Manipulering av history
=======================

#### 1. Legg til en ny linje i nyfil.txt med en skrivefeil
	git commit -am "Updated nyfil.txt with fail"

#### 2. Sjekk logg
	git l
sha-a "Updated nyfil.txt with fail"

#### 3. Fiks skrivefeilen
	git commit -a --amend

#### 4. Sjekk loggen
	git l

Legg merke til at sjekksummen er anderledes, men commit-beskrivelsen er anderledes
sha-a' "Updated nyfil.txt with fail"

#### 5. Gjør følgende endringer
	echo "foo" >> nyfil.txt
	git commit -am "Added foo"
	echo "bar" >> nyfil2.txt
	git commit -am "Added bar"
	git l

#### 6. Rebase for å re-order commits
	git rebase -i HEAD~2

Nå kan man endre rekkefølgen på de 2 siste commitene. Den elste er først

pick sha-a "Added foo"
pick sha-b "Added bar"

Bytt rekkefølge på teksten slit at det står slik

pick sha-b "Added bar"
pick sha-a "Added foo"

Save/quit

#### 7. Sjekk loggen
	git l

#### 8. Vi gjorde en feil. Lat som om foo og bar ikke skjedde.
	git reflog

Denne viser hva "HEAD" var X commits siden
Finn 'commit (amend): Updated nyfil.txt'
	//Kjør denne hvor X er nummeret som stod siden commit (ammend)
	git reset --hard HEAD@{X}

Stashing
========
Stashing gjør det mulig å lagre unna arbeid som ikke er klar til å sjekkes inn. Slik at man kan jobbe på noe annet.

#### 1. Gjør en endring på nyfil.txt og nyfil2.txt. Ikke sjekk inn.
#### 2. Kjør:
	git status
	git stash save "Work in progress"
	git status
#### 3. Gjør en endring på nyfil.txt og sjekk inn denne endring
#### 4. Arbeidet er ferdig med nyfil.txt. Hent kode fra stashen
	git stash apply
#### 5. Fiks merge konflikten i nyfil.txt og sjekk inn koden.





Part 8 - Lokal branching with rebase
====================================

Hver gang vi har gjort en merge nå, så har vi lagd en fork i history. Med rebase, så kan man merge sammen kode, uten at det lages en fork i historien. Rebase "replays commits" over din egen kode, slik at man får en lineær historie.

Generel regel er at du ikke skal kjøre rebase for kode som er lastet opp til et public repository, siden det kan skape problemer for de andre på prosjektet.

#### 1. lag en ny branch, gjør en endring til nyfil.txt
	git checkout -b working
	echo "rebasing1" >> nyfil.txt
	git commit -am "Rebasing"

#### 2. Bytt tilbake til master, gjør en endring til nyfil2.txt
	git checkout master
	echo "rebasing2" >> nyfil2.txt
	git commit -am "Rebasing master"

#### 3. Bytt tilbake til branch working, legg merke til fork
	git checkout working
	git l working master

#### 4. Rebase ditt arbeid på toppen av master, legg merke til at det ikke lenger er noen fork
	git rebase master
	git l working master

http://git-scm.com/book/en/Git-Branching-Rebasing


Remote Branches
===============

Så langt har man kun sjekket inn kode til ditt lokale repository. Noen kommandoer du bruker når du jobber mot remote er push, clone, fetch og pull. En kort oversikt over kommandoene.

#### git clone url target-dir
Clone lager en kopi av hele prosjektet, inkludert brancher.

#### git remote -v
Lister opp url på remote branch.

#### git push
Pusher alle commits til remote reopsitories

#### git pull
Henter ned endring fra kilden for en ønsket branch. Dette gjør først git fetch for å hente koden ned fra remote og så en git merge inn i aktiv branch. For å kjøre denne så må all koden din være sjekket inn. Alternativt kan man dumpe den til stash og så kjøre git stash apply.

#### git fetch origin
Denne kommandoen henter ned koden fra remote server. Man gjør selv en merge(e.eks git merge origin master) på koden hentet ned til branchen din.

Mange anbefaler å gjøre en git fetch og en git merge istedet for git pull, for å ha bedre kontroll over hva som skjer.




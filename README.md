# FBS Postman klient

[Det Fælles Bibliotekssystem (FBS)](http://www.kombit.dk/bibliotek), udstiller et API bestående af en række REST services, som er beskrevet i "Cicero CMS integration" dokumentationen under http://fbsudrulning.dk/partner-bogen/.

API'et kan ikke tilgås fra en browser, og der findes ikke en web klient, som vi er vant til med nogle af de andre API'er vi benytter i folkebibliotekssektoren.

Hensigten med dette Github repository er at:
* Give biblioteksmedarbejdere mulighed for at se de data FBS API'et uden det forudsætter programmeringskompetencer.
* Dele en Postman collection bestående af et antal foruddefinerede forespørgsler til FBS.
* Beskrive hvordan man installerer postman.
* Beskrive hvordan man importerer FBS Postman collectionen.
* Beskrive hvordan man benytter environment variables til at autentificere FBS, bestemte brugere mv.

Målgruppen er webmastere der ønsker at se de data FBS udleverer til f. eks. DDB CMS.

FBS Postman collection giver mulighed for at hente følgende oplysninger fra FBS:

* Lånerstatus
  * Patron - getLoans -> Hjemlån (DDB CMS: /user/me/status)
  * Patron - getReservations -> Reserveringer (DDB CMS: /user/me/status/reservations) 
  * Patron - getFees -> Mellemværender (DDB CMS: /user/me/status/debts)
  * Patron - getBookingLoans
  * Patron - getBookings
* Biblioteksoplysninger
  * Library - getRootGroup
  * Library - getBranches -> Biblioteksfilial
  * Library - getDepartments -> Afdeling
  * Library - getLocations -> Opstilling
  * Library - getSublocations -> Underopstilling

## Installér Postman klienten

Postman er gratis værktøj med en grafisk brugergrænseflade, der kan installeres på Windows, Mac og Linux computere, og den egner sig derfor godt til brugere, som undertegnede, der ikke færdes hjemmevant i en kommandoprompt og ikke kan programmere op imod et API.

Postman kan downloades fra https://www.getpostman.com/ og installeres som ethvert andet program, og det forudsætter derfor også administratorrettigheder at installere.

## Importér FBS.postman_collection

"FBS postman collection" er en samling af REST service kald som er defineret ud fra "Cicero CMS integration" dokumentationen under http://fbsudrulning.dk/partner-bogen/.

Samlingen består primært af to POST kald der autentificerer henholdsvis "Postman klienten" og Låneren (Patron) og en række GET kald der henter forskellige typer af information ud af systemet. 

1. Download [FBS Postman collection](https://raw.githubusercontent.com/rolfmadsen/FBS-Postman-client/master/FBS.postman_collection.json), som er delt i dette Github repository, og gem filen på dit skrivebord
2. Åben Postman
3. Tryk på "Import" knappen i den øverste grå menu linje og tryk på "Choose Files" knappen i det Import vindue der dukker op midt på skærmen.
4. Dobbelt-klik på "FBS postman collection" filen på dit skrivebord for at importere den.

## Benytte Environment variables

Postman giver mulighed for at benytte Environment variables, der er variabler gør det let at skifte imellem forskellige FBS miljøer, Lånere mv.

Variabler fremgår som {{variabel}}.

Variablerne kan ikke deles da de indeholder brugernavne og passwords til FBS systemet og brugere.
Derfor kan jeg kun vise hvilke variabler der er indsat i kaldene og hvordan de oprettes.

1. Tryk på tandhjulet i øverste højre hjørne
2. Tryk på "Manage environments"
3. Tryk på "Add" knappen nederst i det "Manage environments" vinduet der dukker op
4. Skriv navnet på det environment du ønsker at oprette, f. eks. "FBS", i feltet hvor der står "Environment name" lige under overskriften "Add environment".
5. Tryk på det orange "Bulk edit" link til højre i vinduet.
6. Indsæt teksten herunder:
```
host:https://cicero-fbs.com/rest/external/v1/
agencyId:DK-******
username:**********
password:**********
libraryCardNumber:**********
pincode:****
sessionKey:
patronId:
```
7. Udskift de dele af teksten der er markeret med \*'ner med følgende oplysninger 
  * "agencyId:DK-" efter "DK-" tilføjes biblioteksnummer (ISIL/agency)
  * "username:" Brugernavnet til FBS kan findes i DDB CMS under /admin/config/ding/provider/fbs
  * "password:" Password til FBS kan findes i DDB CMS under /admin/config/ding/provider/fbs
  * "libraryCardNumber:" CPR-nummer eller Biblioteksnummer for den bruger der skal laves opslag på i FBS
  * "pincode:" Pin-kode for den bruger der skal laves opslag på i FBS

### sessionKey og patronId

"sessionKey" og "patronId" er variabler der leveres af FBS og udfyldes når kaldene laves.

#### sessionKey

sessionkey hentes ud af svaret fra FBS på Client autenticate kaldet via "Tests" fanebladet og sættes på sessionId variablen på følgende måde:

```
var data = JSON.parse(responseBody);
postman.setEnvironmentVariable("sessionKey", data.sessionKey);
```
#### patronId
patronId hentes ud af svaret fra FBS på Client autenticate kaldet via "Tests" fanebladet og sættes på patronId variablen på følgende måde:

```
var data = JSON.parse(responseBody);
postman.setEnvironmentVariable("patronId", data.patron.patronId);
```

## Sådan laver du kald til FBS

For at lave et kald til FBS skal Postman klienten og låneren (patron) først autentificeres. 

Det gør man ved at vælge:
1. "Client authenticate" og trykke på den blå "Send" knap.
2. "Patron authenticate" og trykke på den blå "Send" knap.

Herefter kan man vælge de kald man ønsker at udføre og trykke på den blå "Send" knap.

Hvis man har oprettet flere Environment variables, f. eks. hvis man har et antal testbrugere man vil kunne skifte let imellem, skal man huske at autentificere både Postman klienten og Låneren igen.

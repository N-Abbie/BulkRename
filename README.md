# Rename Tool

Rename Tool is een Windows GUI-tool (PowerShell + WinForms) om documenten in bulk te hernoemen op twee manieren: (1) automatisch op basis van een namenlijst (“Batch Renamer”) en (2) interactief, waarbij je bestanden één-voor-één visueel doorloopt met een preview (“Interactive Renamer”).

## Wat je ermee kunt

Je kiest in het startmenu tussen **BATCH RENAMER** (lijstgestuurd) en **INTERACTIVE RENAMER** (visueel doorlopen). De Batch Renamer valideert je lijst, markeert conflicten en kan daarna uitvoeren. De Interactive Renamer toont een preview (PDF/afbeeldingen waar mogelijk) en laat je per bestand datum/bron/titel invullen waarna de naam direct wordt toegepast.

## Vereisten

Dit is een Windows-only tool (WinForms). Voor het inlezen van `.xlsx`-lijsten gebruikt de Batch Renamer Excel via COM, dus daarvoor is Microsoft Excel nodig. Zonder Excel kun je `.csv`/`.txt` gebruiken of de lijst vanuit het klembord plakken.

## Starten

Zet het `.ps1`-bestand op je pc.

**Optie 1:** Start met PowerShell (bij voorkeur Windows PowerShell) en run het script, bijvoorbeeld:

PowerShell

```
PowerShell.exe -NoProfile -ExecutionPolicy Bypass -File ".\RenameTool.ps1"
```

**Optie 2:** Zet het bestand over op een `.exe` file met de PS2EXE module. Met een `.ico` file pas je het logo aan.

PowerShell

```
Install-Module -Name ps2exe -Scope CurrentUser
Set-Location "BESTANDSPAD NAAR JE .PS1 FILE"
Invoke-PS2EXE -InputFile ".\RenameTool.ps1" -OutputFile ".\BulkRename.exe" -IconFile ".\JOUWICOON.ico" -noConsole
```

## Toelichting Batch Renamer (lijstgestuurd)

In de Batch Renamer hernoem je in één keer een grote set bestanden op basis van een lijst met “oude naam” en “nieuwe naam”.

### Werkwijze in het kort

1. Kies een map met documenten (slepen van een map naar de tool wordt ondersteund). Optioneel kun je “Inclusief submappen” aanvinken.
2. Laad je namenlijst:
    - Plak Klembord (kopieer de volledige lijsttekst en plak via de knop), of
    - Kies bestand (ondersteunt `.csv`, `.txt`, en `.xlsx`).  
3. Klik **Controleer** om te laten valideren of elk bestand gevonden wordt en of de nieuwe naam geen conflict oplevert.
4. Klik **Start hernoemen** om de hernoeming uit te voeren.  
5. Na uitvoeren kun je (voor zover mogelijk) terugdraaien met **Herstel**.
    
### Format van de namenlijst (CSV / tekst)

De Batch Renamer verwacht regels in de vorm: `Oude bestandsnaam;Nieuwe bestandsnaam`

Voorbeeld:

Plaintext

```
scan001.pdf;20251217_Volkskrant_Schade aan huizen door aardbevingen Groningen.pdf
```

> **Belangrijk:** zet de extensie in de nieuwe bestandsnaam (bijv. `.pdf`), want de Batch Renamer neemt de nieuwe naam letterlijk over.

### .xlsx-invoer

Als je een `.xlsx` kiest, leest de tool het eerste werkblad uit en pakt alle cellen waar een puntkomma (`;`) in staat. De tool bouwt daar zelf een “Oud;Nieuw”-lijst van.

### Statussen bij controleren/uitvoeren

Tijdens **Controleer** en **Start hernoemen** kan een regel o.a. deze statussen krijgen:

- **OK** (klaar / voltooid)
    
- **Conflict** (doelnaam bestaat al)
    
- **Niet gevonden** (bronbestand ontbreekt)
    
- **In gebruik** (bestand staat open/vergrendeld)
    
- **Fout** (andere fout tijdens hernoemen)
    
- **Te lang** (pad/naam wordt te lang, alleen tijdens uitvoeren)
    

### Naam-sanitizing en case-only renames

Nieuwe namen worden opgeschoond naar geldige Windows-bestandsnamen (o.a. ongeldige tekens eruit/omgezet en trailing spaties/punten weg). Als alleen de hoofdlettering verandert (case-only rename), gebruikt de tool een tijdelijke tussennaam om dit op Windows betrouwbaar te laten slagen.

### Undo (Herstel)

De Batch Renamer houdt een undo-lijst bij van uitgevoerde hernoemingen en biedt daarna een **Herstel**-knop die de (toen) uitgevoerde wijzigingen terug probeert te zetten. Dit is “best effort”: als bestanden intussen verplaatst/gewijzigd zijn, kan herstel deels mislukken.

## Tool 2: Interactive Renamer (visueel, één-voor-één)

In de Interactive Renamer loop je bestanden één-voor-één door. Links vul je metadata in, rechts zie je een preview als dat kan.

### Werkwijze in het kort

1. Kies een map (optioneel met “Inclusief submappen”).
2. De tool laadt het eerste bestand.
3. Vul datum, bron en titel in. Als de bestandsnaam al delen bevat (bijv. underscores en een datum), probeert de tool velden vooraf te vullen.
4. Bevestig met de knop (bijv. “Opslaan & Volgende”): het bestand wordt direct hernoemd en het volgende bestand verschijnt.
5. Bij een fout kun je terug met **Herstel vorige** (of “Vorige” als er niets te herstellen is).   

### Uitvoerformaat in Interactive Renamer

De nieuwe naam wordt opgebouwd als:

Plaintext

```
YYYYMMDD_Bron_Titel<originele-extensie>
```

De extensie blijft dus behouden.

> **Tip:** als dag/maand onbekend is, kun je 00 gebruiken (bijv. 00-00-2025), zodat de output `20250000_...` wordt.

### Preview gedrag
- **PDF’s** worden getoond via de ingebouwde WinForms WebBrowser-control (werkt alleen als jouw Windows-installatie PDF’s in die control kan renderen).
- **Afbeeldingen** worden als afbeelding-preview getoond.
- **Voor andere bestandstypen** verschijnt een melding dat er geen preview is, met een knop om het bestand extern te openen.

## Bekende beperkingen
- Batch Renamer zet de bestemming standaard in de gekozen map. Als je in “Oude naam” submappaden gebruikt, kan dat ertoe leiden dat bestanden bij de hernoeming in de hoofdmap terechtkomen (de tool bewaart in Batch-modus niet automatisch de oorspronkelijke submapstructuur).
- Voor `.xlsx`-import is Excel nodig.
- Preview van PDF’s is afhankelijk van wat de WebBrowser-control op jouw systeem kan weergeven.

# Einführung in TUSCRIPT II

## Skripte
A. TRACE

B. RD_WR_UTF8

C. CHANGE_TAG

D. TF2XML

## Übungsdateien

- TEI.XML
- mfslit.tf

## tustep.ini
- init
- edit

# Dateizugriffe   

## Zugriff im Modus `STREAM` auf eine XML-Datei

- Datei `source` anmelden mit Lesezugriff 
`ERROR/STOP OPEN (source,READ,-std-)`

- Dateizugriff auf FDF-Datei im Modus STREAM
- MODI

```
`READ`  =  Lesezugriff
`FILE`   = Zugriff auf Datei
`STREAM` = Portionsweiser Zugriff
`UTF8`   = ZUgriff auf UTF8 codierte Datei
``` 

- Variablen

`s` = Satznummern (bei /FILE/UTF8 und /VARIABLE)

`s.z/u` = Satznummern (bei ZUGRIFF auf TUSTEPDATEI)

`a` = Anfangskennung der eingelesenen Portion 

`t` = Inhalt der durch Anfangs- und Endekennung definierten Portion

`e`=  Endekennung der eingelesenen Portion 

`typ` = Typ der gestreamten "Portion" 

`stack` = Tag-Hierarchie

`stack_num` = Tag-Hierarchie (nummeriert)

- LESEN/Schreiben
 
`LOOP/#` = Schleifendurchläufe

`READ/EXIT q` = Portionsweises Einlesen der unter `q` angegebenen Datei/Variable bis Ende
`Write z` = Portionsweises Schreiben der unter `z` angegebenen Datei/Variable

`ENDLOOP` = Schleife beenden

`ENDACCESS` = Lesezugriff beenden 

`TRACE` = Protokoll einschalten 

```bash
ACCESS q: READ/FILE/STREAM/UTF8 source s,a+t+e,typ,stack,stack_num
 LOOP/99999
  READ/EXIT q
  --- Hier folgen Makroanweisungen
  TRACE
 ENDLOOP
ENDACCESS q
```

### Übungsaufgaben
- Tagname ändern (p in ab)                                    
- Tagname ändern, kontextabhängig (nur p innerhalb des text-Elements)

# Dateizugriff auf strukturierte Dateien  (≠xml)

- Struktur definieren

`a`- definiert durch S_TABLE 
 

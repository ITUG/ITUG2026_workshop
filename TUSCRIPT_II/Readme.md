# Einführung in TUSCRIPT II

## Skripte
A. TRACE

```bash
$$ MODE TUSCRIPT,{}
source="tei.xml"
ERROR/STOP OPEN (source,READ,-std-)

ACCESS q: READ/FILE/STREAM/UTF8 source s,a+t+e,typ,stack,stack_num
LOOP/99999
READ/EXIT q
 --- Makroanweisungen
 TRACE
ENDLOOP
ENDACCESS q
```

B. RD_WR_UTF8

```bash
$$ MODE TUSCRIPT,{}

source="tei.xml"
ERROR/STOP OPEN   (source,READ,-std-)

target="tei_neu.xml"
ERROR/STOP CREATE (target,FDF-o,-std-)

ACCESS q: READ/FILE/STREAM/RECORD/UTF8 source ...
    s, a+t+e, typ, stack, stack_num
ACCESS z: WRITE/FILE/ERASE/STREAM/UTF8 target ...
    s, a+t+e

 LOOP/99999
  READ/EXIT q
  --- Makroanweisungen
  WRITE z
 ENDLOOP
ENDACCESS q
ENDACCESS/PRINT z

```

C. CHANGE_TAG

```bash
$$ MODE TUSCRIPT,{}
source="tei.xml"
ERROR/STOP OPEN (source,READ,-std-)

target="tei_neu.xml"
ERROR/STOP CREATE (target,FDF-o,-std-)

ACCESS q: READ/FILE/STREAM/RECORD/UTF8 source ...
    s, a+t+e, typ, stack, stack_num
ACCESS z: WRITE/FILE/ERASE/STREAM/UTF8 target ...
    s, a+t+e

 LOOP
  READ/EXIT q
   IF (stack.ct."|<text>|"&&a.hn."p") a=START_TAG ("quote")
   IF (stack.ct."|<text>|"&&e.hn."p") e=END_TAG   ("quote")
  WRITE z
 ENDLOOP

ENDACCESS/PRINT q
ENDACCESS/PRINT z
```

D. TF2XML

```bash
$$ MODE TUSCRIPT,{}
-- Dateien anmelden / einrichten
source="mfslit.tf"
target="mfslit_neu.tf"

ERROR/STOP OPEN   (source,READ,-std-)
ERROR/STOP CREATE (target,SEQ-o,-std-)

-- tables definieren
BUILD S_TABLE entry_beg=*
DATA |{4-00}#|

BUILD S_TABLE sigle=*
DATA |@???\*|

BUILD X_TABLE klammern=*
DATA |<|^<|
DATA |>|^>|

-- Variable(n) belegen
ss=0

-- DATEIZUGRIFFE
ACCESS q: READ/FILE/STREAM        source ...
   s. z /u, a/entry_beg+t+e,typ,stack
ACCESS z: WRITE/FILE/ERASE/STREAM target ...
   ss.zz/uu,a          +t+e

 LOOP/9999999
  READ/EXIT q
   IF (typ!=1) CYCLE

   t=JOIN (t,"")

   IF (t=="") CYCLE

   -- Spitzklammern ersetzen
   t=EXCHANGE (t,klammern)
   t=SPLIT (t,|sigle)

   LOOP/CLEAR line=t
     IF (line.sw."*","@*") CYCLE

     sigle=EXTRACT (line,2,5)
     aken=START_TAG (sigle)

     text=EXTRACT (line,6,0)
     text=SQUEEZE (text)
     IF (text=="") CYCLE

     eken=END_TAG (aken)

     line_new=CONCAT (aken,text,eken)

     t=APPEND(t,line_new)
    ENDLOOP

    -- Zieldatei aufsteigend nummerieren
    ss=ss+1
    zz=1
    uu=0

    -- XML-Struktur
    a=START_TAG ("item")
    a=SET_ATTRIBUTE (a,"n",ss)
    e=END_TAG (a)
   WRITE z
  ENDLOOP
ENDACCESS/PRINT q
ENDACCESS/PRINT z
```

## tustep.ini
- init

```bash
#an,,skripte
#de,skripte
#ed,makro=COLOR1
```
- edit

```bash
= Farbeinstellungen
c=MAC
c1,=MFS-Beispiel
c1,1=CF:|{4-00}#|
c1,2=DF:|@???\*|

= Makroleiste definieren
y,*=execute
y,execute=clr_cmd_line,"x #ma,<editor>",confirm

= EDITOR MAKROANWEISUNGEN (ausfuehren mit alt-p)
y,md.=SET_INS,"MODE DATA",SPLIT
y,ms.=SET_INS,"$$ MODE STATEMENT",SPLIT
y,mt.=SET_INS,"$$ MODE TUSCRIPT,{}",SPLIT
y,color1=COLOR:1
```

# Dateizugriffe   

## 1. Zugriff im Modus `STREAM` auf eine XML-Datei

### Übungsdatei

- TEI.XML

### ACCESS

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

### Variablen

`s` = Satznummern (bei /FILE/UTF8 und /VARIABLE)

`s.z/u` = Satznummern (bei ZUGRIFF auf TUSTEPDATEI)

`a` = Anfangskennung der eingelesenen Portion 

`t` = Inhalt der durch Anfangs- und Endekennung definierten Portion

`e`=  Endekennung der eingelesenen Portion 

`typ` = Typ der gestreamten "Portion" 

`stack` = Tag-Hierarchie

`stack_num` = Tag-Hierarchie (nummeriert)

### LESEN/Schreiben
 
`LOOP/#` = Schleifendurchläufe

`READ/EXIT q` = Portionsweises Lesen der unter `q` angegebenen Datei/Variable 

`/EXIT` - bis Ende, dann ACCESS verlassen

`WRITE z` = Portionsweises Schreiben der unter `z` angegebenen Datei/Variable

`ENDLOOP` = Schleife beenden

`ENDACCESS` = Lesezugriff beenden 


```bash
ACCESS q: READ/FILE/STREAM/UTF8 source s,a+t+e,typ,stack,stack_num
 LOOP/99999
  READ/EXIT q
  --- Hier folgen Makroanweisungen
  TRACE
 ENDLOOP
ENDACCESS q
```

### Makroanweisungen
`TRACE` = Protokoll einschalten 

`START_TAG` 

`END_TAG` 

### Übungsaufgaben
- Tagname ändern (`p` in `quote`)                                    
- Tagname ändern, kontextabhängig (nur `p` innerhalb des text-Elements)

## 2. Zugriff im Modus `STREAM` auf eine strukturierte Datei (≠xml)

### Übungsdatei
mfslit.tf

### Aufgabe

XML-Struktur entwickeln

### ACCESS - Variablen 

`a` - definiert durch `S_TABLE` 

`t` - Inhalt nach `a` bis zum folgenden `a`

`e` - undefiniert

`typ=1` - Portion mit `a` (wie in `S_TABLE` definiert)

`typ=0` – Portion ohne `a` (wie in `S_TABLE` definiert)

### Makroanweisungen

`CYCLE` – weiter im Schleifendurchgang

`JOIN`

`SPLIT`

`EXTRACT` 

`EXCHANGE` 

`SQUEEZE` 

`SET_ATTRIBUTE` 

`CONCAT` 

`APPEND` 	
 

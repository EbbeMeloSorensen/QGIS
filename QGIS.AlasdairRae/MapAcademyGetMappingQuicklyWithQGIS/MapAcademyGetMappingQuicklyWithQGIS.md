## Udemy Course: "Map Academy: get mapping quickly, with QGIS"

### Instructor: Alasdair Rae

https://www.udemy.com/course/mapacademy/


## Summary

Hands on kursus om QGIS

## Den røde tråd

### Section 1: Introduction

Terminologi og installation

### Section 2: Know your way around QGIS

Han introducerer brugergrænsefladen og demonstrerer nogle basale operationer

1) Lav et nyt projekt ved at klikke på Project->New

2) Klik i 'Coordinate'-feltet nederst i skærmen, skriv world og tryk enter for at tilføje et world map

3) Leg evt lidt med at zoome og panorere

4) Dobbeltklik på WorldMap i Layers-panelet for at vise Layer Properties. Vælg i dialogen Symbology og ændr farverne for Fill og Stroke

5) Klik på Projects->Properties->General og ændr i dialogen Background color fra hvid til en anden farve

6) Klik på Projects->Properties->CRS (Coordinate Reference System) for at vælge et koordinatsystem, som f.eks. WGS84, EPSG:4326

7) Gem projektet - det gemmes som en *.qgz-fil. Bemærk, at det ikke omfatter layers. Sørg for at layers kan nås på deres originale lokation. Gem jævnligt.

8) Klik på Settings->Options->General og check 'Override System Locale' for at ændre sprogindstillinger, hvilket f.eks. påvirker, hvordan tal skrives

9) Klik på Settings->Options->General og ændr UI theme til Night Mapping

   (Man kan også ændre en del andre ting på Settings->Options->General for at påvirke QGIS miljøet)

10) Klik på Help for at se, hvilke Help-faciliteter der er tilgængelige.

11) Klik på View for at se, hvilke View-muligheder, der er, f.eks. full screen

12) Klik på "Identify Features"-knappen i øverste toolbar og klik derefter på en feature såsom et land på kortet for at se attributter og deres værdier såsom landenavn

### Section 3: Let's make a map

1. Lav et nyt projekt

2. Klik på "Open Data Source Manager"-toolknappen yderst til venstre. Klik så på "Vector", og vælg filen 'world-countries-ne.gpkg' i Source-sektionen (resource fra kurset)

3. Klik nu på "Delimited Text" og åbn populated-places.csv filen. Sikr, at settings står korrekt for import af filen

   (Nu skulle der gerne være 2 layers: Polygoner for lande og dots for populated places. Bemærk, at datasættet er lavet af Natural Earth, hvor man også kan hente mange andre datasæt)

4. Drag layers i Layers panelet for at ændre på, hvilke layers der ligger øverst

5. Dobbeltklik på et layer i Layers panelet for at åbne Layer Properties dialogen, hvor man kan ændre farver.

6. Select de forskellige layers i Layers panelet og klik på toolbar-knappen "Open Attribute Table" for at få vist tabeldata for lagene. Bemærk, at når man vælger en række i tabellen, så highlightes featuren i mappet.

7. Højreklik på populated-places-layeret i Layers panelet og vælg Filter i context menuen for at åbne Query Builder dialogen. Dobbeltklik på felter POP_MAX. Tilføj " > 10000000" og klik på Test. Åbn Attribute tabellen og bemærk, at den nu kun viser de byer, der passerer filteret. Bemærk også, at der er kommet et lille filter ikon på layeret i Layers panelet - det kan man klikke på for at ændre filteret.

8. Klik på Projection-feltet til højre i statusbaren og skift projektionen til World Winkel Tripel NGS

9. Ændr evt lidt på farverne. Bemærk, at man kan dragge og droppe farver i Layer properties->Symbology, f.eks. hvis man gerne vil have stroke som en mørkere variant af fill.

10. Dobbeltklik på populated-places-layeret i Layers panelet og vælg Labels og derefter Single Labels. Sikr, at der feltet NAME er valgt i Value feltet, ændr evt font og klik Apply.

11. Juster baggrund og placering for labels i Layer Properties dialogen.

12. Klik på View->Decorations->Title Label i menuen for at tilføje en titel

13. Klik på View->Decorations->Copyright Label i menuen for at tilføje en copyright label

14. Panorer og zoom, så det ser passede ud, og klik så på Project->Import/Export->Export Map to Image for at eksportere til en pdf fil

15. Højreklik på world-countries-ne-laget i Layers panelet og vælg Duplicate Layer

16. Lav et filter med Name = ''Indonesia" for det nye Layer

    (Han laver et kort over indonesien med 5 udvalgte byer, hvor man også kan se nabolande i en svagere nuance. Han styler også kortet, så det ser pænt ud. Resten af sectionen handler i det hele taget om styling)

### Section 4: Data handling in QGIS

Han starter denne sektion med at sige, at man skal være kritisk over for data

1. Lav et nyt projekt
2. Klik på "Open Data Source Manager" toolknappen, vælg **Vector** og klik så på browse-knappen for at vælge shape-filen nz-focus, som kan downloades fra kurset. Bemærk, at en "shapefil" består af et antal filer, som ligger side om side. Selv om det er muligt at vælge zip-filen, anbefaler han at unzippe først. Han demonstrerer også, at man bare kan dragge en shapefil fra Explorer ind på Map Canvas for at oprette et lag.
3. Klik på "Open Data Source Manager" toolknappen, vælg **Raster** og klik så på browse-knappen for at vælge filen milford-nz-10km.tif, som kan downloades fra kurset. Vælg layeret i Layers-panelet, og klik 'Zoom to Layer' i toolbaren. Dobbeltklik på layeret i Layers-panelet. Vælg så Symbology og skift render type fra singleband gray til hill shade - det giver en visuelt flot effekt.
4. Klik på "Open Data Source Manager" toolknappen, vælg **Delimited Text** og klik så på browse-knappen for at vælge filen nz-town-city-lat-long.csv, som kan downloades fra kurset. Sikr, at settings i Geometry Definition fsa projektion og valg af kolonner står korrekt (bemærk, at QGIS i vidt omfang selv kan genkende kolonnenavne) og klik Add for at lave et lag med new zealandske byer.
5. Højreklik på laget og vælg Export->Save Features As. Vælg så GeoPackage som output format,  indtast et filnavn og klik på OK. Bemærk at den pr default laver et nyt layer baseret på GeoPackage-filen og tilfæjer det til mappet - så kan man passende fjerne det lag, der baserer sig på csv-filen.












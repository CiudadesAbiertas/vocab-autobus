# Ejemplos de consultas

Se ha realizado un proceso de evaluación a través de unos datos de prueba generados, para comprobar la cobertura del vocabulario con respecto a los requisitos planteados.

Para ello se ha realizado la transformación a partir de algunos datos publicados por los ayuntamientos de Madrid y Zaragoza en sus portales de datos abiertos.

Los datos transformados y anotados con este vocabulario se han cargado en un Virtuoso endpoint disponible en: http://ciudadesabiertas.linkeddata.es/sparql.

A continuación, se presentan varias consultas que se han ejecutado en este endpoint y que han permitido evaluar el vocabulario de acuerdo con los requisitos que se plantearon en la actividad de especificación. 

Los grafos de trabajo, con datos de prueba, se encuentran en:
- http://vocab.ciudadesabiertas.es/grafo/transporte/autobus/datos-autobus-ejemplos
- http://vocab.ciudadesabiertas.es/grafo/transporte/autobus/datos-autobus-extendido

## Consulta 1 Dame el orden de la parada 4608 dentro de la ruta 138b y las características de esa parada
Se utiliza el grafo http://vocab.ciudadesabiertas.es/grafo/transporte/autobus/datos-autobus-ejemplos
```
PREFIX dcterms:<http://purl.org/dc/terms/> 
PREFIX esautob:<http://vocab.ciudadesabiertas.es/def/transporte/autobus#> 
PREFIX schema:<http://schema.org/> 
PREFIX tmcommons:<https://w3id.org/transmodel/commons#> 
PREFIX tmjourney:<https://w3id.org/transmodel/journeys#> 
PREFIX tmorg:<https://w3id.org/transmodel/organisations#> 
PREFIX xsd:<http://www.w3.org/2001/XMLSchema#> 
PREFIX dcterms:<http://purl.org/dc/terms/>
PREFIX geosparql:<http://www.opengis.net/ont/geosparql#>
PREFIX geo:<http://www.w3.org/2003/01/geo/wgs84_pos#>

SELECT  ?orden ?nombreParada ?wifi ?panel ?lat ?long  where {
	?ruta a tmjourney:Route .
	?ruta dcterms:identifier "138b"^^xsd:string .
	?ruta tmjourney:madeUpOf ?puntoRuta .
	?puntoRuta tmjourney:order ?orden . 
	?puntoRuta tmjourney:viewedAs ?punto .
	?punto tmcommons:functionalCentroidFor ?parada .
	?parada dcterms:identifier "4608"^^xsd:string .
	?parada schema:name ?nombreParada .
           ?parada esautob:wifi ?wifi .
           ?parada esautob:panelElectronico ?panel .
           ?parada geosparql:hasGeometry ?point .
           ?point geo:long ?long .
           ?point geo:lat ?lat
}
```
## Consulta 2. Dame los horarios y frecuencias de la ruta 138a Línea 138 con inicio en Cristo Rey y destino en San Ignacio de Loyola.
Se utiliza el grafo http://vocab.ciudadesabiertas.es/grafo/transporte/autobus/datos-autobus-ejemplos
```
PREFIX dcterms:<http://purl.org/dc/terms/> 
PREFIX esautob:<http://vocab.ciudadesabiertas.es/def/transporte/autobus#> 
PREFIX schema:<http://schema.org> 
PREFIX tmcommons:<https://w3id.org/transmodel/commons#> 
PREFIX tmjourney:<https://w3id.org/transmodel/journeys#> 
PREFIX tmorg:<https://w3id.org/transmodel/organisations#> 
PREFIX xsd:<http://www.w3.org/2001/XMLSchema#> 
PREFIX dcterms:<http://purl.org/dc/terms/>
PREFIX geosparql:<http://www.opengis.net/doc/IS/geosparql/1.0#>

SELECT  ?idViaje ?idGrupoHorario ?primeraHora ?ultimaHora  ?frecuenciaMin ?frecuenciaMax ?frecuenciaProg where {
	?ruta a tmjourney:Route .
	?ruta dcterms:identifier "138a"^^xsd:string .
	?patronViaje tmjourney:on ?ruta .
    	?viaje tmjourney:madeUsing ?patronViaje .
   	 ?viaje dcterms:identifier ?idViaje .
    	?viaje tmjourney:composedOf ?grupoHorario .
   	 ?grupoHorario dcterms:identifier ?idGrupoHorario .
	?grupoHorario tmjourney:firstDepartureTime ?primeraHora .
	?grupoHorario tmjourney:lastDepartureTime ?ultimaHora .
    	?grupoHorario tmjourney:determinedBy ?frecuencia .
	?frecuencia tmjourney:minimumHeadwayInterval ?frecuenciaMin . 
    	?frecuencia tmjourney:maximumHeadwayInterval ?frecuenciaMax . 
	?frecuencia tmjourney:scheduledHeadwayInterval ?frecuenciaProg . 	       
}
```

## Consulta 3 Dame el tipo de día que corresponde al 29 de mayo de 2020 y la información sobre ese tipo de día.
Se utiliza el grafo http://vocab.ciudadesabiertas.es/grafo/transporte/autobus/datos-autobus-ejemplos
```
PREFIX schema:<http://schema.org/> 
PREFIX tmcommons:<https://w3id.org/transmodel/commons#> 
PREFIX tmjourney:<https://w3id.org/transmodel/journeys#> 
PREFIX tmorg:<https://w3id.org/transmodel/organisations#> 
PREFIX xsd:<http://www.w3.org/2001/XMLSchema#> 
PREFIX dcterms:<http://purl.org/dc/terms/>
PREFIX geosparql:<http://www.opengis.net/doc/IS/geosparql/1.0#>

select * where {
#SELECT ?nombre  ?horaMasTemprana WHERE {
        	?fecha a tmjourney:DayTypeAssignment .
?fecha tmjourney:date "2020-01-01"^^xsd:date .
       	?fecha tmjourney:specifying ?tipoDia .
        	?tipoDia a tmjourney:DayType .
        	?tipoDia schema:name ?nombre .
	?tipoDia tmjourney:earliestTime ?horaMasTemprana .
}
```

## Consulta 4. ¿Cuáles son las paradas más cercanas a esta localización, en un radio de 500 mts.
Se utiliza el grafo http://vocab.ciudadesabiertas.es/grafo/transporte/autobus/datos-autobus-extendido

Dada la ubicación en el Museo de América de Madrid (40.438352 -3.721677):
```
PREFIX schema: <http://schema.org/>
PREFIX esautob: <http://vocab.ciudadesabiertas.es/def/transporte/autobus#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX dcterms: <http://purl.org/dc/terms/>
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX geosparql:<http://www.opengis.net/doc/IS/geosparql/1.0#>

SELECT ?idParada ?nombreParada ?longParada ?latParada where {
	?parada a esautob:Parada .
	?parada dcterms:identifier ?idParada .
	OPTIONAL {?parada schema:name ?nombreParada} .
     ?parada geosparql:hasGeometry ?point .
     ?point geo:long ?longParada .
     ?point geo:lat ?latParada .
       FILTER (bif:haversine_deg_km(?longParada, ?latParada, "-3.721677"^^xsd:double, "40.438352"^^xsd:double) < 0.5)
}
```

## Consulta 5. Dame el orden de la parada 2512 dentro de la ruta 52-ida y las características de esa parada.
Se utiliza el grafo http://vocab.ciudadesabiertas.es/grafo/transporte/autobus/datos-autobus-extendido
```
PREFIX dcterms:<http://purl.org/dc/terms/> 
PREFIX esautob:<http://vocab.ciudadesabiertas.es/def/transporte/autobus#> 
PREFIX schema:<http://schema.org/> 
PREFIX tmcommons:<https://w3id.org/transmodel/commons#> 
PREFIX tmjourney:<https://w3id.org/transmodel/journeys#> 
PREFIX tmorg:<https://w3id.org/transmodel/organisations#> 
PREFIX xsd:<http://www.w3.org/2001/XMLSchema#> 
PREFIX dcterms:<http://purl.org/dc/terms/>
PREFIX geosparql:<http://www.opengis.net/doc/IS/geosparql/1.0#>
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>


SELECT  ?orden ?nombreParada ?wifi ?panel ?lat ?long  where {
	?ruta a tmjourney:Route .
	?ruta dcterms:identifier "52-ida"^^xsd:string .
	?ruta tmjourney:madeUpOf ?puntoRuta .
	?puntoRuta tmjourney:order ?orden . 
	?puntoRuta tmjourney:viewedAs ?punto .
	?punto tmcommons:functionalCentroidFor ?parada .
	?parada dcterms:identifier "2512"^^xsd:string .
	?parada schema:name ?nombreParada .
    	?parada esautob:wifi ?wifi .
    	?parada esautob:panelElectronico ?panel .
    	?parada geosparql:hasGeometry ?point .
    	?point geo:long ?long .
    	?point geo:lat ?lat
}
```

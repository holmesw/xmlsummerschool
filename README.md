# XML Summer School: Linked Data

## Epimorphics' Qlassroom:

<http://epi-training.epimorphics.net/qlassroom>

## Namespace Prefixes

    prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>
    prefix owl: <http://www.w3.org/2002/07/owl#>
    prefix xsd: <http://www.w3.org/2001/XMLSchema#>
    prefix geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
    prefix lu-c: <http://training.epimorphics.com/transport/london-underground/connection/>
    prefix lu-l: <http://training.epimorphics.com/transport/london-underground/line/>
    prefix lu-s: <http://training.epimorphics.com/transport/london-underground/station/>
    prefix metro: <http://training.epimorphics.com/transport/def/metro/>
    prefix qb: <http://purl.org/linked-data/cube#>
    prefix passengers: <http://training.epimorphics.com/transport/def/passenger-count/>
    prefix museum: <http://training.epimorphics.com/culture/london/museum/>
    prefix dbpedia: <http://dbpedia.org/ontology/>
    PREFIX transport: <http://training.epimorphics.com/transport/def/metro/>

## Exercises:

<http://training.epimorphics.com/course-materials/1-5-QueryingLinkedData.pdf>


### Find All the Underground Lines

    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    PREFIX transport: <http://training.epimorphics.com/transport/def/metro/>
    
    SELECT ?line WHERE {
    
      ?line rdf:type transport:UndergroundLine . 
    }


### Find All the Underground Stations

    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    PREFIX transport: <http://training.epimorphics.com/transport/def/metro/>
    
    SELECT ?station WHERE {
    
      ?station rdf:type transport:UndergroundStation . 
    }


### What if we wanted their names as well?

    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>
    PREFIX transport: <http://training.epimorphics.com/transport/def/metro/>
    
    SELECT ?station ?stationName WHERE {
    
      ?station rdf:type transport:UndergroundStation ; 
               rdfs:label ?stationName . 
    }


### Now I want to see what line the station is on

    SELECT ?stationName ?lineName WHERE {
      ?station rdf:type transport:UndergroundStation ;
               rdfs:label ?stationName ; 
               transport:incomingConnection/transport:onLine ?line .
      ?line rdfs:label ?lineName .
    }


### Eliminating Duplicates

    SELECT DISTINCT ?stationName ?lineName WHERE {
      ?station rdf:type transport:UndergroundStation ;
               rdfs:label ?stationName ; 
               transport:incomingConnection/transport:onLine ?line .
      ?line rdfs:label ?lineName .
    }


### I only want stations on the Bakerloo line

    SELECT DISTINCT ?stationName WHERE {
      ?station rdf:type transport:UndergroundStation ;
               rdfs:label ?stationName ;
               transport:incomingConnection/transport:onLine lu-l:bakerloo_line .
    }


### List the Bakerloo line stations in alphabetical order

    SELECT DISTINCT ?stationName WHERE { 
      ?station rdf:type transport:UndergroundStation ;
               rdfs:label ?stationName ;
               transport:incomingConnection/transport:onLine lu-l:bakerloo_line ;
               geo:lat ?lat .  
    }
    ORDER BY ASC(?stationName)


### List the Bakerloo line stations from north to south

    SELECT DISTINCT ?stationName WHERE { 
      ?station rdf:type transport:UndergroundStation ;
               rdfs:label ?stationName ;
               transport:incomingConnection/transport:onLine lu-l:bakerloo_line ;
               geo:lat ?lat .  
    }
    ORDER BY DESC(?lat)


### Count the stations

    SELECT (COUNT( ?station) AS ?count) WHERE {
    ?station rdf:type transport:UndergroundStation }


### Count the tations on each line

    SELECT ?lineName (COUNT(DISTINCT ?station) AS ?numStations) WHERE {
      ?station rdf:type transport:UndergroundStation ;
               rdfs:label ?stationName;
               transport:incomingConnection/transport:onLine ?line .
               ?line rdfs:label ?lineName .
      }
      GROUP BY ?lineName order by ?numStations


### Count all the stations on the Bakerloo line south of Oxford Circus

    SELECT (COUNT(DISTINCT ?station) AS ?numStations) WHERE {
      ?station rdf:type transport:UndergroundStation ;
               rdfs:label ?stationName ;
               transport:incomingConnection/transport:onLine lu-l:bakerloo_line ;
               geo:lat ?lat .
      lu-s:oxford_circus geo:lat ?oc_lat .
      FILTER ( ?lat < ?oc_lat)
    }


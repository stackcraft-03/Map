https://github.com/dsivov/investigator?utm_source=chatgpt.com
https://github.com/teenatom17/NewsTrace?utm_source=chatgpt.com
https://github.com/ContextNews/news-pipeline?utm_source=chatgpt.com
https://github.com/dukeblue1994-glitch/chronicle?utm_source=chatgpt.com
https://github.com/miharcan/event_extraction?utm_source=chatgpt.com

It’s the way to calculate the distance inside the database, as part of a SPARQL query, so the number arrives together with each location. You’d use it when you want GraphDB to do the work instead of the browser.

What it’s good for
Distance as a query result. The distance becomes a column next to each location, with no distance calculation in React.
Filtering by distance in the query. For example, only locations within 50 km of the border.
Sorting in the query. Rank the locations by distance with ORDER BY and return only the top few.
Not shipping the border to the browser. The border WKT can be several MB. If GraphDB does the distance, the browser never needs it for the number.
Example
sparql
PREFIX geo:  <http://www.opengis.net/ont/geosparql#>
PREFIX geof: <http://www.opengis.net/def/function/geosparql/>
PREFIX uom:  <http://www.opengis.net/def/uom/OGC/1.0/>

SELECT ?loc ?name (geof:distance(?locWkt, ?borderWkt, uom:metre) AS ?meters)
WHERE {
  ?loc a :Location ; :name ?name ; geo:hasGeometry/geo:asWKT ?locWkt .
  :Pakistan geo:hasGeometry/geo:asWKT ?borderWkt .
}
ORDER BY ?meters

The names :Location, :name and :Pakistan are placeholders for your own ontology.


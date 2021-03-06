# STAC API Examples

A typical OAFeat will have multiple collections, and each will just offer simple search for its particular collection at `GET /collections/{collectionId}/items`.
Due to the limited parameter support in OGC API - Features, it is recommended to use the STAC API endpoint `POST /stac/search` for advanced queries.
The filtering is made to be compatible between STAC API and OGC API - Features whenever feasible, and the two specs seek to share the general query and filtering patterns.
The key difference is that the STAC API search endpoints will do cross collection search.

Implementations may **optionally** provide support for the full superset of STAC API query parameters to the `/collections/{collectionId}/items` endpoint,
where the collection ID in the path is equivalent to providing that single value in the `collections` query parameter in STAC API.

## OGC API - Features (OAFeat)

Note that the OAFeat endpoints _only_ supports HTTP GET. HTTP POST requests are not supported.

Request all the data in `mycollection` that is in New Zealand:

```
GET /collections/mycollection/items?bbox=160.6,-55.95,-170,-25.89
```

Request 100 results in `mycollection` from New Zealand:

```
GET /collections/mycollection/items?bbox=160.6,-55.95,-170,-25.89&limit=100
```

Request all the data in `mycollection` that is in New Zealand from January 1st, 2019:

```
GET /collections/mycollection/items?bbox=160.6,-55.95,-170,-25.89&datetime=2019-01-01T00:00:00Z/2019-01-01T23:59:59ZZ
```

Request 10 results from the data in `mycollection` from between January 1st (inclusive) and April 1st, 2019 (exclusive):

```
GET /collections/mycollection/items?datetime=2019-01-01T00:00:00Z/2019-03-31T23:59:59Z&limit=10
```

## STAC API

A STAC API supports both GET and POST requests. It is best to use POST when using the intersects query for two reasons:

1. the size limit for a GET request is less than that of POST, so if a complex geometry is used GET may fail.
2. The parameters for a GET request must be escaped properly, making it more difficult to construct when using JSON parameters (such as intersect)

**STAC API extensions** allow for more sophisticated searching, such as the ability to search by geometries and searching on specific Item properties.

Use the *[Query](extensions/query/README.md)* extension to search for any data falling within a specific geometry collected between Jan 1st and May 1st, 2019:

```
POST /stac/search
```

Body:
```json
{
    "limit": 100,
    "intersects": {
        "type": "Polygon",
        "coordinates": [[
            [-77.08248138427734, 38.788612962793636], [-77.01896667480469, 38.788612962793636],
            [-77.01896667480469, 38.835161408189364], [-77.08248138427734, 38.835161408189364],
            [-77.08248138427734, 38.788612962793636]
        ]]
    },
    "time": "2019-01-01/2019-05-01"
}
```

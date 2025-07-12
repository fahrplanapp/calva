Calva Open Data API Documentation
================================

Version: v2.1  
Base URL: https://ressources.data.sncf.com/api/explore/v2.1/catalog

Overview
--------
This API provides programmatic access to Calva's open datasets. It allows you to query, filter, and export datasets via RESTful endpoints.

Authentication
--------------
No authentication is required for public access.

Main Endpoints
==============

GET /datasets/{dataset_id}/records
-----------------------------------
Query dataset records.

**Path Parameters:**

- ``dataset_id`` *(string, required)*: Identifier of the dataset.

**Query Parameters (optional):**

- ``select``: Choose specific fields or compute expressions.
- ``where``: Filter using Opendatasoft Query Language (ODSQL).
- ``group_by``: Group results by a specific field or expression.
- ``order_by``: Sort results (e.g., `order_by=name asc`).
- ``limit`` *(integer)*: Max number of items to return (max 100 or 20,000 depending on grouping).
- ``offset`` *(integer)*: Index of the first result (for pagination).
- ``refine``: Filter using facet values (e.g., `refine=city:Paris`).
- ``exclude``: Exclude specific facet values.
- ``lang``: Language (default: "fr").
- ``timezone``: Timezone for datetime fields (e.g., "UTC").
- ``include_links`` *(boolean)*: Adds HATEOAS links if true.
- ``include_app_metas`` *(boolean)*: Includes application metadata if true.

**Example Response:**

.. code-block:: json

   {
     "total_count": 137611,
     "results": [
       {
         "name": "Saint-Leu",
         "coordinates": {
           "lat": 46.7306,
           "lon": 4.50083
         },
         "population": 29278
       }
     ]
   }

GET /datasets/{dataset_id}/records/{record_id}
-----------------------------------------------
Fetch a single record from a dataset.

**Path Parameters:**

- ``dataset_id`` *(string, required)*
- ``record_id`` *(string, required)*

**Query Parameters (optional):** Same as in `/records`.

GET /datasets/{dataset_id}/exports
-----------------------------------
List available export formats for a dataset.

**Path Parameters:**

- ``dataset_id`` *(string, required)*

GET /datasets/{dataset_id}/exports/{format}
-------------------------------------------
Export a dataset in the specified format.

**Supported formats:**

- ``csv``, ``parquet``, ``gpx``, etc.

**Path Parameters:**

- ``dataset_id`` *(string, required)*
- ``format`` *(string, required)*

**Query Parameters:**

- Same as in `/records`, plus:
- ``use_labels`` *(boolean)*: Output field labels instead of field names.
- ``compressed`` *(boolean)*: Export as compressed (e.g., `.csv.gz`).
- ``epsg`` *(integer)*: EPSG projection code for geometric exports.

**Response:** Downloadable file in specified format.

GET /datasets/{dataset_id}/facets
----------------------------------
List values for each facet (for filtering/navigation).

**Query Parameters:**

- Same as in `/records`, plus:
- ``facet``: Field or facet expression (e.g., `facet=name` or `facet=facet(name="city", sort="-count")`).

**Example Response:**

.. code-block:: json

   {
     "facets": [
       {
         "name": "timezone",
         "facets": [
           {
             "name": "Europe",
             "count": 68888
           }
         ]
       }
     ]
   }

GET /datasets/{dataset_id}/attachments
---------------------------------------
List file attachments related to the dataset.

**Path Parameters:**

- ``dataset_id`` *(string, required)*

GET /datasets/{dataset_id}/exports/csv
---------------------------------------
Export a dataset in CSV format with extra CSV-specific parameters.

**Additional CSV Parameters:**

- ``delimit``: Field delimiter (e.g., `;`).
- ``list_separator``: Separator for multivalue fields (e.g., `,`).
- ``quote_all`` *(boolean)*: Quote all fields if true.
- ``with_bom`` *(boolean)*: Add BOM for Excel compatibility (default `true` in v2.1).

GET /datasets/{dataset_id}/exports/parquet
-------------------------------------------
Export a dataset in Parquet format.

**Additional Parquet Parameter:**

- ``parquet_compression``: Compression type (e.g., `snappy`).

GET /datasets/{dataset_id}/exports/gpx
---------------------------------------
Export a dataset in GPX format (for geographic data).

**Additional GPX Parameters:**

- ``name_field``: Field to use as GPX name.
- ``description_field_list``: Fields used for GPX description.
- ``use_extension`` *(boolean)*: Use `<extension>` tag (default: true in v2.1).

Response Codes
==============

- **200 OK**: Successful request.
- **400 Bad Request**: Invalid ODSQL query or parameters.
- **401 Unauthorized**: Authentication required.
- **429 Too Many Requests**: Rate limit exceeded.
- **500 Internal Server Error**: Server error.

**Example Error Response:**

.. code-block:: json

   {
     "message": "ODSQL query is malformed: invalid_function()",
     "error_code": "ODSQLError"
   }

Additional References
=====================

- API Console: https://ressources.data.sncf.com/api/explore/v2.1/console
- ODSQL Language Reference: https://docs.opendatasoft.com/en/data_exploration/04_analyzing_data/03_using_query_language.html

How to Use Calva
================

Calva is a powerful, REPL-powered, and beginner-friendly development environment for Clojure and ClojureScript, built as a Visual Studio Code extension.

Prerequisites
-------------
Before getting started, make sure you have the following tools installed:

1. **Visual Studio Code**  
   Download: https://code.visualstudio.com/

2. **Java Development Kit (JDK)** – Version 11 or higher recommended  
   Download: https://adoptium.net/

3. **Leiningen or Clojure CLI tools**  
   - Leiningen: https://leiningen.org/  
   - Clojure CLI: https://clojure.org/guides/getting_started

4. **Node.js** (optional, for ClojureScript development)  
   Download: https://nodejs.org/

Step-by-Step Setup
------------------

1. **Install Calva**
   - Open Visual Studio Code
   - Go to Extensions panel (Ctrl+Shift+X)
   - Search for *Calva*
   - Click **Install**

2. **Create a Clojure Project**

   Option A: Leiningen  
   .. code-block:: bash

      lein new app my-first-app  
      cd my-first-app

   Option B: Clojure CLI  
   .. code-block:: bash

      clj -Ttools new :template app :name my-first-app  
      cd my-first-app

3. **Open the Project in VS Code**
   - Go to File > Open Folder...
   - Select your project folder

4. **Start a REPL**
   - Press Ctrl+Shift+P (or Cmd+Shift+P on macOS)
   - Choose: *Calva: Start a Project REPL and Connect*
   - Select REPL type (Leiningen or deps.edn)

Trying Calva for the First Time
-------------------------------

1. Open `src/my_first_app/core.clj`  
2. Replace its contents with:

.. code-block:: clojure

   (ns my-first-app.core)

   (defn -main []
     (println "Hello, Calva!"))

3. Place your cursor on the `(println ...)` expression  
4. Press Ctrl+Enter (or Cmd+Enter on macOS) to evaluate in REPL

Key Features
------------

- Inline evaluation and REPL output
- Structural editing with Paredit
- Code formatting (Alt+Shift+F)
- Syntax highlighting and rainbow brackets
- Code navigation and test running
- Linting and refactoring tools

Community & Support
-------------------

- Join #calva on Clojurians Slack: https://clojurians.net/
- For beginners: join #beginners channel
- Official docs: https://calva.io

Supporting Calva
----------------

Calva is free and open-source. You can help by:

- Starring the GitHub repo: https://github.com/BetterThanTomorrow/calva
- Becoming a sponsor: https://github.com/sponsors/BetterThanTomorrow

Conclusion
----------

You now have a full Clojure development environment using Calva in VS Code. With REPL-driven workflows and modern tooling, Calva helps you write expressive and maintainable Clojure code.

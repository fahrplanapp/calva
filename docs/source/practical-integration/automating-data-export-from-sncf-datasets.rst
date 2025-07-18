Automating Data Export from SNCF Datasets Using Clojure and Calva
==================================================================

Overview
--------
In this guide, you'll learn how to automate data exports from the SNCF Open Data API using Clojure and Calva. You'll work with the `/exports/{format}` endpoint to download datasets in formats like CSV, Parquet, and GPX, and save them locally for further analysis or GIS use.

What You’ll Learn:
- How to call the `/exports/{format}` endpoint
- Use export-specific query parameters (`compressed`, `epsg`, `with_bom`, etc.)
- Save exported files using Clojure I/O tools
- Understand when to use each export format for data and GIS applications

Prerequisites
-------------
Make sure you have the following installed:

- `Visual Studio Code` and the Calva extension
- `Java Development Kit (JDK)` version 11 or later
- `Leiningen` or `Clojure CLI`
- Internet access for fetching remote data

Add Dependencies
----------------
Add these libraries to your project:

**Leiningen (`project.clj`)**

.. code-block:: clojure

   :dependencies [[org.clojure/clojure "1.11.1"]
                  [clj-http "3.12.3"]]

**Clojure CLI (`deps.edn`)**

.. code-block:: clojure

   :deps {org.clojure/clojure {:mvn/version "1.11.1"}
          clj-http {:mvn/version "3.12.3"}}

Exporting Data via API
----------------------
To export a dataset, use the following API structure:

.. code-block:: text

   GET /datasets/{dataset_id}/exports/{format}

**Example: Export CSV with compression**

Endpoint:

.. code-block:: text

   https://ressources.data.sncf.com/api/explore/v2.1/catalog/datasets/sncf-gares-et-arrets/exports/csv

With query parameters:

- `compressed=true`
- `with_bom=true`
- `limit=1000`

Write the client function in `core.clj`:

.. code-block:: clojure

   (ns exporter.core
     (:require [clj-http.client :as client]
               [clojure.java.io :as io]))

   (def export-url
     "https://ressources.data.sncf.com/api/explore/v2.1/catalog/datasets/sncf-gares-et-arrets/exports/csv")

   (defn download-csv []
     (let [response (client/get export-url
                                {:query-params {"compressed" "true"
                                                "with_bom" "true"
                                                "limit" "1000"}
                                 :as :byte-array})]
       (with-open [out (io/output-stream "stations.csv.gz")]
         (.write out (:body response)))))

   (defn -main []
     (download-csv))

Running the Exporter
---------------------
1. Open a REPL using Calva (`Ctrl+Shift+P` → "Start Project REPL")
2. Evaluate `(-main)` or call `(download-csv)` from the REPL
3. A file named `stations.csv.gz` will be saved to your project directory

Exporting in Parquet or GPX
---------------------------
Change the `format` in the URL to `parquet` or `gpx`, and modify parameters:

**Parquet Example**

.. code-block:: clojure

   (def parquet-url
     "https://ressources.data.sncf.com/api/explore/v2.1/catalog/datasets/sncf-gares-et-arrets/exports/parquet")

   (defn download-parquet []
     (let [response (client/get parquet-url
                                {:query-params {"parquet_compression" "snappy"}
                                 :as :byte-array})]
       (with-open [out (io/output-stream "stations.parquet")]
         (.write out (:body response)))))

**GPX Example**

.. code-block:: clojure

   (def gpx-url
     "https://ressources.data.sncf.com/api/explore/v2.1/catalog/datasets/sncf-gares-et-arrets/exports/gpx")

   (defn download-gpx []
     (let [response (client/get gpx-url
                                {:query-params {"name_field" "name"
                                                "description_field_list" "city,population"
                                                "use_extension" "true"}
                                 :as :byte-array})]
       (with-open [out (io/output-stream "stations.gpx")]
         (.write out (:body response)))))

Choosing the Right Format
--------------------------
- **CSV**: Best for spreadsheets, lightweight analytics, and data pipelines
  - Use `with_bom=true` for Excel compatibility
  - Compress large exports with `compressed=true`

- **Parquet**: Ideal for big data workflows (e.g., Spark, Hive)
  - Use `parquet_compression=snappy` or `gzip` for space efficiency
  - Retains data types and schema

- **GPX**: Suitable for geographic apps and GPS devices
  - Set `name_field` and `description_field_list` for metadata
  - Use `epsg` to match coordinate systems

Error Handling Tips
-------------------
The API may return errors like:

- `400 Bad Request`: Malformed query or unsupported parameters
- `429 Too Many Requests`: Rate limit hit — retry later
- `500 Internal Server Error`: Server-side issue

Always check `(:status response)` and log failures gracefully.

Conclusion
----------
You now know how to automate dataset exports from SNCF's Open Data API using Clojure and Calva. This enables powerful workflows for data transformation, GIS analysis, and pipeline automation. With a few lines of Clojure, you can fetch structured data and integrate it into your applications or tools.

Next Steps:
- Add CLI arguments or config files for dynamic control
- Schedule exports with `cron` or a Clojure task runner
- Convert this into a reusable library or script

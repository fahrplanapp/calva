Building Your First API-Driven Clojure App with Calva
======================================================

Overview
--------
In this tutorial, you will learn how to build a simple Clojure application that interacts with the Calva Open Data API (SNCF) using the Calva extension in Visual Studio Code. You'll make HTTP requests, parse JSON responses, and display real-time station data.

By the end, you’ll understand how to:
- Set up a Clojure project with Calva
- Fetch data using the `/records` endpoint
- Parse JSON responses with the `cheshire` library
- Work with real-time station data

Prerequisites
-------------
Make sure the following tools are installed:

- `Visual Studio Code`: https://code.visualstudio.com/
- `Calva` Extension for VS Code: https://marketplace.visualstudio.com/items?itemName=betterthantomorrow.calva
- `Java Development Kit (JDK)` (version 11+): https://adoptium.net/
- `Leiningen` or `Clojure CLI`: https://clojure.org/guides/getting_started
- `Node.js` (optional, for ClojureScript): https://nodejs.org/

Project Setup
-------------
Create a new Clojure project using your preferred tool:

**Option A: Leiningen**

.. code-block:: bash

   lein new app api-crawler
   cd api-crawler

**Option B: Clojure CLI**

.. code-block:: bash

   clj -Ttools new :template app :name api-crawler
   cd api-crawler

Open the project folder in VS Code:

.. code-block:: text

   File > Open Folder... > select `api-crawler`

Install Calva from the Extensions Panel (Ctrl+Shift+X), then:

.. code-block:: text

   Ctrl+Shift+P → "Calva: Start a Project REPL and Connect" → Select REPL type (Leiningen or deps.edn)

Adding Dependencies
-------------------
Edit your `project.clj` or `deps.edn` to include:

**Leiningen (`project.clj`)**

.. code-block:: clojure

   :dependencies [[org.clojure/clojure "1.11.1"]
                  [clj-http "3.12.3"]
                  [cheshire "5.11.0"]]

**Clojure CLI (`deps.edn`)**

.. code-block:: clojure

   :deps {org.clojure/clojure {:mvn/version "1.11.1"}
          clj-http {:mvn/version "3.12.3"}
          cheshire {:mvn/version "5.11.0"}}

Writing the API Client
----------------------
Open `src/api_crawler/core.clj` and replace the content with:

.. code-block:: clojure

   (ns api-crawler.core
     (:require [clj-http.client :as client]
               [cheshire.core :as json]))

   (def api-url
     "https://ressources.data.sncf.com/api/explore/v2.1/catalog/datasets/sncf-gares-et-arrets/records")

   (defn fetch-stations []
     (let [response (client/get api-url {:as :json})
           results  (get-in response [:body "results"])]
       (doseq [station results]
         (println "Station:" (:name station))
         (println "Coordinates:" (:coordinates station))
         (println "Population:" (:population station))
         (println "-------------------------"))))

   (defn -main []
     (fetch-stations))

Running the Application
-----------------------
1. Open the REPL: Press `Ctrl+Shift+P` → "Calva: Start a Project REPL and Connect"
2. Evaluate `(fetch-stations)` directly or run `(-main)` to test your API client.
3. You should see a list of station names, coordinates, and population data printed in the REPL.

Sample Output:

.. code-block:: text

   Station: Saint-Leu
   Coordinates: {:lat 46.7306, :lon 4.50083}
   Population: 29278
   -------------------------

Tips and Enhancements
---------------------
- Use `:query-params` to add filters or sort results:

.. code-block:: clojure

   {:query-params {"where" "city='Paris'" "limit" "5"}}

- Save the response to a file using `spit`:

.. code-block:: clojure

   (spit "stations.json" (json/generate-string results {:pretty true}))

- Convert this to a web app with Ring or display it in a terminal UI.

Conclusion
----------
You've built a basic Clojure application using Calva that interacts with SNCF's Open Data API. You’ve learned to make API calls, parse JSON with `cheshire`, and use the REPL to iterate on live code. This is a great foundation for more advanced integrations like dashboards, analytics, or GIS tools.

Next Steps:
- Explore filtering and exporting features of the API.
- Add unit tests or move logic into reusable namespaces.
- Build a simple front-end or web interface.

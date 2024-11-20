### **Datomic Tutorial: Getting Started**

#### **1. Introduction to Datomic**
   - **What is Datomic?** 
      - Datomic is a distributed database designed to scale horizontally and support immutable, time-based data modeling.
      - Key features: immutability, history, scalability, and Datalog queries.
   - **When to Use Datomic**
      - Datomic is ideal for systems where data history, immutability, and scalability are important, such as finance, healthcare, and auditing systems.

#### **2. Installation and Setup**

   - **Prerequisites**
      - Java (version 8+)
      - [Leiningen](https://codeberg.org/leiningen/leiningen) (for Clojure project management)
   - **Downloading Datomic**
      - Go to [Datomic’s website](https://www.datomic.com/) and download the free version, Datomic Free.
   - **Starting Datomic**
      - Unzip the downloaded file and start the Datomic transactor by navigating to the directory and running:
        ```bash
        bin/transactor config/samples/free-transactor-template.properties
        ```
   - **Setting Up a REPL (Read-Eval-Print Loop)**
      - Start a Clojure REPL using Leiningen:
        ```bash
        lein repl
        ```
   - **Connecting to Datomic**
      - Connect to the Datomic transactor in the REPL:
        ```clojure
        (require '[datomic.api :as d])
        (def conn (d/connect "datomic:free://localhost:4334/<database-name>"))
        ```

#### **3. Datomic Concepts Overview**
   - **Entities**: The primary units of data in Datomic, representing a unique instance (e.g., a person or product).
   - **Attributes**: Characteristics or properties of entities (e.g., `:user/name`, `:user/age`).
   - **Datoms**: Fundamental facts in Datomic, including entity, attribute, value, and transaction information.
   - **Schemas**: Define the structure of data, specifying allowed attributes and their constraints.
   - **Transactions**: Used to add, update, or delete data.

#### **4. Creating a Basic Database**

   - **Define a Schema**
      - In Datomic, we start by defining a schema to specify the structure of our data. Here’s an example of a schema for a `user` entity:
        ```clojure
        (def user-schema
          [{:db/ident :user/id
            :db/valueType :db.type/long
            :db/unique :db.unique/identity
            :db/cardinality :db.cardinality/one}
           {:db/ident :user/name
            :db/valueType :db.type/string
            :db/cardinality :db.cardinality/one}
           {:db/ident :user/age
            :db/valueType :db.type/long
            :db/cardinality :db.cardinality/one}])

        ;; Transact the schema
        (d/transact conn {:tx-data user-schema})
        ```

   - **Adding Data**
      - After defining the schema, we can add data to our database. Here’s an example of adding a `user` entity:
        ```clojure
        (d/transact conn {:tx-data [{:user/id 1 :user/name "Alice" :user/age 30}]})
        ```
   - **Querying Data**
      - Datomic uses Datalog, a declarative query language. Here’s a simple query to retrieve a user by `:user/name`:
        ```clojure
        (def query
          '[:find ?id ?name ?age
            :where
            [?e :user/name ?name]
            [?e :user/id ?id]
            [?e :user/age ?age]])

        (d/q query (d/db conn))
        ```

#### **5. Exploring Basic Queries**

   - **Find All Users**
      ```clojure
      (def all-users-query
        '[:find ?id ?name ?age
          :where
          [?e :user/id ?id]
          [?e :user/name ?name]
          [?e :user/age ?age]])

      (d/q all-users-query (d/db conn))
      ```

   - **Query with Filtering**
      - For example, find users over a certain age:
        ```clojure
        (def age-query
          '[:find ?name ?age
            :in $ ?min-age
            :where
            [?e :user/age ?age]
            [(> ?age ?min-age)]
            [?e :user/name ?name]])

        ;; Execute with minimum age of 25
        (d/q age-query (d/db conn) 25)
        ```

#### **6. Transaction History and Time Travel**

   - **History Query**
      - To view the history of changes for an entity:
        ```clojure
        (d/q '[:find ?name ?age ?tx
               :where
               [?e :user/name ?name ?tx true]
               [?e :user/age ?age ?tx true]]
             (d/history (d/db conn)))
        ```

   - **As Of Queries**
      - Querying the database as it was at a specific transaction:
        ```clojure
        (def as-of-db (d/as-of (d/db conn) <transaction-id>))
        ;; Run a query on the as-of database
        (d/q all-users-query as-of-db)
        ```

#### **7. Conclusion and Next Steps**

   - **Key Points Recap**
      - Datomic provides a unique approach to data modeling with immutability and time-based features.
   - **Next Steps**
      - Explore advanced queries, data partitioning, and indexing.
      - Look into more complex schema modeling, including one-to-many relationships and entity references.

---

This tutorial provides a structured introduction to Datomic, highlighting its core concepts and practical application through basic examples. Once comfortable, you can explore more complex schema designs, relationships, and Datomic's advanced querying capabilities.
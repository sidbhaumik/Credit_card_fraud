graph TD
    TX["Credit Card Transactions"]
    PubSub["Google Pub/Sub Ingestion Buffer"]
    Dataflow["Apache Beam / Dataflow Stream Processing"]
    
    Val["Validation"]
    Tok["Tokenization (Salted SHA-256)"]
    DLQ["Dead Letter Queue (Cold Path Bypass)"]

    BQ["BigQuery Silver Layer"]
    Engine["Fraud Detection Engine (SQL Rules)"]
    Cases["Fraud Cases Queue"]

    Email["Email Alert"]
    SNow["ServiceNow Incident"]
    Console["Investigation Console"]
    Analysts["Fraud Analysts Team"]

    TX --> PubSub
    PubSub --> Dataflow
    
    Dataflow --> Val
    Dataflow --> Tok
    Dataflow --> DLQ

    Val --> BQ
    Tok --> BQ
    BQ --> Engine
    Engine --> Cases
    
    Cases --> Email
    Cases --> SNow
    
    Email --> Console
    SNow --> Console
    Console --> Analysts

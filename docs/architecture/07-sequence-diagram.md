graph TD
    %% Define Styles & Colors
    classDef source fill:#ECEFF1,stroke:#37474F,stroke-width:2px,stroke-dasharray: 5 5,color:#263238;
    classDef gcp fill:#E8F0FE,stroke:#1A73E8,stroke-width:2px,color:#1A73E8,font-weight:bold;
    classDef engine fill:#FFF0E6,stroke:#F4511E,stroke-width:2px,color:#BF360C,font-weight:bold;
    classDef alert fill:#FCE8E6,stroke:#C5221F,stroke-width:2px,color:#A50E0E;
    classDef ops fill:#E6F4EA,stroke:#137333,stroke-width:2px,color:#137333;

    %% Nodes Definitions with safe string wrapping
    TX["💳 Credit Card Transactions"]:::source
    PubSub["☁️ Google Pub/Sub (Ingestion Buffer)"]:::gcp
    Dataflow["🌊 Apache Beam / Dataflow (Stream Processing)"]:::gcp
    
    Val["✅ Validation"]:::gcp
    Tok["🔒 Tokenization (Salted SHA-256)"]:::gcp
    DLQ["⚠️ Dead Letter Queue (Cold Path Bypass)"]:::alert

    BQ["🗄️ BigQuery Silver Layer"]:::gcp
    Engine["🧠 Fraud Detection Engine (SQL Rules)"]:::engine
    Cases["💼 Fraud Cases Queue"]:::engine

    Email["📧 Email Alert"]:::alert
    SNow["🛠️ ServiceNow Incident"]:::alert
    Console["🖥️ Investigation Console"]:::ops
    Analysts["👥 Fraud Analysts Team"]:::ops

    %% Diagram Flow Relationships
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

    %% Legend Block
    subgraph Scope ["Platform Scope"]
        direction LR
        LEGEND["🚀 Near Real-Time Fraud Detection and Investigation Platform Built Natively on GCP"]
    end

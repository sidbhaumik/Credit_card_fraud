# Credit_card_fraud

graph TD
    %% Define Styles & Colors
    classDef source fill:#ECEFF1,stroke:#37474F,stroke-width:2px,stroke-dasharray: 5 5,color:#263238;
    classDef gcp fill:#E8F0FE,stroke:#1A73E8,stroke-width:2px,color:#1A73E8,font-weight:bold;
    classDef engine fill:#FFF0E6,stroke:#F4511E,stroke-width:2px,color:#BF360C,font-weight:bold;
    classDef alert fill:#FCE8E6,stroke:#C5221F,stroke-width:2px,color:#A50E0E;
    classDef ops fill:#E6F4EA,stroke:#137333,stroke-width:2px,color:#137333;

    %% Nodes Definitions
    TX[💳 Credit Card Transactions]:::source
    PubSub[☁️ Google Pub/Sub<br/><i>Ingestion Buffer</i>]:::gcp
    Dataflow[🌊 Apache Beam / Dataflow<br/><i>Stream Processing</i>]:::gcp
    
    Val[✅ Validation]:::gcp
    Tok[🔒 Tokenization<br/><i>Salted SHA-256</i>]:::gcp
    DLQ[⚠️ Dead Letter Queue<br/><i>Cold Path Bypass</i>]:::alert

    BQ[🗄️ BigQuery Silver Layer<br/><i>Optimized Storage</i>]:::gcp
    Engine[🧠 Fraud Detection Engine<br/><i>Scheduled SQL Rules</i>]:::engine
    Cases[💼 Fraud Cases Queue]:::engine

    Email[📧 Email Alert]:::alert
    SNow[🛠️ ServiceNow Incident]:::alert
    Console[🖥️ Investigation Console]:::ops
    Analysts[👥 Fraud Analysts Team]:::ops

    %% Diagram Flow Relationships
    TX --> PubSub
    PubSub --> Dataflow
    
    Dataflow --> Val
    Dataflow --> Tok
    Dataflow --> DLQ

    Val & Tok --> BQ
    Engine --> Cases
    BQ --> Engine
    
    Cases --> Email
    Cases --> SNow
    
    Email --> Console
    SNow --> Console
    Console --> Analysts

    %% Configuration Click/Hyperlinks text layout
    subgraph Legend & Core Framework Scope
        direction LR
        🚀[Near Real-Time Fraud Detection and Investigation Platform Built Natively on GCP]
    end

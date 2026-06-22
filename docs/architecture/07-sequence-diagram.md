Transaction
     |
     v
Pub/Sub
     |
     v
Dataflow
     |
     +--> Validation
     |
     +--> Tokenization
     |
     +--> Fraud Rules
     |
     +--> Fraud Case
               |
               +--> BigQuery
               |
               +--> Email
               |
               +--> ServiceNow
               |
               +--> Investigation Console

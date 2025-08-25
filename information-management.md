# Information Management

The technical specification defines the what and how of logging authorization decisions. This section addresses the critical governance aspects of how much data is logged, who can access it, and for how long it is retained. It provides a framework for managing the data lifecycle within the Authorization Decision Log to ensure it is purposeful, compliant, and secure.

Each organization is responsible for its own Authorization Decision Log. There is no central log although logs of several organisations can be aggregated if desired.

## Defining Information Requirements

Before implementing a logging solution, it is essential to define the specific information needs it must satisfy. The intended use of the log directly determines what information must be captured.

Key considerations include:

* **Purpose Limitation**: Clearly define the objectives of the log. Is it for internal auditing, providing accountability to citizens, analyzing security patterns, or supplying evidence to supervisory authorities? You should not record more information than is necessary for these defined purposes.  
* **Stakeholder Needs**: Identify all stakeholders and their information requirements. This may include internal auditors, legal departments, security officers, and external regulators or supervisory bodies.  
* **Data Minimization**: A core principle is to avoid storing unnecessary information, especially sensitive data. The goal is to make the log precise, compact, and manageable. Instead of duplicating large amounts of data (e.g., all user attributes from a source system like BRP), prefer storing only te data used (e.g., the birthdate) or even just a reference (e.g., the BSN) that allows the state at the time of the decision to be reconstructed.

## Data Verifiability and Level of Detail

The ability to provide accountability depends on the log's level of detail. A balance must be struck between capturing enough information to accurately replay historical decisions and practical challenges like data duplication and scalability. The appropriate level of detail depends on the organization's specific context and legal requirements, as the highest level is not always necessary.

To ensure historical accuracy while minimizing data storage, referencing external information (e.g., via a timestamp or version number) is preferred over storing full copies. This approach keeps logs lean but is contingent on the source system's ability to provide versioned historical data.

For full replayability, the log must also identify the exact version and configuration of the policy engine that evaluated the decision. However, providing reliable versioning for the engine may not always be feasible depending on the infrastructure.

### Definitions of Levels

We have identified the following four levels of detail, in order of least to most detail. Each level builds on the information from the previous level.

* **Request/Response**: Only the request for authorization and the response containing the decision are logged. To provide accountability for a historical decision, all environmental variables that affected the decision must be recreated from the request/response information, such as request identifiers and timestamps. This may often be impractical or impossible.  
* **Policy version**: In addition to the request and response, the exact version of the policies that were used to evaluate the request can be programmatically retrieved.  
* **Information source**: All information used in the evaluation can be programmatically retrieved. This allows full replayability, assuming the engine (PDP) behaves identically or can be manually recreated in the correct state, which is generally achievable.  
* **Full environment**: In addition to all information used in the evaluation, the environment and configuration of the engine that evaluates the decision can also be accurately recreated. This provides full, guaranteed replayability and maximum accountability.

## Legal and Regulatory Compliance

The storage and management of log data are also subject to various laws and legal frameworks.

* **Applicable Legislation**: Analyze all relevant laws and regulations (e.g., GDPR/AVG, archival - and domain specific laws) to determine requirements and restrictions on storing decision data. This analysis will inform what should and should be logged.  
* **Retention Periods**: Define a clear retention policy for log records. This period should be based on legal obligations and the defined purposes of the log. Data should not be kept longer than is necessary. Data retention may vary within a single log as well.
* **Accountability to Authorities**: Determine the information requirements of supervisory authorities ("toezichthouders"). The log must be structured to provide them with the necessary data for their oversight role in a timely and effective manner.

## Access and Usage

Clear rules must be established for who can access the log and under what conditions.

* **Access Control**: Define roles and permissions for accessing the log data itself. Access should be granted on a need-to-know basis.  
* **Data Aggregation**: For analytical purposes, such as identifying patterns to improve system resilience, consider providing aggregated or anonymized data. This can provide valuable insights while mitigating the privacy risks associated with granting access to raw log data.  
* **Responding to Incidents**: The log is a critical tool for forensic analysis after a security incident or data breach. It can help determine what actions were permitted at a specific time and on what basis, even if that permission was technically correct but improper in hindsight.
* **Observability in Data Spaces**: The log can provide a structured method for data providers to evaluate and inspect data usage by consumers. This allows the standard to be used to provide so called "Observability services" for data spaces.
# Information Management

This section addresses the critical governance aspects of how much data is logged, who can access it, and for how long it is retained. It provides a framework for managing the data lifecycle within the Authorization Decision Log to ensure it is purposeful, compliant, and secure.

Each organization is responsible for its own Authorization Decision Log. There is no central log, although logs of several organizations can be aggregated if desired.

## Defining Information Requirements

Before implementing a logging solution, it is essential to define the specific information needs it must satisfy. The intended use of the log directly determines what information must be captured.

Key considerations include:

* **Purpose Limitation**: Clearly define the objectives of the log. Is it for internal auditing, providing accountability to citizens, analyzing security patterns, or supplying evidence to supervisory authorities? You should not record more information than is necessary for these defined purposes.  
* **Stakeholder Needs**: Identify all stakeholders and their information requirements. This may include internal auditors, legal departments, security officers, and external regulators or supervisory bodies.  
* **Data Minimization**: A core principle is to avoid storing unnecessary information, especially sensitive data. The goal is to make the log precise, compact, and manageable. Instead of duplicating large amounts of data (e.g., all user attributes from a source system like the BRP), prefer storing only the data used (e.g., the birthdate) or even just a reference (e.g., the BSN) that allows the state at the time of the decision to be recreated.
* **Data Aggregation**: For analytical purposes, such as identifying patterns to improve system resilience, consider providing aggregated or anonymized data. This can provide valuable insights while mitigating the privacy risks associated with granting access to raw log data.  

## Legal and Regulatory Compliance

The storage and management of log data are also subject to various laws and legal frameworks.

* **Applicable Legislation**: Analyze all relevant laws and regulations (e.g., GDPR/AVG, archival and domain-specific laws) to determine requirements and restrictions on storing decision data. This analysis will inform what should and should be logged.  
* **Retention Periods**: Define a clear retention policy for log records. This period should be based on legal obligations and the defined purposes of the log. Data should not be kept longer than is necessary. Data retention may vary within a single log as well.
* **Accountability to Authorities**: Determine the information requirements of supervisory authorities ("toezichthouders"). The log must be structured to provide them with the necessary data for their oversight role in a timely and effective manner.

## Data Integrity and Non-Repudiation

To be effective for auditing and accountability, the Authorization Decision Log must be trustworthy. This requires guarantees of data integrity and non-repudiation.

* **Data Integrity**: This ensures that log records, once written, cannot be altered or deleted without detection. The integrity of the log is paramount for forensic analysis, internal audits, and providing reliable evidence. Organizations should implement technical measures, such as write-once (WORM) storage, append-only mechanisms, cryptographic hashing, or digital signatures, to protect the log data from tampering.
* **Non-Repudiation**: This provides proof of the origin and integrity of the data, preventing any party from denying their involvement in an authorization decision. By ensuring non-repudiation (e.g., through strong authentication of logging components and digitally signing log entries), the log can definitively prove what decision was made, what information it was based on, and which component recorded it.

## Access and Usage Policies

It is essential to define clear policies for authorizing access to the Authorization Decision Log or parts thereof. These decisions to provide or deny access to the log should also be included in the Authorization Decision Log. 

Common and important usage policies include:

* **Responding to Incidents**: The log is a critical tool for forensic analysis after a security incident or data breach. It can help determine what actions were permitted at a specific time and on what basis, even if that permission was technically correct but improper in hindsight.
* **Observability in Trust Frameworks**: The log offers a structured method for data users to provide insight into their data usage to data providers when required, as may be required in trust frameworks. It thus offers an implementation standard for "Observability services" as are often defined for data spaces.
* **Debugging and Support**: The log can be a useful tool for determining why the authorization is not working as expected. It can also contain highly sensitive data, however, so it's essential to carefully define if, and under which conditions, the Authorization Decision Log can be used for this purpose.

## Data Verifiability and Level of Detail

The ability to provide accountability depends on the log's level of detail. A balance must be struck between capturing enough information to accurately replay historical decisions and practical challenges like data duplication and scalability. The appropriate level of detail depends on the organization's specific context and legal requirements, as the highest level is not always necessary.

To ensure historical accuracy while minimizing data storage, referencing external information (e.g., via a timestamp or version number) is preferred over storing copies. This approach keeps logs lean but is contingent on the ability of source systems to provide versioned historical data.

For full replayability, the log must also identify the exact version and configuration of the policy engine that evaluated the decision; however, providing reliable versioning for the engine may not always be feasible, depending on the infrastructure.

We have identified four levels of detail, in order of least to most detail. Each level builds on the information from the previous level.

### Decision Request/Response

At the most basic level only the decision request and the decision response are logged. 

<p class="note" title="Engine boundaries">
The decision request and response can contain all information required for an audit log, as described by [[?ISO/IEC 27002:2022]] and [[?BIO2]]. If that is the case, the Authorization Decision Log <b>MAY</b> be used as an audit log.
</p>

At this level of detail log requests contain the following keys, as defined in [[[#specifications]]]:

| Field          | Required  | Reference                 |
|----------------|-----------|---------------------------|
| `trace_id`     | optional  | [[[#spec-trace-context]]] |
| `span_id`      | optional  | [[[#spec-trace-context]]] |
| `timestamp`    | required  | [[[#spec-timestamp]]]     |
| `type`         | required  | [[[#spec-type]]]          | 
| `request`      | required  | [[[#spec-request]]]       |
| `response`     | required  | [[[#spec-response]]]      |

### Decision and Policies

In addition to the request and response, the exact version of the policies that were used to evaluate the request can be programmatically retrieved.  

At this level of detail log requests contain the following keys, as defined in [[[#specifications]]]:

| Field          | Required  | Reference                 |
|----------------|-----------|---------------------------|
| `trace_id`     | optional  | [[[#spec-trace-context]]] |
| `span_id`      | optional  | [[[#spec-trace-context]]] |
| `timestamp`    | required  | [[[#spec-timestamp]]]     |
| `type`         | required  | [[[#spec-type]]]          | 
| `request`      | required  | [[[#spec-request]]]       |
| `response`     | required  | [[[#spec-response]]]      |
| `policies`     | required  | [[[#spec-policies]]]      |

### All Information Sources

All information used in the evaluation can be programmatically retrieved. This allows full replayability, assuming the engine (PDP) behaves identically or can be manually recreated in the correct state, which is generally achievable.  

At this level of detail log requests contain the following keys, as defined in [[[#specifications]]]:

| Field          | Required  | Reference                 |
|----------------|-----------|---------------------------|
| `trace_id`     | optional  | [[[#spec-trace-context]]] |
| `span_id`      | optional  | [[[#spec-trace-context]]] |
| `timestamp`    | required  | [[[#spec-timestamp]]]     |
| `type`         | required  | [[[#spec-type]]]          | 
| `request`      | required  | [[[#spec-request]]]       |
| `response`     | required  | [[[#spec-response]]]      |
| `policies`     | required  | [[[#spec-policies]]]      |
| `information`  | required  | [[[#spec-information]]]   |

### Full Environment

In addition to all information used in the evaluation, the environment and configuration of the system that evaluates the decision can also be accurately recreated. This provides full, guaranteed replayability and maximum accountability. 

<p class="note" title="System boundaries">
The configuration of all components that influence the decision should be included. While this may be limited to the configuration of the PDP, it often also requires configuration of the PIP and sometimes the PAP as well. 
</p>

At this level of detail log requests contain the following keys, as defined in [[[#specifications]]]:

| Field           | Required  | Reference                  |
|-----------------|-----------|----------------------------|
| `trace_id`      | optional  | [[[#spec-trace-context]]]  |
| `span_id`       | optional  | [[[#spec-trace-context]]]  |
| `timestamp`     | required  | [[[#spec-timestamp]]]      | 
| `type`          | required  | [[[#spec-type]]]           | 
| `request`       | required  | [[[#spec-request]]]        |
| `response`      | required  | [[[#spec-response]]]       |
| `policies`      | required  | [[[#spec-policies]]]       |
| `information`   | required  | [[[#spec-information]]]    |
| `configuration` | required  | [[[#spec-configuration]]] |

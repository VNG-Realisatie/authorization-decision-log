# Level of detail

Logging can take place at different levels of details. The higher the level of detail, the more accurate the environment in which the historical decision occurred can be recreated allowing for optimal accountability.

## Definitions of levels

We have identified the following four level of details in the order of least to most detail.

### Request/Response

Only the request for authorization and the response containing the decision are logged. 

To provide accountability for a historical decision all environmental variables that affected the decision must be recreated from the request/response information; such as request identifiers and timestamps. This may often be impractical or impossible.

### Policy version

In addition to the request and response the exact version of the policies that were used to evaluate the request can programmatically retrieved. 

### Information source

*All* information used in the evaluation can be programmatically retrieved. This allows full replayability assuming the engine (PDP) behaves identically or can be manually recreated in the correct state; which is generally achievable. 

### Full environment 

In addition to all information used in the evaluation, the environment and configuration of the engine that evaluates the decision can also be accurately recreated. This provides full guaranteed replayability and maximum accountability.

## Implications of level of detail

The higher the level of detail; the more detailed accountability can be provided based on the Authorization Decision Log. Higher levels also introduce other challenges around data duplication, scalability and feasibility. 

The lowest level of detail on the other hand will often provide insufficient information to provide the desired level of accountability. 

The interplay between these two aspects depends on the situation, organisation and legal environment. That's why it's important to evaluate which level of detail is appropriate for a given use-case. It is not necessary to always provide the highest level of detail.

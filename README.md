Environments
====

Outline
----

The motivating problem here is that we have multiple developers working on the code base(s) of one or more production services. We want to be able to release new features quickly and we want to minimize the number of bugs that make it to production. The solution described here consists in the provision of 1) shared staging environments as well as 2) containerized local development environments. While this solution is not novel it can be gone about in different ways, and the point of this presentation of staging and development environments is to specify their desired properties, to call out their natural limitations, and to describe useful workflows that leverage them.

Staging environments
----

Prior to a release of code changes to any production service, the same code changes should run in a staging environment for long enough to generate performance statistics and output data sufficient to verify that the code changes have introduced no regressions (with respect to code-correctness or performance) and that the effect of any new features is as expected. The staging pipeline should be exposed to conditions and input data as representative as is feasible of real production conditions and input data.

There are at least two distinct structures of staging environment, either or both of which might be employed:

### Single-service staging environments

In a single-service staging environment, input data (whether mocked or drawn from upstream in the production pipeline) is fed to the service under test in an isolated staging environment. That service's output data is not fed to services downstream in the pipeline but rather is analyzed directly, whether by some set of assertions (i.e. "end-to-end tests") and/or comparison to a reference data set (i.e. a "diff-test"). In this scheme, changes to the service under test are validated with reference to that service's performance and outputs. No direct reference is made to the effect that any changes to the distribution of the service's output data might have to the full pipeline's performance and final outputs.

Pros: Less costly; easier to parallelize staging of changes to different services; faster iteration.

Cons: No direct view of the changes' impact on downstream services performance or outputs.

### Full-pipeline staging environments

In a full-pipeline staging environment, all of the pipeline's services are run in isolated staging environments that are connected (in the flow of data) to one another. Validation of the changes staged for release is done with reference to the full pipeline's performance and final outputs. Usually, when full-pipeline staging results pass validation, more confidence in the changes is gained than from single-service staging environments. On the other hand, when regressions are identified in full-pipeline staging results, it may be harder to pinpoint their root cause than when the regression arises in a single-service staging run.

Pros: Validation may provide greater confidence in changes.

Cons: Most costly; harder to parallelize staging of changes to different services; slower iteration; harder to pinpoint the cause of a regression.

Development environments
----

By "development environment" I'm referring to the environment that a service application might run in on a given developer's workstation, be it their laptop, a cloud instance, or some virtualized environment. The relevant thing is that the developer's workstation might not be dedicated to the purpose of running the service under test. Because of that, some system resources are necessarily shared between the running service and arbitrary other things going on in the developer's workstation. Because of that, results from tests conducted in the developer's workstation are more often not reproducible, so they enstill less confidence in validation of code changes and especially of effects on performance.

* * *

While staging pipelines are worthwhile and important, imitations of relying a staging pipeline alone include:
* Any bugs that arise in staging require that we either start the staging experiment again after the bug is fixed or else proceed with an uncertain release (after fixing the bug, of course); and
* If we have multiple services in our pipeline then it is impossible or relatively expensive to vet changes to one service in isolation, keeping all other services running the same code as is then in production.

Local development environments are ideal for addressing both of the above limitations. In a local development environment, a single service is containerized and code changes to it can be tested in isolation, iteratively and rapidly.

Running the service in a development environment should reflect production conditions with the following possible exceptions:
* The distribution of possible input data may be two large to expose the service to all of it in a given run;
* The hardware that the development environment runs in may differ from the production hardware;
* The service in the dev env may interact with mocks or fixtures of external services and APIs that the production service interacts with.

Desired properties of a dev env include:
* Isolated from the host machine's environment.
* Requires little to no knowledge by the user of the "glue" connecting the service to any fixtures or mocks.
* Accepts arbitrary valid input.
* Enables materialization of outputs.

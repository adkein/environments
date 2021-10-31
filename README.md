Environments
====

The motivating problem is that we have multiple developers working on the code base(s) of one or more production services. We want to release new features quickly and we want to minimize the number of bugs that make it to production.

We should build a staging pipeline to vet combined code changes to all services on the pipeline, in preparation for a release. The staging pipeline should be exposed to conditions and input data as representative as is feasible of real production conditions and input data.

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

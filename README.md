# Notes on AWS Lambda Performance

- This is a lambda function that sits behind API Gateway.

- The lambda function pretty much acts as a "passthrough" to the DB to perform some sort of DB query.

- We should think about things we can configure on API Gateway, AWS Lambda, and MongoDB.

- Our analytics API is in the `Aktiv-Lambda/account-backend/f-domains/analytic`.

## AWS API Gateway

- For e.g. look at [Developer Guide](https://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html) for understanding limits that we could set on API Gateway. Read the docs and figure out if there is something useful that we can configure to get more load.

### REST vs HTTP
- There are different types of AWS API Gateway REST vs HTTP (actually `AWS::ApiGateway` and `AWS::ApiGatewayV2`)
- [Differences](https://www.tinystacks.com/blog-post/api-gateway-rest-vs-http-api-what-are-the-differences/)

| Feature                       | HTTP APIs                      | REST APIs                                   |
|-------------------------------|--------------------------------|---------------------------------------------|
| Price and performance         | + *Cheaper, faster*            | - *More expensive, slower*                  |
| Canary support                | - *Manual*                     | + *Build-in for simple traffic shifting*    |
| WAF Support                   | - *Not, possible via VPC Link* | + *Build-in*                                |
| AWS X-Ray                     | - *Manual*                     | + *Build-in*                                |
| Ease to deploy                | + *Designed for ease of use*   | - *Complicated, longer learning curve*      |
| Swagger/OpenAPI support       | - *Export only*                | + *Import & Export*                         |
| Private Integrations          | + *ALB, NLB, Cloud Map*        | - *NLB*                                     |
| Authentication                | + *Build-in*                   | - *Manual*                                  |
| Mock API                      | - *Manual*                     | + *Build-in*                                |
| API Management                | - *Custom domain names*        | + *Natively supports usage plans, API keys* |
| Certificates for backend auth | - *Not supported*              | + *Build-in*                                |
| Deployment types              | - *Regional*                   | + *Regional, edge-optimized, and private*   |
| Kinesis Data Firehose         | - *Manual*                     | + *Build-in*                                |
| Resource policies             | - *Manual*                     | + *Build-in*                                |

### REST API Caching
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html

## AWS Lambda

- See the https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html for info about quotas on lambda. We can configure `memorySize` for e.g. to have a higher spec lambda machine which can be up to `10240` which would give us 6 cores. Is this worth it? Do we need more than 1 core? We could also request a higher limit of 1000 lambdas.

- We should understand how lambda works and how they get re-used. Perhaps we should add some code in the lambda function to know if the lambda function gets re-used and then we can asses this in the logs.

- There is also `reservedConcurrency` concept for a lambda function, what should this be set to?

## Mongo DB

- And then finally, look at our DB queries, figure out if we need better DB indexes to support them.

- Look at our (Atlas) MongoDB specification. Can we upgrade something specific for this to improve these sorts of aggregation queries that we need to make? Perhaps better CPU? Better IOPS?
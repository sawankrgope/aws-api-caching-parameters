# AWS API Gateway Caching with Lambda (POC)

This Proof of Concept (POC) demonstrates how to implement caching in **AWS API Gateway** with **AWS Lambda**, using **query parameters** and **custom headers** as cache keys. The goal is to optimize performance by reducing redundant Lambda invocations and serving cached responses.

---

## Table of Contents
- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Setup Instructions](#setup-instructions)
  - [Create the Lambda Function](#1-create-the-lambda-function)
  - [Set Up API Gateway](#2-set-up-api-gateway)
  - [Enable and Configure Caching](#3-enable-and-configure-caching)
- [API Gateway Caching](#api-gateway-caching)
- [Testing](#testing)
- [Future Scopes](#future-scopes)
- [Conclusion](#conclusion)
- [License](#license)

---

## Project Overview

This POC demonstrates the implementation of caching in AWS API Gateway, integrated with AWS Lambda, by using query parameters and headers as cache keys. This improves performance and reduces backend Lambda invocations by caching specific responses based on client requests.

---

## Architecture

1. **AWS Lambda**: Generates a random number for each request and returns it with the `userId` and `X-Request-Type`.
2. **API Gateway**: Routes the incoming request to the Lambda function. API Gateway caches the response based on the cache key parameters (query string and header).
3. **Cache Key Parameters**: Responses are cached based on the `userId` query parameter and `X-Request-Type` header.

---

## Prerequisites

Before starting, ensure you have the following:
- An **AWS Account** with access to create Lambda functions and API Gateway.
- **AWS CLI** or **AWS Management Console**.
- **Node.js** installed on your local machine (for creating Lambda functions).

---

## Setup Instructions

### 1. Create the Lambda Function

Create a new AWS Lambda function with the following code. This Lambda function will return a random number based on the query parameters and headers.

```js
export const handler = async (event) => {
    const userId = event.queryStringParameters ? event.queryStringParameters.userId : 'default';
    const requestType = event.headers['X-Request-Type'] || 'default';
    const randomNumber = Math.floor(Math.random() * 100);

    return {
        statusCode: 200,
        body: JSON.stringify({
            message: `Random number for user ${userId} with request type ${requestType}`,
            number: randomNumber,
        }),
    };
};

\`\`\`

# AWS API Gateway Caching with Lambda (Setup)

This section covers how to set up AWS API Gateway for caching using query parameters and headers as cache keys.

---

## Setup Instructions

### 2. Set Up API Gateway

- Go to **API Gateway** in the AWS Console.
- Create a new API with **REST API** as the protocol.
- Add a new **GET** method that integrates with your Lambda function.
- Deploy the API to a stage (e.g., `dev`).

### 3. Enable and Configure Caching

- In **Stage** settings, enable caching.
- Set **Cache Key Parameters** as:
  - **Query String**: `method.request.querystring.userId`
  - **Header**: `method.request.header.X-Request-Type`

This configuration ensures that API Gateway will cache responses for different `userId` and `X-Request-Type` combinations.

---

## API Gateway Caching

When caching is enabled in API Gateway, the following caching behavior occurs:

- The request URL `https://your-api-url/?userId=1` with the header `X-Request-Type: typeA` is cached.
- A different request, like `https://your-api-url/?userId=1` with the header `X-Request-Type: typeB`, will generate a new random number and cache this new response.

This allows responses to be cached for each unique combination of `userId` and `X-Request-Type`.

---

## Testing

### Test Cases:

1. **Request 1**:
   - URL: `https://your-api-url/?userId=1`
   - Header: `X-Request-Type: typeA`
   - Response: A random number is generated and cached.

2. **Request 2**:
   - URL: `https://your-api-url/?userId=1`
   - Header: `X-Request-Type: typeB`
   - Response: A different random number is generated and cached.

3. **Request 3**:
   - URL: `https://your-api-url/?userId=1`
   - Header: `X-Request-Type: typeA`
   - Response: The cached response from **Request 1** is returned.

This confirms the caching is working based on query parameters and headers.

---

## Future Scopes

Here are potential improvements or extensions to the current setup:

- **Advanced Cache Invalidation**: Implement cache invalidation logic when data changes.
- **Layered Caching Strategy**: Introduce multiple caching layers (e.g., Redis, Memcached).
- **Role-Based Caching**: Cache different responses based on user roles or authentication tokens.
- **Dynamic Cache Key Composition**: Expand the use of cache keys to include cookies or request bodies.
- **Monitoring and Optimization**: Use CloudWatch or third-party tools to monitor cache hits and optimize performance.
- **Enhanced Security**: Add authentication mechanisms like OAuth or API keys for secured access.

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.

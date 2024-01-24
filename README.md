# apollo-graphql-errorlink

A simple integration to include GraphQL queries &amp; variables in your Sentry issues when using Apollo GraphQL.

## Implementation

The implementation is simple. But here are the dependencies needed :

- Apollo GraphQL
    - We are using their `onError` link
- Sentry
    - Used to add extra information to our issues

```javascript
const errorLink = onError(({ graphQLErrors, networkError, operation }) => {
    const scope = new Sentry.Scope()
    scope.setLevel('error')
    scope.setExtra('query', operation.query.loc?.source.body)
    scope.setExtra('variables', operation.variables)
    if (graphQLErrors) {
        Sentry.captureMessage(`[ErrorLink] GraphQL Error : ${graphQLErrors.map(error => error.message).join(', ')}`, scope)
    }
    if (networkError) {
        Sentry.captureMessage(`[ErrorLink] Network Error : ${networkError.message}`, scope)
    };
});
```


## Usage

You can just add the created link above into your `ApolloLink` array when initializing your client. **It should be before your terminating links**

```javascript
const link: ApolloLink = ApolloLink.from([
    authLink,
    errorLink, <--
    ...terminating links
])
```
## Contributing

Contributions are always welcome! Fork out and create a Pull Request!

Please adhere to this project's `code of conduct`.


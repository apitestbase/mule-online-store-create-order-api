# Online Store — Create Order API

A demo MuleSoft API for Integration Unit Testing with [API Test Base](https://apitestbase.io): it accepts a customer's order, saves it to the database, and publishes an order-created event for the fulfillment system.

It accompanies the article [Integration Unit Testing in Action: MuleSoft](https://apitestbase.io/iut-in-action/mulesoft). Start there.

The API's full promise — HTTP request and response, plus side effects — is specified in [CONTRACT.md](CONTRACT.md).

The Integration Unit Tests for this API live in the [mule-online-store-integration-unit-tests](https://github.com/apitestbase/mule-online-store-integration-unit-tests) repository.

## Prerequisites

- A Mule 4.12 runtime — via Anypoint Studio, or standalone. The project is pinned to it (`minMuleVersion` 4.12.0), so an older Studio with an older embedded runtime won't deploy the app.

## Getting the project into Anypoint Studio workspace

Clone this repository via Anypoint Studio's Git perspective, or use direct git commands to clone the repo and import it into the Studio workspace.

## Note
The JMS connector version is deliberately kept at 1.10.x, so that the app can also run on Mule CE 4.12.0 (useful for testing the app in a CI/CD pipeline without depending on a Mule EE runtime). Later versions of the JMS connector fail there with `java.lang.ClassNotFoundException: org.mule.runtime.core.api.util.ClassUtils`.

## License

[Apache License 2.0](LICENSE)

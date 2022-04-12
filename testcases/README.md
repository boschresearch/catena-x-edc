# EDC Test Cases

## Feature: Connector Integration

**Scenario:** _Consumer and provider use IDS DAPS for participant identification._

## Feature: Data Management

**Scenario:** _Data Provider creates a new asset with data address._
- _When_
  - I create a new asset using the data management API
- _Then_
  - my creation call returns a HTTP 201 code
  - _and_ I can request this asset using the data management API

**Scenario:** _Data Provider creates a new contract definition._

**Scenario:** _Data Provider creates a new policy_

## Feature: Contract Management

**Scenario:** _Data Provider creates a new contract offer_
- _Given_
  - the data provider runs with a SQL storage
- _When_
  - I create a new asset using the data management api
  - _and_ I create a new public policy using the data management api
  - _and_ I create a new contract definition using the data management api
- _Then_
  - the data provider creates exactly one contract offer for potential data consumers

**Scenario:** _Data Consumer sends the data provider a counter offer message, describing different actions._

**Scenario:** _Data Provider sends the data consumer a contract agreement message, adding one more constraint._

## Feature: Data Catalog

**Scenario:** _Data consumer requests a data offer catalog from the data provider._

**Scenario:** _Data consumer requests data offer catalog from the data provider. The provider has different offers for each BPN._

## Feature: Data Transfer

**Scenario:** _Data consumer initiates a HttpProxy-DataTransfer from the data provider_
- _Given_
  - I have a contract agreement
- _When_
  - I ask to transfer the asset of the agreement
  - _and_ use the 'HttpProxy' method
- _Then_ my backend application should be provided with a token
- _and_ I can use this token to request data synchronously from my DataPlane

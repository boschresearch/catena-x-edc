# EDC Test Cases

## Feature: Data Management

Scenario 'Data Provider creates a new asset with data address'
- When
  - I create a new asset using the data management API
- Then
  - My creation call returns a HTTP 201 code
  - I can request this asset using the data management API

Scenario 'Data Provider creates a new contract definition'
Scenario 'Data Provider creates a new policy'

## Feature: Contract Management

Scenario 'Data Provider creates a new contract offer'
- Given
  - the data provider runs with a SQL storage
- When
  - I create a new asset using the data management api
  - AND I create a new public policy using the data management api
  - AND I create a new contract definition using the data management api
- Then
  - The data provider creates exactly one contract offer for potential data consumers

Scenario 'Data Provider creates a new contract offer for specific business partner'


## Feature: Data Catalog

Scenario 'Data consumer requests a data offer catalog from the data provider'

## Feature: Data Transfer

Scenario 'Data consumer initiates a HttpProxy-DataTransfer from the data provider'
- Given
  - I have a contract agreement
- When
  - I ask to transfer the asset of the agreement
  - AND use the 'HttpProxy' method
- Then my backend application should be provided with a token
- AND I can use this token to request data synchronously from my DataPlane

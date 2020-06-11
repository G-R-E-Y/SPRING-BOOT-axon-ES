# spring-boot-axon
Event Sourcing with Spring Boot and Axon

What is Event Sourcing
---------------------------

The basic idea of event sourcing is to store every change in the state of an application as 
events instead of only storing the current state. The current state can be constructed by 
applying all past events. 

Unlike the traditional approach with a relational database, event sourcing does not persist 
the current state of a record, but instead stores the individual changes as a series of deltas 
that led to the current state over time.

The procedure is similar to the way a bank manages an account, for example. The bank 
does not save the current balance. Instead, it records the deposits and withdrawals that 
occur over time. The current balance can then be calculated from this data: 

+500 (deposit)
+200 (deposit)
 -300 (withdraw)
---
= 400 (balance)

Event sourcing is relatively simple: a storage for events is required, which only has to support 
adding and reading events. It is therefore a so-called append-only data store.

Calling the API
------------------

1. With the database configured we are now able to call our REST API to verify that the application works as expected. First, let’s create a new account:

curl --data {\"name\":\"ramana\"} -H "Content-Type: application/json" localhost:8080/accounts

The response is the account id, e.g. 725a33ec-6e37-4dff-8fa2-7c79a004263e


2. With the new account you can deposit money:
curl -X PUT --data 1000 -H "Content-Type: application/json" localhost:8080/accounts/9557b37d-76d7-43af-b8ab-4ee8d7cbade6/balance

and withdraw money:

curl -X PUT --data -2000 -H "Content-Type: application/json" localhost:8080/accounts/9557b37d-76d7-43af-b8ab-4ee8d7cbade6/balance

Both requests will only return HTTP Status 202 and no body. At the moment there is no way to request the current balance of an account.

3. It is also possible to verify that you cannot overdraw the account:

curl -X PUT --data -1000 -H "Content-Type: application/json" localhost:8080/accounts/d378da8e-55d5-4e89-807a-93959f863f4c/balance

This will result in error with the message: Insufficient balance.

4. And finally you can close the bank account:
 	
curl -X DELETE localhost:8080/accounts/725a33ec-6e37-4dff-8fa2-7c79a004263e



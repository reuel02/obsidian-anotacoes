
![[Pasted image 20261123194931.png]]

|Stage|Value|Timing|Typical use|Key constraint|
|---|---|---|---|---|
|PreValidation|10|Before the main operation and before security checks|Cancel an operation early, before any transaction opens|Runs before permission checks occur|
|PreOperation|20|Before the main operation, inside the transaction|Change values on the entity undergoing the operation|Canceling here rolls back an open transaction and carries a performance cost|
|MainOperation|30|The platform's core data operation|Reserved for custom APIs and virtual table data providers|Not available for your own business-logic plug-ins|
|PostOperation|40|After the main operation, inside the transaction for synchronous steps|React to a completed change; call other systems|The only stage that supports asynchronous execution|
A Dataverse Plugin can 
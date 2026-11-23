
![[Pasted image 20261123194931.png]]

|Stage|Value|Timing|Typical use|Key constraint|
|---|---|---|---|---|
|PreValidation|10|Before the main operation and before security checks|Cancel an operation early, before any transaction opens|Runs before permission checks occur|
|PreOperation|20|Before the main operation, inside the transaction|Change values on the entity undergoing the operation|Canceling here rolls back an open transaction and carries a performance cost|
|MainOperation|30|The platform's core data operation|Reserved for custom APIs and virtual table data providers|Not available for your own business-logic plug-ins|
|PostOperation|40|After the main operation, inside the transaction for synchronous steps|React to a completed change; call other systems|The only stage that supports asynchronous execution|
A Dataverse plug-in can be created with two execution modes: synchronous or asynchronous. In synchronous mode, the plug-in is executed as part of the main operation, and the operation waits for the plug-in to finish its execution.

Synchronous plug-ins can be used in PreValidation, PreOperation, and PostOperation stages, depending on the requirements. However, asynchronous plug-ins can only be registered in the PostOperation stage.

When you use asynchronous mode to run a plug-in in the PostOperation stage, the plug-in does not execute at the same time as the main operation. Instead, its execution is queued and processed asynchronously after the main transaction has completed. Because of that, asynchronous execution is a good choice for PostOperation tasks that do not need to complete immediately, especially when you don't want the user to wait for the plug-in to finish.


|Step|Checks|Execution order|
|---|---|---|
|1|Allowance balance|10|
|2|Device table for an eligible replacement category|20|

Dataverse uses this value to decide which step runs first: a lower execution order value runs before a higher one, for the same stage and message. Here, the allowance check (order 10) runs before the replacement-category check (order 20). Don't give two steps on the same message and stage the same execution order, because Dataverse doesn't guarantee which one runs first.

Keeping related checks in separate, ordered steps, rather than one large step, makes each piece of logic easier to test and reuse later. Contoso's developers build out the full plug-in that way in the next unit.
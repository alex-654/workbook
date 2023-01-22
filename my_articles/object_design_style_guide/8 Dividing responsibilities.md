### Separate write models from read models
- Different clients use entities in different ways.
  Some clients will want to manipulate an entity’s data using its command methods,
  while others just want to retrieve a piece of information using its query methods
- You should never pass an entity that can be modified to a client that isn’t allowed
  to modify it so separate the write model from the read model
- Clients that solely use an entity to retrieve information should use a dedicated read model instead of a write model
- Some object still need both queries and command methods, and it's ok

### Create read models that are specific for their use cases
We may create any number of read models that correspond to each of the application’s specific use cases

### Create read models directly from their data source

### Build read models from domain events
More moving part of code involved so they more difficult to implement and maintain.
But sometimes its helpful. Make read model queries more efficient.

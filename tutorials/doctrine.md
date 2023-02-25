Entity name for exp. Product

- To apply entity changes or get them from DB use Repository (ProductRepository)
  Repository extended from Lib or framework.
  And contain simple methods like find, findAll and can get access to queryBuilder
- Repository contain EntityManager and Entity class name.
  EntityManager can create Query and QueryBuilder. Knows only about Metadata from Entity
- queryBuilder contain entityManager.

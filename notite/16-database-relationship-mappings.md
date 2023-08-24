DB relationship mappings
----

DB relations will get complex easily..
relationshipg mapping in JPA can support many scenarios ..

Relationships:
- one-to-one  [ PK / UQ ]
- one-to-many : an object with a list of properties [ PK / FK ]
- many-to-many : two lists related to each other  [ JOIN TABLE, 2 x PK/FK ]

E.R.D. = Entity Relationship Diagram

Relationship direction:
- bi-directional
- uni-directional

Cascading operations:
- eg. deleting would fail when PK/FK .. hibernate would cascade all relevant deletes ..
- JPA has annotation @ForeignKey - ONLY METADATA INFORMATION !!
  - Hibernate will ref this ONLY for schema generation, it will NOT enforce NOT generate if missing
  - not needed when using schema migration tools ( Liquibase / Flyway )

HIBERNATE will only look at the JPA entities and verify that the data-structure is there for those.
  Initially hibernate will not know about them.
  First we will create the DB schema .. then DB relationships to support the several relationships we added.
  Take the model from DB to Java modeling .. setting relationships in JPA..

----------------------------------------------------------------------

 BEER_ORDER   ------<  BEER_ORDER_LINE  >------------  BEER
    V
    |
    |
  CUSTOMER             FLYWAY_SCHEMA_HISTORY

----------------------------------------------------------------------

MySQL Workbench can generate the E.R.D. for us, once we have the tables ..
Order of actions: run own DDLs and then model them in entities/x-pojo.java ..

BEST PRACTICE:
1. always add helper methods for mapping, can do for both/one entity 
  ( initialize with empty hashSet, add @Builder.Default, add own setter(set this, set other to this), remove default Lombok @AllArgsCtor while adding own Ctro to invoke own setter method)
2. naming conventions to follow, for hibernate to know DB columns .. In our SQL script, use '_' : eg: beer_order_id, etc


When in debuger and checking fields from mapped entities, we could see warning like:
  - 'Unable to evaluate the expression Method threw 'org.hibernate.LazyInitializationException' exception.'
      This is because we are running outside the transactional context.
      When using a XXRepository methods ( save, etc ), there will be an implicit transaction.
      We can make it run all in a @Transaction context => cover the error with LazyInit
  - Even after that, we could notice that a mapped relation, could bring 0 results
      This is because we maintain 1 side of the relationshisp, we'll not see it was not flushed to DB .. hibernate would think that
      the other side of the relationship hasn't been done yet ..
      2 Options: manually set the relation, or use SaveAndFlush() instead of Save(). 
      SaveAndFlush() could impose performance degradation.
      ALTERNATIVE: 
         - add helper method (BeerOrder.java)
         - can override the Setter method for customer, in BeerOrder class ..
             public void setCustomer(Customer customer) {
                 this.customer = customer;
                 // do the inverse as well, so no need to SaveAndFlush
                 customer.getBeerOrders().add(this);
             }
         - in Customer.java, initialize the object :    private Set<BeerOrder> beerOrders = new HashSet<>();
         - remove the Lombok annotation @AllArgsConstructor from BeerOrder
            and set own full-args Ctor, and invoke our own setter method:
            public BeerOrder(UUID id, Long version .. ) {
                this.id = id;
                //this.customer = customer;
                setCustomer(customer);
            }

CASCADE OPERATIONS
- @OneToTone/etc ( cascade = CascadeType.xx )
- 2 categories of TYPES: JPA specific and HIBERNATE specific
- JPA will be availlabe in any JPA implementation. 
  Hibernate ones will be available only when using Hibernate for your JPA provider
  A. JPA specific cascade-types ( child object == transient instances )
        CascadeType.ALL        - propagate all operations
        CascadeType.DETACH     - detach also child obj from persistence context
        CascadeType.MERGE      - cp state ( includes persist ), eg if child is already persisted, will be updated/merged further ..
        CascadeType.PERSIST *  - save also child objects ( transient instances )
        CascadeType.REPRESH    - refresh also child objects 
        CascadeType.REMOVE  *  - rem also child objects
  B. HIBERNATE specific cascade-types:
        CascadeType?.DELETE        - same as JPA REMOVE
        CascadeType?.SAVE_UPDATE   - save+update
        CascadeType?.REPLICATE     - replicate child objects to second data source
        CascadeType?.LOCK          - reattaches entity and children to persistence context - without refresh
- ___
- Eg. we can add CascadeType.PERSIST to BeerOrder.BeerOrderShipment, so when we create a new Beer with link to a newly beerOrderShipment,
      and try to save the beer, to automatically persist the shipment as well
- Eg: caution using Delete..
     When deleting Order, might want also orderLines also..
     BUT woulud not want to delete up to products ( beers objects ) ..

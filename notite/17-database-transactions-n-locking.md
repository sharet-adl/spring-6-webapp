DATABASE TRANSACTIONS

BRANCH: 109-xx

- mongo had in the beginnning bad reputation for losing data, as they do not handle transactions ( transactions (commit/rollback), perform consistent view )
  - A.C.I.D.:
        Atomicity
        Consistency
        Isolated
        Durable
- transaction = unit of work, DML, 1+ sql operations
- commit = end of a transaction, make permanent
- rollback = revert all
- save-point = programatic point, allowing rolling back to ( rollback part of transaction )

LOCKS:
- record lock in general. Table lock / DB lock ..
- lock records of affected rows: UPDATE / DELETE / SELECT FOR UPDATE (exclusive) / INSERT?
- other sessions will wait
- deadlock - both fail and rollback
- alter statements, adding columns - full lock

ISOLATION LEVELS
- repeatable read *  ( view / snapshot of DB at point in time when you started transaction )
- read committed
- read uncommitted   ( dirty-read )
- serializable

ACID compliance is complex and costly for NoSQL DBs ( high performance ) ..

CONCEPT: LOST UPDATE
  2 sessions read original value from DB as 10; sess-A wants to add +5, session-B wants to add another +5 ..
  after second one commit, the final value is +5 only, instead of +10
  SOLUTIONS:
  - JDBC locking modes ( mode applies to lifespan of the connection )

JPA LOCKING:
  1 PESIMISTIC LOCKING ( imposing exclusing lock )
    DB locking mechanisms are used, to lock records for updates.
    Capabilities vary with DB and JDBC driver ..  Eg: 'SELECT FOR UPDATE..'
    Modes: 
      - PESSIMISTIC_READ
      - PESSIMISTIC_WRITE
      - PESSIMISTIC_FORCE_INCREMENT ( if entity has 'version' property )
  2 OPTIMISTIC LOCKING
    Done by checking a version attribute of the entity ..
    Under the hood, JPA will set a version attribute on the entity on the corresponding DB record, start at 0 ..
    Can be int/(Integer)/long/Long/short/Short/java.sql.Timestamp ..
    Downside: updates outside of JPA/Hibernate, which do not uddate the version, will break it !
    Downside: performance for extra read
    Modes:
       - OPTIMISTIC
       - OPTIMISTIC_FORCE_INCREMENT ( same as previous, but increments the version value )
       - READ
       - WRITE

Q: why not DB itself to compute SHA of original value, and upon trying to commit to allow throwning exception if they differ ?

WHICH TO USE
- does it need locking ? will it have concurrent updates of the same records ?
- pesimistic-locking : use if data is frequently updated and if updated concurrently ( costly, more work to avoid deadlocks )
- optimistic-locking : use if data is read more often than updated ( majority ). Also when web-aps, waiting seconds for user to enter data ..

MULTI-REQUEST CONVERSATION
- wait for the user to provide info ..


DEMO
- project GIT: 'sdjpa-order-service'

Spring Data JPA Transactions
- supports by default implicit transactions, meaning repository methods will create a transaction if there isn't one active
- two types of implicit transactions:
-   read operations - done in a readonly context
        - use with caution: dirty checks are skipped for performance; if object from context is updated/save, might encounter issues
        - do not use if read/ want to update
-   updates/delete are done with default transactional context


Spring Boot Testing Transactions
- SB by default will create a transaction for tests, and roll it back
- implicit transactions are NOT used in test context, because there is already an active transaction
-   Eg: some method under test, with 1..N repository calls, may see different results when ran outside of the test context.
         Typically, a detached entitty error from accessing LAZY load properties, outside of Hibernate context
-        Solution - run within a transactional context ( @Transactional )
- two ways to specify a transactional:
  - @Transactional ( org.springframework.transaction.annotation, older javax.transaction ). SFW version is more flexible thatn the jakarta version..
  - value / label / propagation / isolation / ..
  - rollbackFor / ..
  - auto-configure instance of Transaction Manager --> Interface PlatformTransactionManager (JDBC, Hibernate, JAA, )
    SB will auto-configure the appropriate implementation .. based on the classes foudn in the ClassPath,
      and those will have the bean name as 'transactionManager'
  - timeout: -1 ( use underlying impl ). SB does not override it. Can be set at connection level, otheweise default to platform ..MySQL-8h
- not supported in 'private' methods
- do NOT annotation inner methods with @Transactional, as SB uses AOP and it will not detect it !!!
- --------------------------------------------------------------------------------------------
     CORRECT                                   WRONG
   -------------------------------------------------------------------------------------------
   @Transactional                         | void topMethod() {
   void topMethod() {                     |   method1();
      method1(); // with repo usage       |   method2();
      method2(); // with repo usage       | }
   }                                      | 
                                          | @Transactional
   void method1() {..repo.save() ..}      | void method1() { ..} 
                                          |
                                          | @Transactional
   void method2() {..}                    | void method2() {..}
                                          |

  --------------------------------------------------------------------------------------------

- ObjectOptimisticLockingFailureException
  Depending on how the code is impl, issue might be caught or not ..

  TestMethod                                     |  IMPL1 SVC                     | IMPL2 SVC
- -----------------------------------------------+--------------------------------+--------------------------
  - get repo object                              | impl(id, obj) {                | impl(id, obj)
  - do update1 and repo-save#1                   |   repo.save(input obj)         |   repo.findById(id).ifPresentOrElse
  - use same object, do update2 and repo-save2   |                                |     atomicReference.set()
  -                                              |                                |     

  IMPL2: TestMethod will pass - Hibernate knows that we are in a transaction, so it will do both updates at once
  IMPL1: TestMethod will fail - detached object to persist, we took update and tried to commit directly

  

  
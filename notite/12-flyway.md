Flyway
- flyway migrations, simple to use
- folositor in contextul creeri multor environments ( local, ci, dev, uat, .. ) ..
- important de a putea migra DB changes impreuna cu Code changes - tandem

https://docs.spring.io/spring-boot/docs/3.0.0-M5/reference/htmlsingle/#howto.data-initialization.migration-tool.flyway

QA/UAT/Production - usually permanent DBs, maintained not by DEV
    Liquibase     vs     Flyway
    ------------------------------
     yaml, xml           SQL and Java only
     json, sql abst
     more robust         more popularity
     larger
     enterprise          90% cases

Flyway commands:
   Migrate
   Clean
   Info
   Validate
   Undo
   Baseline ( assumes DB tables and objects are already there, case when want to introduce Flyway to an existing DB schema )
   Repair

SpringBoot supports both tools .. It will run Flyway on startup, to update configured database to latest changeset.

Dependencies:
  mvn:org.flywaydb:flyway-mysql:

Configure flyway to perform the DB migrations:
- by default SpringBoot configures location folder: 'classpath:db/migration' ( eg. resources/db/migration )
- generate the sql script from prev lesson ( drop-and-create.sql ) and copy it to the default path above
- rename it to 'V1__init-mysql-database.sql' ( very important __ for naming convention, V<vers>__<name>.sql, vers = { 1, 2_1, etc } )
- spring.flyway.locations=classpath:db/migration/{vendor} ( h2 might not like the sql scripts, etc )
- set property 'spring.jpa.hibernate.ddl-auto=' update -> validate  ( to run after flyway, to validate only )
- flyway will create its own table with metadata, named 'flyway_schema_history' [installed_ra, version, description, type, script, checksum, installed_by, installed_on, execution_time, success]
- change#1 : add DB column
  - update entity pojo Customer, add @Column(length = 255)String email;
  - run app => fail application startup => hibernate error: 'Schema-validation: missing column [email] in table [customer]..'
- after the switch (mysql and flyway with mysql scripts), the tests might start failing ..
  H2 might not like when we switch to mySQL, syntax of mysql ( for previously defined tests )/ mysql compatibility mode - limited
  - 2 options:
    - allow hibernate to manage the schema, when doing tests (H2 in-memory)
    - configure flyway to use vendor specific scripts
- we can disable Flyway in our application.properties, AND enable it only in our own mysql profile (application-localmysql.properties)
  - spring.flyway.enabled=false/true

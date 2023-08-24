CSV
- not structured data
- simple and useful when you just need to get some data in the DB
- tool OpenCSV - help map the row of CSV data to a Java POJO, and populate it into DB, using Spring DataJPA
- create resources/csvdata/beers.csv
  Data originally provided via [Plotly GitHub Repository](https://github.com/plotly/datasets) under MIT license.
- create a POJO / model : BeerCSVRecord.java. Preferable to use specific types ( Integer, Float ) - needs a bit of time to check all data ..
- https://opencsv.sourceforge.net/
  - List<MyBean> beans = new CsvToBeanBuilder(new FileReader(yourCsvFile)).withType(MyBean.class).build().parse();
  - or even load it to a map, without POJO declared
- DEPENDENCIES:
  - com.opencsv:opencsv:5.7.1
- POJO - use annotation @CsvBindByName to bind to names of colums in CSV
- define own service to load the file, using CsvToBeanBuilder() ..
- can invoke it in Bootstrap part, to preload data .. and to persist into DB
- hibernate to populate 'created_date' and 'update_date' in table 'beer' .. Update 'beer' entity with:
  @CreationTimestamp and @UpdateTimestamp - hibernate specific annotations. Useful to auditing ..
  For testing, truncate the tables in DB, to get recreated ..
- once we updated the BootstrapDataTest to autowire the BeerCsvService -> failure 'NoSuchBeanDefinition no qualifying bean'
  This is because we used a test-splice (DataJpaTest), which was not bringing the whole spring context ..
  Fix: add extra explicit import Annotation for test class: 
       @DataJpaTest
       @Import(BeerCsvService.class)
       class BootstrapDataTest {..}
- _
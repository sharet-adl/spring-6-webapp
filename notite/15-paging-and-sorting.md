Paging and sorting
- when dealing with large record sets, request data in manageable chunks
- paging ~ pagination; first page ~25, ordered by 'best (search) match' ..
- most SQL DBs will return everything for a query; could set LIMIT, some impose 1000 by default 
  - SQL 'limit' clause and 'offset' clause
- by default data is not sorted ( pulled as it found it ) .. ORDER BY ASC(/DESC)
- 
- Spring Data JPA 
 - not limit OTB, only limit is JVM => best practice is to set default limit for number of records to return
 - abstracting the paging/sorting to the JPA standard
 - structures:
   - 'PageRequest' : page number(0+) and size, unsorted default
   - 'Page'        : INTERFACE, utility with Pageable next/previous ..
 - the query methods support it (?pageNumber=, ?pageSize=), add last parameter and update return type
- API in Spring-data-commons family of projects

Hands-on:
- modify the controller (beer), to accept page info (nr/size) ..
  - TDD - add test testListBeersByNameAndStyleShowInventoryTruePage2()
  - controller - refactor listBeers() :
    - Integer pageNumber 1
    - Integer pageSize 25
  - logic to be implemented in service layer
  - service - refactor the service inrterface to accept paging parameters
    - helper to return a PageRequest
    - common mistake : transposing the 2 parameters ( as they have same type and similar name )
  - refactor Spring Data JPA repositories
    - controller >> return a Page<BeerDTO> instead of a List<BeerDTO>  ( org.springframework.data.domain )
      - service  needs to updated as well, etc
          return new ArrayList<>(beerMap.values());
          return new PageImpl<>(new ArrayList<>(beerMap.values()));
    - repository :  Page<Beer>, NEW parameters : Pageable pageable null


SORTING
- e o idee buna a adauga sortare, pentru a putea mentine ordinea; s-ar putea sa returneze alte rezultate altfel ( depinzand de modul in care sunt stocate )
- tricky de testat
- parte din PageRequest, org.springframework.data.domain.Sort
     Sort sort = Sort.by(Sort.Order.asc("beerName"));
     return PageRequest.of(queryPageNumber, queryPageSize, sort);




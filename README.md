# utl_Read_paradox_database_files
SAS/R Importing a paradox database into SAS  inspired by https://goo.gl/f0UGW7 https://communities.sas.com/t5/Base-SAS-Programming/Importing-a-paradox-db-file-into-SAS/m-p/324832/highlight/false#M72138  SAS did a better job than R. Neither R nor SAS could convert Paradox 7 tables? Of the Paradox 4 and 5 SAS read 4 and R read 3

    ```  SAS/R Importing a paradox database into SAS                                                                                                                  ```
    ```                                                                                                                                                               ```
    ```  inspired by                                                                                                                                                  ```
    ```  https://goo.gl/f0UGW7                                                                                                                                        ```
    ```  https://communities.sas.com/t5/Base-SAS-Programming/Importing-a-paradox-db-file-into-SAS/m-p/324832/highlight/false#M72138                                   ```
    ```                                                                                                                                                               ```
    ```  SAS did a better job than R.                                                                                                                                 ```
    ```  Neither R nor SAS could convert Paradox 7 tables?                                                                                                            ```
    ```  Of the Paradox 4 and 5 SAS read 4 and R read 3                                                                                                               ```
    ```                                                                                                                                                               ```
    ```  * you can get a sample paradox database here;                                                                                                                ```
    ```  * these are good samples and have many variable types;                                                                                                       ```
    ```  http://www.sportamok.com/development/download;                                                                                                               ```
    ```                                                                                                                                                               ```
    ```  * usefull utility to get finames froom a directory;                                                                                                          ```
    ```  * Roland Rashleigh-Berry directory code, may he RIP;                                                                                                         ```
    ```  * the old guard is dying off;                                                                                                                                ```
    ```  * don't forget the blank after db;                                                                                                                           ```
    ```                                                                                                                                                               ```
    ```  * put the members of directory d:\db in macro var dbs;                                                                                                       ```
    ```  * macros on end;                                                                                                                                             ```
    ```  %let dbs= %utl_dir(d:\db\*.db );                                                                                                                             ```
    ```  %put &=dbs;                                                                                                                                                  ```
    ```                                                                                                                                                               ```
    ```  DBS=biolife.db  COUNTRIES.DB  country.db  events.db  FILMS.DB  venues.db                                                                                     ```
    ```                                                                                                                                                               ```
    ```  Uppercase Paradox 7 tables could not be read by SAS or R but Stattransfer could read them.                                                                   ```
    ```                                                                                                                                                               ```
    ```                                                                                                                                                               ```
    ```                                                                                                                                                               ```
    ```  HAVE A PARADOX DATABASE WITH THESE TABLES  (Paradox 4, 5 and 7)                                                                                              ```
    ```  ===============================================================                                                                                              ```
    ```                                                                                                                                                               ```
    ```  biolife.db                                                                                                                                                   ```
    ```  country.db   * R could not read this one but SAS could                                                                                                       ```
    ```  events.db                                                                                                                                                    ```
    ```  venues.db                                                                                                                                                    ```
    ```                                                                                                                                                               ```
    ```  WANT SAS DATSETS                                                                                                                                             ```
    ```  ================                                                                                                                                             ```
    ```                                                                                                                                                               ```
    ```  BIOLIFE.SAS7BDAT                                                                                                                                             ```
    ```  COUNTRY.SAS7BDAT                                                                                                                                             ```
    ```  EVENTS.SAS7BDAT                                                                                                                                              ```
    ```  VENUES.SAS7BDAT                                                                                                                                              ```
    ```                                                                                                                                                               ```
    ```                                                                                                                                                               ```
    ```  SAS SOLUTION (cannot read Paradox 7 tables, however Stat Transfer can)                                                                                       ```
    ```  ======================================================================                                                                                       ```
    ```  options validvarname=upcase;;                                                                                                                                ```
    ```  data _null_;                                                                                                                                                 ```
    ```                                                                                                                                                               ```
    ```    tables= "%utl_dir(d:\db\*.db )";                                                                                                                           ```
    ```    put tables=;                                                                                                                                               ```
    ```    do tbl=1 to countc(tables,'.');                                                                                                                            ```
    ```                                                                                                                                                               ```
    ```       table=scan(scan(tables,tbl,' '),1,'.');                                                                                                                 ```
    ```       * PARADOX 7;                                                                                                                                            ```
    ```       if upcase(table) not in ('COUNTRIES', 'FILMS', 'CARS' 'RESTTEMP') then do;                                                                              ```
    ```           call symputx('table',table);                                                                                                                        ```
    ```           put table=;                                                                                                                                         ```
    ```           rc=dosubl('                                                                                                                                         ```
    ```                proc import out=work.&table                                                                                                                    ```
    ```                file="d:/db/&table..db"                                                                                                                        ```
    ```                dbms=db replace;                                                                                                                               ```
    ```                run;quit;                                                                                                                                      ```
    ```              ');                                                                                                                                              ```
    ```       end;                                                                                                                                                    ```
    ```     end;                                                                                                                                                      ```
    ```                                                                                                                                                               ```
    ```  run;quit;                                                                                                                                                    ```
    ```                                                                                                                                                               ```
    ```                                                                                                                                                               ```
    ```  * As soon as R implements 'write_sas' this code will become                                                                                                  ```
    ```    very simple - the save will rePlaced by something like:                                                                                                    ```
    ```    for (table in tables) {write_sas(assign(table,sqlFetch(myDB, table),paste("d:/db/",table,".sas7bdat")};                                                    ```
    ```                                                                                                                                                               ```
    ```  However stattransfer and SAS seem to do a better job;                                                                                                        ```
    ```                                                                                                                                                               ```
    ```  Howver it is rather complex now;                                                                                                                             ```
    ```                                                                                                                                                               ```
    ```  * I don't have a 64 bit driver so I need to use                                                                                                              ```
    ```    the 32bit version of R, note R comes with both versions;                                                                                                   ```
    ```                                                                                                                                                               ```
    ```  * this works;                                                                                                                                                ```
    ```  * you may need 32bit SAS. I used 32bit R;                                                                                                                    ```
    ```  * create R dataframe from db table;                                                                                                                          ```
    ```  %macro todb(dummy);                                                                                                                                          ```
    ```                                                                                                                                                               ```
    ```   %let dbs=biolife.db country.db event.db venues.db;                                                                                                          ```
    ```                                                                                                                                                               ```
    ```    %do tbl=1 %to %sysfunc(countc(&dbs,%str(.)));                                                                                                              ```
    ```                                                                                                                                                               ```
    ```      %let table=%sysfunc(strip(%scan(%scan(&dbs.,&tbl.,%str( )),1,%str(.))));                                                                                 ```
    ```                                                                                                                                                               ```
    ```      %put &=table.;                                                                                                                                           ```
    ```                                                                                                                                                               ```
    ```      /* %let table=events;  */                                                                                                                                ```
    ```                                                                                                                                                               ```
    ```      * you need to use double quotes so macros and macro variables are resolved;                                                                              ```
    ```      %utl_submit_r32("                                                                                                                                        ```
    ```      source('C:/Program Files/R/R-3.3.2/etc/Rprofile.site', echo=T);                                                                                          ```
    ```      library(RODBC);                                                                                                                                          ```
    ```      library(sqldf);                                                                                                                                          ```
    ```      '&table';                                                                                                                                                ```
    ```      myDB<-odbcDriverConnect('Driver={Microsoft Paradox Driver (*.db )};DriverID=538;                                                                         ```
    ```      Fil=Paradox 5.X;DefaultDir=d:/db/;Dbq=d:/db/;CollatingSequence=ASCII');                                                                                  ```
    ```      '&table';                                                                                                                                                ```
    ```      &table <- sqlFetch(myDB, '&table.');                                                                                                                     ```
    ```      '&table';                                                                                                                                                ```
    ```      save(&table,file='d:/rds/&table..rda', compress = FALSE);                                                                                                ```
    ```      &table;                                                                                                                                                  ```
    ```      close(myDB);                                                                                                                                             ```
    ```      ");                                                                                                                                                      ```
    ```                                                                                                                                                               ```
    ```      %utl_submit_wps64("                                                                                                                                      ```
    ```      options set=R_HOME 'C:/Program Files/R/R-3.3.2';                                                                                                         ```
    ```      libname wrk '%sysfunc(pathname(work))';                                                                                                                  ```
    ```      proc r;                                                                                                                                                  ```
    ```      submit;                                                                                                                                                  ```
    ```      source('C:/Program Files/R/R-3.3.2/etc/Rprofile.site', echo=T);                                                                                          ```
    ```      load(file = 'd:/rds/&table..rda');                                                                                                                       ```
    ```      &table.;                                                                                                                                                 ```
    ```      endsubmit;                                                                                                                                               ```
    ```      import r=&table. data=wrk.&table.;                                                                                                                       ```
    ```      run;quit;                                                                                                                                                ```
    ```                                                                                                                                                               ```
    ```      ");                                                                                                                                                      ```
    ```    %end;                                                                                                                                                      ```
    ```                                                                                                                                                               ```
    ```  %mend todb;                                                                                                                                                  ```
    ```                                                                                                                                                               ```
    ```  %todb;                                                                                                                                                       ```
    ```                                                                                                                                                               ```
    ```                                                                                                                                                               ```
    ```                                                                                                                                                               ```


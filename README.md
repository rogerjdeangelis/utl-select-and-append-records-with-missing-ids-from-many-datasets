# utl-select-and-append-records-with-missing-ids-from-many-datasets
Select and append records with missing ids from many datasets

    Select and append records with missing ids from many datasets                                                                   
                                                                                                                                    
      Two additional solutions that package the code inside one datastep                                                            
                                                                                                                                    
          a. Get meta data at datastep compilation time, ie if _n_=0.                                                               
          b. get meta data inside set statement ast compile time                                                                    
                                                                                                                                    
    SAS Forum                                                                                                                       
    https://tinyurl.com/tjjpzua                                                                                                     
    https://github.com/rogerjdeangelis/utl-select-and-append-records-with-missing-ids-from-many-datasets                            
                                                                                                                                    
    github                                                                                                                          
    https://tinyurl.com/rc63ch9                                                                                                     
    https://communities.sas.com/t5/SAS-Programming/How-to-pass-the-dataset-name-in-set-statement-from-a-variable/m-p/604436         
                                                                                                                                    
    *_                   _                                                                                                          
    (_)_ __  _ __  _   _| |_                                                                                                        
    | | '_ \| '_ \| | | | __|                                                                                                       
    | | | | | |_) | |_| | |_                                                                                                        
    |_|_| |_| .__/ \__,_|\__|                                                                                                       
            |_|                                                                                                                     
    ;                                                                                                                               
                                                                                                                                    
    * meta data;                                                                                                                    
    data dsn ;                                                                                                                      
      input dsn $32. ;                                                                                                              
      cards ;                                                                                                                       
    customer                                                                                                                        
    company                                                                                                                         
    employee                                                                                                                        
    ;                                                                                                                               
    run ;                                                                                                                           
                                                                                                                                    
    * observation data;                                                                                                             
    data customer company employee ;                                                                                                
      do id = 11, . , 12, 13 ; output customer ; end ;                                                                              
      do id = 21, 22, 23, 24 ; output company  ; end ;                                                                              
      do id = 31, . , 23, .  ; output employee ; end ;                                                                              
      retain data1 1 data2 2 data3 3 ;                                                                                              
    run ;                                                                                                                           
                                                                                                                                    
    WORK.DSN total obs=3                                                                                                            
                                                                                                                                    
    Obs      DSN                                                                                                                    
                                                                                                                                    
     1     customer                                                                                                                 
     2     company                                                                                                                  
     3     employee                                                                                                                 
                                                                                                                                    
    WORK.CUSTOMER total obs=4                                                                                                       
                                                                                                                                    
    Obs    ID    DATA1    DATA2    DATA3                                                                                            
                                                                                                                                    
     1     11      1        2        3                                                                                              
     2      .      1        2        3                                                                                              
     3     12      1        2        3                                                                                              
     4     13      1        2        3                                                                                              
                                                                                                                                    
    WORK.COMPANY total obs=4                                                                                                        
                                                                                                                                    
    Obs    ID    DATA1    DATA2    DATA3                                                                                            
                                                                                                                                    
     1     21      1        2        3                                                                                              
     2     22      1        2        3                                                                                              
     3     23      1        2        3                                                                                              
     4     24      1        2        3                                                                                              
                                                                                                                                    
    WORK.EMPLOYEE total obs=4                                                                                                       
                                                                                                                                    
    Obs    ID    DATA1    DATA2    DATA3                                                                                            
                                                                                                                                    
     1     31      1        2        3                                                                                              
     2      .      1        2        3                                                                                              
     3     23      1        2        3                                                                                              
     4      .      1        2        3                                                                                              
                                                                                                                                    
    *           _                                                                                                                   
     _ __ _   _| | ___                                                                                                              
    | '__| | | | |/ _ \                                                                                                             
    | |  | |_| | |  __/                                                                                                             
    |_|   \__,_|_|\___|                                                                                                             
                                                                                                                                    
    ;                                                                                                                               
                                                                                                                                    
    Append just the the records with missing IDS from datasets customer, company, and employee                                      
                                                                                                                                    
    *            _               _                                                                                                  
      ___  _   _| |_ _ __  _   _| |_                                                                                                
     / _ \| | | | __| '_ \| | | | __|                                                                                               
    | (_) | |_| | |_| |_) | |_| | |_                                                                                                
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                               
                    |_|                                                                                                             
    ;                                                                                                                               
                                                                                                                                    
                                                                                                                                    
    Up to 40 obs WORK.MISS total obs=3                                                                                              
                                                                                                                                    
    Obs    ID    DATA1    DATA2    DATA3       DSNAME                                                                               
                                                                                                                                    
     1      .      1        2        3      WORK.CUSTOMER                                                                           
     2      .      1        2        3      WORK.EMPLOYEE                                                                           
     3      .      1        2        3      WORK.EMPLOYEE                                                                           
                                                                                                                                    
    *                                                                                                                               
     _ __  _ __ ___   ___ ___  ___ ___                                                                                              
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|                                                                                             
    | |_) | | | (_) | (_|  __/\__ \__ \                                                                                             
    | .__/|_|  \___/ \___\___||___/___/                                                                                             
    |_|                                                                                                                             
    ;                                                                                                                               
                                                                                                                                    
    *                                                                                                                               
      __ _                                                                                                                          
     / _` |                                                                                                                         
    | (_| |                                                                                                                         
     \__,_|                                                                                                                         
                                                                                                                                    
    ;                                                                                                                               
    * package in one datastep;                                                                                                      
                                                                                                                                    
    data want ;                                                                                                                     
      if _n_=0 then do; %let rc=%sysfunc(dosubl('                                                                                   
        proc sql;select dsn into :dsn separated by " " from dsn;quit;'));                                                           
        '));                                                                                                                        
      end;                                                                                                                          
      set &dsn indsname = dsn ;                                                                                                     
      where cmiss (id) ;                                                                                                            
      DSNAME = dsn ;                                                                                                                
    run ;                                                                                                                           
                                                                                                                                    
    *_                                                                                                                              
    | |__                                                                                                                           
    | '_ \                                                                                                                          
    | |_) |                                                                                                                         
    |_.__/                                                                                                                          
                                                                                                                                    
    ;                                                                                                                               
                                                                                                                                    
    data want ;                                                                                                                     
      set %let rc=%sysfunc(dosubl('                                                                                                 
        proc sql;select dsn into :dsn separated by " " from dsn;quit;')); &dsn                                                      
        indsname = dsn ;                                                                                                            
      where cmiss (id) ;                                                                                                            
      DSNAME = dsn ;                                                                                                                
    run ;                                                                                                                           
                                                                                                                                    
    *_                                                                                                                              
    | | ___   __ _                                                                                                                  
    | |/ _ \ / _` |                                                                                                                 
    | | (_) | (_| |                                                                                                                 
    |_|\___/ \__, |                                                                                                                 
             |___/                                                                                                                  
    ;                                                                                                                               
                                                                                                                                    
    NOTE: There were 1 observations read from the data set WORK.CUSTOMER.                                                           
          WHERE CMISS(id);                                                                                                          
    NOTE: There were 0 observations read from the data set WORK.COMPANY.                                                            
          WHERE CMISS(id);                                                                                                          
    NOTE: There were 2 observations read from the data set WORK.EMPLOYEE.                                                           
          WHERE CMISS(id);                                                                                                          
    NOTE: The data set WORK.WANT has 3 observations and 5 variables.                                                                
    NOTE: DATA statement used (Total process time):                                                                                 
          real time           0.10 seconds                                                                                          
                                                                                                                                    

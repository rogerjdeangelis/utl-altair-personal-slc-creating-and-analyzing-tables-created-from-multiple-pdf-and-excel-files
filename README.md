# utl-altair-personal-slc-creating-and-analyzing-tables-created-from-multiple-pdf-and-excel-files
Altair personal slc creating and analyzing tables created from multiple pdf and excel files
    %let pgm=utl-altair-personal-slc-creating-and-analyzing-tables-created-from-multiple-pdf-and-excel-files;

    %stop_submission;

    Altair personal slc creating and analyzing tables created from multiple pdf and excel files

    Too long to post on listserv, see github

    github
    https://github.com/rogerjdeangelis/utl-altair-personal-slc-creating-and-analyzing-tables-created-from-multiple-pdf-and-excel-files

    community.altair.com
    https://community.altair.com/discussion/38559/monarch-learning-series-2023-exercise-9-ap-expense-report-analysis#latest

    Due to a bug in sqlite passthru you need to run some solutions using sqlite code in either R or Python
    Proc sql does not support some common sql functions like partition.

    PROBLEM
    =======

    You are an Accounts Payable Accountant at a Software Company and you need
    to analyze 3 months worth of expense reports to answer the following questions:

    What is the Total Expense in USD for the 3 Expense Reports (2016-12, 2017-01, 2017-02)?
    (HINT: You will need to convert any expenses not in USD by using the Currency Rate Pair.xlsx file
    and creating Formula field for the USD rate for the non-USD expenses)


    Who are the Top 3 Employees that spent the most on any Expense that contains
           "Travel"
           "Taxi"
           "Airfare"
           "Airline"
           "Hotel"
           "Lunch"
           "Breakfast"
           "Dinner"

    CONTENTS
    ========

       1 create table expenses
       2 create table pairs (for exchange rates)
       3 create table exchange adjusting dollars

       Once you have these tables the solution is straightforward

       4 Total accounts payable
         select sum(equiv) from exchange

       5 Top 3 Employees that spent the most on any Expense that contains
         "Travel","Taxi","Airfare","Airline","Hotel","Lunch","Breakfast","Dinner"

         want<- sqldf('
              with totalexpenses as (
              select
                name,
                itm,
                sum(equiv) as total_exp
              from exchange
              where itm in ("Travel","Taxi","Airfare","Airline","Hotel","Lunch","Breakfast","Dinner")
              group by name, itm
            ),
            rankedexpenses as (
              select
                name,
                itm,
                total_exp,
                row_number() over (partition by itm order by total_exp desc) as rank
              from totalexpenses
            )
            select name, itm, total_exp
            from rankedexpenses
            where rank <= 3
            order by itm, rank
            ')

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    You are an Accounts Payable Accountant at a Software Company and you need
    to analyze 3 months worth of expense reports to answer the following questions:

    What is the Total Expense in USD for the 3 Expense Reports (2016-12, 2017-01, 2017-02)?
    (HINT: You will need to convert any expenses not in USD by using the Currency Rate Pair.xlsx file
    and creating Formula field for the USD rate for the non-USD expenses)


    Who are the Top 3 Employees that spent the most on any Expense that contains
           "Travel"
           "Taxi"
           "Airfare"
           "Airline"
           "Hotel"
           "Lunch"
           "Breakfast"
           "Dinner"


    ONCE YOU HAVE CREATED THESE TABLES IN ANY DATABASE OR EXCEL THE SOLUTIONS ARE STRAIGHT FORWARD

    /******************************************************************************************************************************/
    /* TABLE PAIRS                               |    TABLE EXCHANGE                                                              */
    /*                                           |                                                      CURRENCY                  */
    /*   PAIR   BASE CURRENCY BASE__1  PRICE     |             NAME             EXP    CUR      ITM       PRICE  CURRENCY EQUIV   */
    /*                                           |                                                                                */
    /*  USD-EGP USD    EGP       1       30.85   |  MEARS, SHANNON (DW1032826)  31.38  EUR Lunch          .8905      EUR  35.239  */
    /*  USD-PHP USD    PHP       1       54.59   |  MEARS, SHANNON (DW1032826) 105.92  EUR Internet       .8905      EUR 118.944  */
    /*  USD-CLP USD    CLP       1      815.05   |  MEARS, SHANNON (DW1032826)  45.77  EUR Entertainment  .8905      EUR  51.398  */
    /*  USD-PKR USD    PKR       1      282.75   |  MEARS, SHANNON (DW1032826)  36.03  EUR Lunch          .8905      EUR  40.460  */
    /*  USD-IQD USD    IQD       1     1309.00   |  MEARS, SHANNON (DW1032826)  30.23  EUR Lunch          .8905      EUR  33.947  */
    /*  ....                                     |  ....                                                                          */
    /******************************************************************************************************************************/

    SOLUTIONS

      1  Total Accounts Payable for 3 months

         Altair SLC

          TOTEXP
         --------
         226442.8

         proc sql;
           select
             sum(equiv) as totExp
           from
             exchange
         ;quit;


     2  Top 3 Employees that spent the most on any Expense that contains
        "Travel","Taxi","Airfare","Airline","Hotel","Lunch","Breakfast","Dinner"

        Altair SLC

                     NAME                    ITM       TOTAL_EXP

        CATZ, LAURENCE (DW1030298)        Airfare       3098.80
        CONNEALY, TYLER (DW0921188)       Airfare       2729.48
        MAICO, REKO (DW0386388)           Airfare       1733.61

        BALL, GUY (DW0835689)             Airline        164.68
        KNOOP, JEFF (DW0767037)           Airline         99.02
        CRAIG, ROSE (DW0357148)           Airline         93.74

        SANDERSON, MICHAEL (DW0231288)    Breakfast      441.45
        SEPHERS, GEOFF (DW0355310)        Breakfast      307.85
        CAVANAGH, NATHAN (DW0423887)      Breakfast      292.51

        HO, JON (DW0170296)               Dinner        1781.82
        SEPHERS, GEOFF (DW0355310)        Dinner        1179.65
        SANDERSON, MICHAEL (DW0231288)    Dinner         956.91

        PRINCE, KYLE (DW1292453)          Hotel         2410.33
        CONNEALY, TYLER (DW0921188)       Hotel         2208.08
        PENERA, HEATH (DW0332752)         Hotel         1892.71

        MEARS, SHANNON (DW1032826)        Lunch         1647.05
        SEPHERS, GEOFF (DW0355310)        Lunch         1568.62
        SANDERSON, MICHAEL (DW0231288)    Lunch         1396.57

        MAICO, REKO (DW0386388)           Taxi          5871.38
        BERRETT, SHAUN (DW0147143)        Taxi          3913.01
        WILLIAMS, VAN (DW1071325)         Taxi          2808.35

        SIPES, LANCE (DW0741723)          Travel         977.30
        METOYER, MICHELLE (DW0744874)     Travel         676.20
        GONZALEZ, YVETTE (DW0266104)      Travel         542.78


        /*--- same code in python ---*/
        want<-sqldf('
        with totalexpenses as (
          select
            name,
            itm,
            sum(equiv) as total_exp
          from exchange
          where itm in ("Travel","Taxi","Airfare","Airline","Hotel","Lunch","Breakfast","Dinner")
          group by name, itm
        ),
        rankedexpenses as (
          select
            name,
            itm,
            total_exp,
            row_number() over (partition by itm order by total_exp desc) as rank
          from totalexpenses
        )
        select name, itm, total_exp
        from rankedexpenses
        where rank <= 3
        order by itm, rank

    /*                      _         _        _     _
    / |  ___ _ __ ___  __ _| |_ ___  | |_ __ _| |__ | | ___   _____  ___ __   ___ _ __  ___  ___  ___
    | | / __| `__/ _ \/ _` | __/ _ \ | __/ _` | `_ \| |/ _ \ / _ \ \/ / `_ \ / _ \ `_ \/ __|/ _ \/ __|
    | || (__| | |  __/ (_| | ||  __/ | || (_| | |_) | |  __/|  __/>  <| |_) |  __/ | | \__ \  __/\__ \
    |_| \___|_|  \___|\__,_|\__\___|  \__\__,_|_.__/|_|\___| \___/_/\_\ .__/ \___|_| |_|___/\___||___/
                                                                      |_|
    */
     Note columh equiv has equivalent dollars

    */

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

     d:/pdf/Expense Report  2017-01.pdf
     d:/pdf/Expense Report  2017-02.pdf
     d:/pdf/Expense Report  2016-12.pdf

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    /*--- pdfs to tet append and parse creating table expenses ---*/

    proc datasets lib=work nodetails nolist;
     delete expense;
    run;quit;

    /*--- covert pdf to text ---*/
    &_init_;
    proc r;
    submit;
    library(pdftools)

    pdf_text_content <- pdf_text("d:/pdf/Expense Report  2017-01.pdf")
    all_text <- paste(pdf_text_content, collapse = " ")
    writeLines(all_text, "d:/txt/y2017_01.txt")

    pdf_text_content <- pdf_text("d:/pdf/Expense Report  2017-02.pdf")
    all_text <- paste(pdf_text_content, collapse = " ")
    writeLines(all_text, "d:/txt/y2017_01.txt")

    pdf_text_content <- pdf_text("d:/pdf/Expense Report  2016-12.pdf")
    all_text <- paste(pdf_text_content, collapse = " ")
    writeLines(all_text, "d:/txt/y2016_12.txt")

    endsubmit;
    run;quit;

    filename all3 ('d:/txt/y2017_01.txt' 'd:/txt/y2017_02.txt' 'd:/txt/y2016_12.txt');

    data expenses;
      retain name;
      infile all3;
      input ;
      if _infile_=:'Employee Name' then name=left(scan(_infile_,2,':'));
      if
          index(_infile_,'16 Operations') or index(_infile_,'17 Operations') or
          index(_infile_,'16 Sales') or index(_infile_,'17 Sales');
      exp=input(scan(_infile_,3,' '),comma12.2);
      cur=scan(_infile_,4,'/ ');
      itm=scan(_infile_,5,'/ ');

    run;quit;

    proc print data=expenses(obs=5);
    run;quit;

    proc freq data=expenses;
    tables cur;
    run;

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    Altair SLC

    Obs               NAME                 EXP      CUR     ITM

     1     GIELSKI, JESSIE (DW0188511)    158.03    USD    Fuel
     2     GIELSKI, JESSIE (DW0188511)    307.79    USD    Car
     3     GIELSKI, JESSIE (DW0188511)    136.83    USD    Dinner
     4     GIELSKI, JESSIE (DW0188511)    405.49    USD    Hotel
     5     GIELSKI, JESSIE (DW0188511)     90.45    USD    Lunch
     ....

    /*
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    */
    1057      ODS _ALL_ CLOSE;
    1058      ODS LISTING;
    1059      FILENAME WBGSF 'd:\wpswrk\_TD9616/listing_images';
    1060      OPTIONS DEVICE=GIF;
    1061      GOPTIONS GSFNAME=WBGSF;
    1062
    1063
    1064      filename all3 ('d:/txt/y2017_01.txt' 'd:/txt/y2017_01.txt' 'd:/txt/y2016_12.txt');
    1065
    1066      data expenses;
    1067        retain name;
    1068        infile all3;
    1069        input ;
    1070        if _infile_=:'Employee Name' then name=left(scan(_infile_,2,':'));
    1071        if index(_infile_,'16 Operations') or index(_infile_,'17 Operations');
    1072        exp=input(scan(_infile_,3,' '),comma12.2);
    1073        cur=scan(_infile_,4,'/ ');
    1074        itm=scan(_infile_,5,'/ ');
    1075
    1076      run;

    NOTE: The infile all3 is:
          Filename='d:\txt\y2017_01.txt',
          Owner Name=T7610\Roger,
          File size (bytes)=71868,
          Create Time=08:02:34 Oct 29 2025,
          Last Accessed=10:14:19 Oct 29 2025,
          Last Modified=08:02:34 Oct 29 2025,
          Lrecl=32767, Recfm=V

    NOTE: The infile all3 is:
          Filename='d:\txt\y2017_01.txt',
          Owner Name=T7610\Roger,
          File size (bytes)=71868,
          Create Time=08:02:34 Oct 29 2025,
          Last Accessed=10:15:57 Oct 29 2025,
          Last Modified=08:02:34 Oct 29 2025,
          Lrecl=32767, Recfm=V

    NOTE: The infile all3 is:
          Filename='d:\txt\y2016_12.txt',
          Owner Name=T7610\Roger,
          File size (bytes)=76211,
          Create Time=08:02:34 Oct 29 2025,
          Last Accessed=10:14:19 Oct 29 2025,
          Last Modified=08:02:34 Oct 29 2025,
          Lrecl=32767, Recfm=V

    NOTE: 1972 records were read from file all3
          The minimum record length was 0
          The maximum record length was 93
    NOTE: 1972 records were read from file all3
          The minimum record length was 0
          The maximum record length was 93
    NOTE: 1997 records were read from file all3
          The minimum record length was 0
          The maximum record length was 96
    NOTE: Data set "WORK.expenses" has 666 observation(s) and 4 variable(s)
    NOTE: The data step took :
          real time : 0.011
          cpu time  : 0.015


    1076    !     quit;
    1077
    1078      &_init_;
    1079      proc r;
    NOTE: Using R version 4.5.1 (2025-06-13 ucrt) from d:\r451
    1080      export data=expenses r=expenses;
    NOTE: Creating R data frame 'expenses' from data set 'WORK.expenses'

    1081      submit;
    1082      str(expenses)
    1083      library(sqldf)
    1084      options(sqldf.dll = "d:/dll/sqlean.dll")
    1085      want<-sqldf("
    1086      WITH TotalExpenses AS (
    1087        SELECT
    1088          name,
    1089          ITM,
    1090          SUM(EXP) AS Total_EXP
    1091        FROM expenses
    1092        WHERE ITM IN ('Dinner', 'Taxi')
    1093        GROUP BY name, ITM
    1094      ),
    1095      RankedExpenses AS (
    1096        SELECT
    1097          name,
    1098          ITM,
    1099          Total_EXP,
    1100          ROW_NUMBER() OVER (PARTITION BY ITM ORDER BY Total_EXP DESC) AS rank
    1101        FROM TotalExpenses
    1102      )
    1103      SELECT name, ITM, Total_EXP
    1104      FROM RankedExpenses
    1105      WHERE rank <= 2
    1106      ORDER BY ITM, rank
    1107      ")
    1108      endsubmit;

    NOTE: Submitting statements to R:

    > str(expenses)
    Loading required package: gsubfn
    Loading required package: proto
    Loading required package: RSQLite
    > library(sqldf)
    > options(sqldf.dll = "d:/dll/sqlean.dll")
    > want<-sqldf("
    + WITH TotalExpenses AS (
    +   SELECT
    +     name,
    +     ITM,
    +     SUM(EXP) AS Total_EXP
    +   FROM expenses
    +   WHERE ITM IN ('Dinner', 'Taxi')
    +   GROUP BY name, ITM
    + ),
    + RankedExpenses AS (
    +   SELECT
    +     name,
    +     ITM,
    +     Total_EXP,
    +     ROW_NUMBER() OVER (PARTITION BY ITM ORDER BY Total_EXP DESC) AS rank
    +   FROM TotalExpenses
    + )
    + SELECT name, ITM, Total_EXP
    + FROM RankedExpenses
    + WHERE rank <= 2
    + ORDER BY ITM, rank

    NOTE: Processing of R statements complete

    + ")
    1109      import data=want r=want;
    NOTE: Creating data set 'WORK.want' from R data frame 'want'
    NOTE: Column names modified during import of 'want'
    NOTE: Data set "WORK.want" has 4 observation(s) and 3 variable(s)

    1110      run;quit;
    NOTE: Procedure r step took :
          real time : 1.398
          cpu time  : 0.015


    1111
    1112      proc print data=want;
    1113      run;
    NOTE: 4 observations were read from "WORK.want"
    NOTE: Procedure print step took :
          real time : 0.016
          cpu time  : 0.000


    1114
    1115
    1116
    1117      quit; run;
    1118      ODS _ALL_ CLOSE;
    1119      FILENAME WBGSF CLEAR;

    /*___                        _         _        _     _
    |___ \    ___ _ __ ___  __ _| |_ ___  | |_ __ _| |__ | | ___
      __) |  / __| `__/ _ \/ _` | __/ _ \ | __/ _` | `_ \| |/ _ \
     / __/  | (__| | |  __/ (_| | ||  __/ | || (_| | |_) | |  __/
    |_____|  \___|_|  \___|\__,_|\__\___|  \__\__,_|_.__/|_|\___|

                 _             __                                         _
     _ __   __ _(_)_ __ ___   / _|_ __ ___  _ __ ___     _____  _____ ___| |
    | `_ \ / _` | | `__/ __| | |_| `__/ _ \| `_ ` _ \   / _ \ \/ / __/ _ \ |
    | |_) | (_| | | |  \__ \ |  _| | | (_) | | | | | | |  __/>  < (_|  __/ |
    | .__/ \__,_|_|_|  |___/ |_| |_|  \___/|_| |_| |_|  \___/_/\_\___\___|_|
    |_|
    */


    &_init_;

    proc datasets lib=work nodetails nolist;
     delete pairs;
    run;quit;

    libname xls xlsx "d:/xls/Currency Rate Pairs.xlsx";

    data pairs;
      set xls.'Expense Currency Pairs$'n;
    run;quit;

    libname xls clear;

    proc print data=pairs(obs=5);
    run;quit;

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    TABLE PAIRS

    Altair SLC

    Obs               NAME                 EXP      CUR     ITM

     1     GIELSKI, JESSIE (DW0188511)    158.03    USD    Fuel
     2     GIELSKI, JESSIE (DW0188511)    307.79    USD    Car
     3     GIELSKI, JESSIE (DW0188511)    136.83    USD    Dinner
     4     GIELSKI, JESSIE (DW0188511)    405.49    USD    Hotel
     5     GIELSKI, JESSIE (DW0188511)     90.45    USD    Lunch
     ....

    /*
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    */

    1710      ODS _ALL_ CLOSE;
    1711      ODS LISTING;
    1712      FILENAME WBGSF 'd:\wpswrk\_TD9616/listing_images';
    1713      OPTIONS DEVICE=GIF;
    1714      GOPTIONS GSFNAME=WBGSF;
    1715      /*--- covert pdf to text ---*/
    1716      &_init_;
    1717      proc r;
    1718      submit;
    1719      library(pdftools)
    1720
    1721      pdf_text_content <- pdf_text("d:/pdf/Expense Report  2017-01.pdf")
    1722      all_text <- paste(pdf_text_content, collapse = " ")
    1723      writeLines(all_text, "d:/txt/y2017_01.txt")
    1724
    1725      pdf_text_content <- pdf_text("d:/pdf/Expense Report  2017-02.pdf")
    1726      all_text <- paste(pdf_text_content, collapse = " ")
    1727      writeLines(all_text, "d:/txt/y2017_01.txt")
    1728
    1729      pdf_text_content <- pdf_text("d:/pdf/Expense Report  2016-12.pdf")
    1730      all_text <- paste(pdf_text_content, collapse = " ")
    1731      writeLines(all_text, "d:/txt/y2016_12.txt")
    1732
    1733      endsubmit;
    NOTE: Using R version 4.5.1 (2025-06-13 ucrt) from d:\r451

    NOTE: Submitting statements to R:

    Using poppler version 25.05.0
    > library(pdftools)
    >
    > pdf_text_content <- pdf_text("d:/pdf/Expense Report  2017-01.pdf")
    > all_text <- paste(pdf_text_content, collapse = " ")
    > writeLines(all_text, "d:/txt/y2017_01.txt")
    >
    > pdf_text_content <- pdf_text("d:/pdf/Expense Report  2017-02.pdf")
    > all_text <- paste(pdf_text_content, collapse = " ")
    > writeLines(all_text, "d:/txt/y2017_01.txt")
    >
    > pdf_text_content <- pdf_text("d:/pdf/Expense Report  2016-12.pdf")
    > all_text <- paste(pdf_text_content, collapse = " ")
    > writeLines(all_text, "d:/txt/y2016_12.txt")

    NOTE: Processing of R statements complete

    >
    1734      run;quit;
    NOTE: Procedure r step took :
          real time : 0.842
          cpu time  : 0.000


    1735
    1736      filename all3 ('d:/txt/y2017_01.txt' 'd:/txt/y2017_02.txt' 'd:/txt/y2016_12.txt');
    1737
    1738      data expenses;
    1739        retain name;
    1740        infile all3;
    1741        input ;
    1742        if _infile_=:'Employee Name' then name=left(scan(_infile_,2,':'));
    1743        if
    1744            index(_infile_,'16 Operations') or index(_infile_,'17 Operations') or
    1745            index(_infile_,'16 Sales') or index(_infile_,'17 Sales');
    1746        exp=input(scan(_infile_,3,' '),comma12.2);
    1747        cur=scan(_infile_,4,'/ ');
    1748        itm=scan(_infile_,5,'/ ');
    1749
    1750      run;

    NOTE: The infile all3 is:
          Filename='d:\txt\y2017_01.txt',
          Owner Name=T7610\Roger,
          File size (bytes)=85328,
          Create Time=08:02:34 Oct 29 2025,
          Last Accessed=12:17:55 Oct 29 2025,
          Last Modified=12:17:55 Oct 29 2025,
          Lrecl=32767, Recfm=V

    NOTE: The infile all3 is:
          Filename='d:\txt\y2017_02.txt',
          Owner Name=T7610\Roger,
          File size (bytes)=85328,
          Create Time=08:02:34 Oct 29 2025,
          Last Accessed=12:16:08 Oct 29 2025,
          Last Modified=08:02:34 Oct 29 2025,
          Lrecl=32767, Recfm=V

    NOTE: The infile all3 is:
          Filename='d:\txt\y2016_12.txt',
          Owner Name=T7610\Roger,
          File size (bytes)=76211,
          Create Time=08:02:34 Oct 29 2025,
          Last Accessed=12:17:55 Oct 29 2025,
          Last Modified=12:17:55 Oct 29 2025,
          Lrecl=32767, Recfm=V

    NOTE: 2223 records were read from file all3
          The minimum record length was 0
          The maximum record length was 92
    NOTE: 2223 records were read from file all3
          The minimum record length was 0
          The maximum record length was 92
    NOTE: 1997 records were read from file all3
          The minimum record length was 0
          The maximum record length was 96
    NOTE: Data set "WORK.expenses" has 1692 observation(s) and 4 variable(s)
    NOTE: The data step took :
          real time : 0.015
          cpu time  : 0.015


    1750    !     quit;
    1751
    1752      proc print data=expenses(obs=5);
    1753      run;quit;
    NOTE: 5 observations were read from "WORK.expenses"
    NOTE: Procedure print step took :
          real time : 0.024
          cpu time  : 0.000


    1754
    1755      quit; run;
    1756      ODS _ALL_ CLOSE;
    1757      FILENAME WBGSF CLEAR;


    /*____                       _         _        _     _                     _
    |___ /    ___ _ __ ___  __ _| |_ ___  | |_ __ _| |__ | | ___   _____  _____| |__   __ _ _ __   __ _  ___
      |_ \   / __| `__/ _ \/ _` | __/ _ \ | __/ _` | `_ \| |/ _ \ / _ \ \/ / __| `_ \ / _` | `_ \ / _` |/ _ \
     ___) | | (__| | |  __/ (_| | ||  __/ | || (_| | |_) | |  __/|  __/>  < (__| | | | (_| | | | | (_| |  __/
    |____/   \___|_|  \___|\__,_|\__\___|  \__\__,_|_.__/|_|\___| \___/_/\_\___|_| |_|\__,_|_| |_|\__, |\___|
                                                                                                  |___/
    ALSO CREAT SUM OF ALL EXPENSES
    ------------------------------

    */

    &_init_;

    proc datasets lib=work nodetails nolist;
     delete exchange;
    run;quit;

    proc sql;
      create
        table exchange as
      select
         l.*
        ,r.currency_price
        ,r.currency
        ,case
           when cur='USD' then 1*exp
           else exp/currency_price
          end as equiv
      from
        expenses as l left join pairs as r
      on
       l.cur = r.currency
    ;quit;

    proc print data=exchange(obs=5);
    run;quit;

    proc sql;
      select
        sum(equiv) as totExp
     from
        exchange
    ;quit;

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

     Altair SLC

        TOTEXP
      --------
      3623.247


    TABLE EXCHANGE

    Altair SLC

    Obs               NAME                EXP      CUR         ITM         CURRENCY_PRICE    CURRENCY    EQUIV

    184    HOGAN, JAMES (DW1220883)       39.47    GBP    Office               .76707          GBP        51.46
    185    CATZ, LAURENCE (DW1030298)     30.50    GBP    Taxi                 .76707          GBP        39.76
    186    VOILES, JEFF (DW1910657)       28.11    GBP    Taxi                 .76707          GBP        36.65
    187    WANG, FRANKLIN (DW1623033)     32.50    GBP    Taxi                 .76707          GBP        42.37
    188    VOILES, JEFF (DW1910657)       67.69    GBP    Taxi                 .76707          GBP        88.24
    189    VOILES, JEFF (DW1910657)       14.61    GBP    Entertainment        .76707          GBP        19.05

    /*
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    */

    2059      ODS _ALL_ CLOSE;
    2060      ODS LISTING;
    2061      FILENAME WBGSF 'd:\wpswrk\_TD9616/listing_images';
    2062      OPTIONS DEVICE=GIF;
    2063      GOPTIONS GSFNAME=WBGSF;
    2064
    2065      &_init_;
    2066
    2067      proc datasets lib=work nodetails nolist;
    2068       delete exchange;
    2069      run;quit;
    NOTE: Deleting "WORK.EXCHANGE" (memtype="DATA")
    NOTE: Procedure datasets step took :
          real time : 0.001
          cpu time  : 0.000


    2070
    2071      proc sql;
    2072        create
    2073          table exchange as
    2074        select
    2075           l.*
    2076          ,r.currency_price
    2077          ,r.currency
    2078          ,case
    2079             when cur='USD' then 1*exp
    2080             else exp/currency_price
    2081            end as equiv
    2082        from
    2083          expenses as l left join pairs as r
    2084        on
    2085         l.cur = r.currency
    2086      ;quit;
    NOTE: Data set "WORK.exchange" has 1692 observation(s) and 7 variable(s)
    NOTE: Procedure sql step took :
          real time : 0.107
          cpu time  : 0.062


    2087
    2088      proc print data=exchange(obs=5);
    2089      run;quit;
    NOTE: 5 observations were read from "WORK.exchange"
    NOTE: Procedure print step took :
          real time : 0.014
          cpu time  : 0.000


    2090
    2091      proc sql;
    2092        select
    2093          sum(equiv) as totExp
    2094       from
    2095          exchange
    2096      ;quit;
    NOTE: Procedure sql step took :
          real time : 0.020
          cpu time  : 0.000


    2097      quit; run;
    2098      ODS _ALL_ CLOSE;
    2099      FILENAME WBGSF CLEAR;


    /*  _     _                _____   _
    | || |   | |_ ___  _ __   |___ /  | |__  _   _   _____  ___ __   ___ _ __  ___  ___    __ _ _ __ ___  _   _ _ __
    | || |_  | __/ _ \| `_ \    |_ \  | `_ \| | | | / _ \ \/ / `_ \ / _ \ `_ \/ __|/ _ \  / _` | `__/ _ \| | | | `_ \
    |__   _| | || (_) | |_) |  ___) | | |_) | |_| ||  __/>  <| |_) |  __/ | | \__ \  __/ | (_| | | | (_) | |_| | |_) |
       |_|    \__\___/| .__/  |____/  |_.__/ \__, | \___/_/\_\ .__/ \___|_| |_|___/\___|  \__, |_|  \___/ \__,_| .__/
                      |_|                    |___/           |_|                          |___/                |_|
    */

    proc datasets lib=work nolist nodetails;
     delete want;
    run;quit;

    &_init_;
    proc datasets lib=work nolist nodetails;
     delete want;
    run;quit;

    proc r;
    export data=exchange r=exchange;
    submit;
    library(sqldf)
    options(sqldf.dll = "d:/dll/sqlean.dll")
    want<-sqldf('
    with totalexpenses as (
      select
        name,
        itm,
        sum(equiv) as total_exp
      from exchange
      where itm in ("Travel","Taxi","Airfare","Airline","Hotel","Lunch","Breakfast","Dinner")
      group by name, itm
    ),
    rankedexpenses as (
      select
        name,
        itm,
        total_exp,
        row_number() over (partition by itm order by total_exp desc) as rank
      from totalexpenses
    )
    select name, itm, total_exp
    from rankedexpenses
    where rank <= 3
    order by itm, rank
    ')

    endsubmit;
    import data=want r=want;
    run;quit;

    proc print data=want;
    run;

    Altair SLC

    Obs                 NAME                    ITM       TOTAL_EXP

      1    CATZ, LAURENCE (DW1030298)        Airfare       3098.80
      2    CONNEALY, TYLER (DW0921188)       Airfare       2729.48
      3    MAICO, REKO (DW0386388)           Airfare       1733.61
      4    BALL, GUY (DW0835689)             Airline        164.68
      5    KNOOP, JEFF (DW0767037)           Airline         99.02
      6    CRAIG, ROSE (DW0357148)           Airline         93.74
      7    SANDERSON, MICHAEL (DW0231288)    Breakfast      441.45
      8    SEPHERS, GEOFF (DW0355310)        Breakfast      307.85
      9    CAVANAGH, NATHAN (DW0423887)      Breakfast      292.51
     10    HO, JON (DW0170296)               Dinner        1781.82
     11    SEPHERS, GEOFF (DW0355310)        Dinner        1179.65
     12    SANDERSON, MICHAEL (DW0231288)    Dinner         956.91
     13    PRINCE, KYLE (DW1292453)          Hotel         2410.33
     14    CONNEALY, TYLER (DW0921188)       Hotel         2208.08
     15    PENERA, HEATH (DW0332752)         Hotel         1892.71
     16    MEARS, SHANNON (DW1032826)        Lunch         1647.05
     17    SEPHERS, GEOFF (DW0355310)        Lunch         1568.62
     18    SANDERSON, MICHAEL (DW0231288)    Lunch         1396.57
     19    MAICO, REKO (DW0386388)           Taxi          5871.38
     20    BERRETT, SHAUN (DW0147143)        Taxi          3913.01
     21    WILLIAMS, VAN (DW1071325)         Taxi          2808.35
     22    SIPES, LANCE (DW0741723)          Travel         977.30
     23    METOYER, MICHELLE (DW0744874)     Travel         676.20
     24    GONZALEZ, YVETTE (DW0266104)      Travel         542.78


    /*--- Lets check ---*/

    proc sort data=exchange(where=(name=:'CONNEALY, TYLER')) out=srt;
     by descending equiv;
    run;quit;

    proc print data=srt;
    run;quit;


    proc sort data=exchange(where=(name=:'CONNEALY, TYLER')) out=srt;
     by descending equiv;
    run;quit;

    proc print data=srt;
    run;quit;


    proc sort data=exchange(where=(name=:'CATZ, LAURENCE')) out=srt;
     by descending equiv;
    run;quit;

    proc print data=srt;
    run;quit;


    Altair SLC

    Obs               NAME                 EXP      CUR      ITM      CURRENCY_PRICE    CURRENCY    EQUIV

      1    CONNEALY, TYLER (DW0921188)    682.37    USD    Airfare         .                        682.37
      2    CONNEALY, TYLER (DW0921188)    682.37    USD    Airfare         .                        682.37
      3    CONNEALY, TYLER (DW0921188)    682.37    USD    Airfare         .                        682.37
      4    CONNEALY, TYLER (DW0921188)    682.37    USD    Airfare         .                        682.37
                                                                                                   =======
                                                                                                   2729.48


                NAME                       EXP      CUR       ITM     CURRENCY_PRICE    CURRENCY     EQUIV

      1    CATZ, LAURENCE (DW1030298)    2377.00    GBP    Airfare        .76707          GBP       3098.80                                                                                              2729.48 Checks


    /*
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    */

    2677      ODS _ALL_ CLOSE;
    2678      ODS LISTING;
    2679      FILENAME WBGSF 'd:\wpswrk\_TD9616/listing_images';
    2680      OPTIONS DEVICE=GIF;
    2681      GOPTIONS GSFNAME=WBGSF;
    2682      &_init_;
    2683      proc datasets lib=work nolist nodetails;
    2684       delete want;
    2685      run;quit;
    NOTE: Deleting "WORK.WANT" (memtype="DATA")
    NOTE: Procedure datasets step took :
          real time : 0.001
          cpu time  : 0.000


    2686
    2687      proc r;
    NOTE: Using R version 4.5.1 (2025-06-13 ucrt) from d:\r451
    2688      export data=exchange r=exchange;
    NOTE: Creating R data frame 'exchange' from data set 'WORK.exchange'

    2689      submit;
    2690      library(sqldf)
    2691      options(sqldf.dll = "d:/dll/sqlean.dll")
    2692      want<-sqldf('
    2693      with totalexpenses as (
    2694        select
    2695          name,
    2696          itm,
    2697          sum(equiv) as total_exp
    2698        from exchange
    2699        where itm in ("Travel","Taxi","Airfare","Airline","Hotel","Lunch","Breakfast","Dinner")
    2700        group by name, itm
    2701      ),
    2702      rankedexpenses as (
    2703        select
    2704          name,
    2705          itm,
    2706          total_exp,
    2707          row_number() over (partition by itm order by total_exp desc) as rank
    2708        from totalexpenses
    2709      )
    2710      select name, itm, total_exp
    2711      from rankedexpenses
    2712      where rank <= 3
    2713      order by itm, rank
    2714      ')
    2715
    2716      endsubmit;

    NOTE: Submitting statements to R:

    Loading required package: gsubfn
    Loading required package: proto
    Loading required package: RSQLite
    > library(sqldf)
    > options(sqldf.dll = "d:/dll/sqlean.dll")
    > want<-sqldf('
    + with totalexpenses as (
    +   select
    +     name,
    +     itm,
    +     sum(equiv) as total_exp
    +   from exchange
    +   where itm in ("Travel","Taxi","Airfare","Airline","Hotel","Lunch","Breakfast","Dinner")
    +   group by name, itm
    + ),
    + rankedexpenses as (
    +   select
    +     name,
    +     itm,
    +     total_exp,
    +     row_number() over (partition by itm order by total_exp desc) as rank
    +   from totalexpenses
    + )
    + select name, itm, total_exp
    + from rankedexpenses
    + where rank <= 3
    + order by itm, rank
    + ')

    NOTE: Processing of R statements complete

    >
    2717      import data=want r=want;
    NOTE: Creating data set 'WORK.want' from R data frame 'want'
    NOTE: Column names modified during import of 'want'
    NOTE: Data set "WORK.want" has 24 observation(s) and 3 variable(s)

    2718      run;quit;
    NOTE: Procedure r step took :
          real time : 1.373
          cpu time  : 0.015


    2719
    2720      proc print data=want;
    2721      run;
    NOTE: 24 observations were read from "WORK.want"
    NOTE: Procedure print step took :
          real time : 0.011
          cpu time  : 0.015


    2722      quit; run;
    2723      ODS _ALL_ CLOSE;
    2724      FILENAME WBGSF CLEAR;

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */

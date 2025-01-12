# utl-example-of-a-cartesian-join-in-sqlite-using-cross-join-instead-of-outer-join
Example of a cartesian join in sqlite using cross join instead of outer join 
    %let pgm=utl-example-of-a-cartesian-join-in-sqlite-using-cross-join-instead-of-outer-join;

    %stop_submission;

    Example of a cartesian join in sqlite using cross join instead of outer join

    Cross(outer) join was added in sqlite in version 3

       TWO SOLUTIONS

            1 sas outer jooin
            2 sqlite cross join

    I was using an older version of sqlite we now have the cross/cartesian join.

    github
    https://tinyurl.com/mr4azeku
    https://github.com/rogerjdeangelis/utl-example-of-a-cartesian-join-in-sqlite-using-cross-join-instead-of-outer-join

    stackoverflow
    https://tinyurl.com/mwdemujz
    https://stackoverflow.com/questions/79345533/wrong-variable-comparison-result-when-performing-data-table-merge-of-two-table-w


    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*                                |                          |                                                            */
    /*                                |                          |                                                            */
    /*             INPUT              |       OUTPUT             |       PROCESS                                              */
    /*             =====              |       ======             |       =======                                              */
    /*                                |                          |                                                            */
    /*                                |  EACH LEFTX RECORD IS    |    1 SAS SQL OUTER JOIN                                    */
    /*                                |  MATCHED WITH EACH       |    =====================                                   */
    /*   TABLE LEFTX  |  TABLE RIGHTX |  RIGHTX RECORDS BY IDS   |                                                            */
    /*   ===========  |  ============ |                          |      select                                                */
    /*                |               |                          |        l.lid                                               */
    /*   LID    LFT   | RID    RYT    |   LID RID  RYT   LFT     |       ,r.rid                                               */
    /*                |               |                          |       ,r.ryt                                               */
    /*    A     R1    |  A     L1     |    A   A    R1    L1     |       ,l.lft                                               */
    /*    A     R2    |  A     L2     |    A   A    R2    L1     |      from                                                  */
    /*    A     R3    |               |    A   A    R3    L1     |        sd1.leftx as l full                                 */
    /*                                |                          |      outer                                                 */
    /*                |               |    A   A    R1    L2     |        join sd1.rightx as r                                */
    /*                |               |    A   A    R2    L2     |      on                                                    */
    /*                |               |    A   A    R3    L2     |        l.lid = r.rid                                       */
    /*                                |                          |      order                                                 */
    /*                |               |                          |        by l.lid, ryt, lft                                  */
    /*                                |   LID RID  RYT   LFT     |                                                            */
    /*                                |                          |    2 SQLITE CROSS JOIN                                     */
    /*    B     R1      B     L1      |    B   B    R1    L1     |    ====================                                    */
    /*    B     R2      B     L2      |    B   B    R2    L1     |                                                            */
    /*                                |                          |      select                                                */
    /*                                |    B   B    R1    L1     |        l.lid                                               */
    /*                                |    B   B    R2    L2     |       ,r.rid                                               */
    /*                                |                          |       ,r.ryt                                               */
    /*                                |                          |       ,l.lft                                               */
    /*                                |                          |      from                                                  */
    /*                                |                          |        leftx as l cross join rightx as r                   */
    /*                                |                          |      on                                                    */
    /*                                |                          |        l.lid = r.rid                                       */
    /*                                |                          |      order                                                 */
    /*                                |                          |        by l.lid, r.ryt, l.lft                              */
    /*                                |                          |                                                            */
    /**************************************************************************************************************************/


    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */
    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.leftx;
      input lid$ lft$;
    cards4;
    A R1
    A R2
    A R3
    B R1
    B R2
    ;;;;
    run;quit;

    data sd1.rightx;
      input rid$ ryt$;
    cards4;
    A L1
    A L2
    B L1
    B L2
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*   LEFTX.SAS7BDAT   RIGHTX.SAS7BDAT                                                                                     */
    /*   ==============   ===============                                                                                     */
    /*                                                                                                                        */
    /*   LID    LFT       RID    RYT                                                                                          */
    /*                                                                                                                        */
    /*    A     R1         A     L1                                                                                           */
    /*    A     R2         A     L2                                                                                           */
    /*    A     R3         B     L1                                                                                           */
    /*    B     R1         B     L2                                                                                           */
    /*    B     R2                                                                                                            */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                                 _               _
    / |  ___  __ _ ___   ___ ___  __ _| |   ___  _   _| |_ ___ _ __
    | | / __|/ _` / __| / __/ __|/ _` | |  / _ \| | | | __/ _ \ `__|
    | | \__ \ (_| \__ \ \__ \__ \ (_| | | | (_) | |_| | ||  __/ |
    |_| |___/\__,_|___/ |___/___/\__, |_|  \___/ \__,_|\__\___|_|
                                    |_|
    */

    proc sql;
      create
        table want as
      select
        l.lid
       ,r.rid
       ,r.ryt
       ,l.lft
      from
        sd1.leftx as l full
      outer
        join sd1.rightx as r
      on
        l.lid = r.rid
      order
        by l.lid, ryt, lft
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  WORK.WANT                                                                                                             */
    /*                                                                                                                        */
    /*  LID    RID    RYT    LFT                                                                                              */
    /*                                                                                                                        */
    /*   A      A     L1     R1                                                                                               */
    /*   A      A     L1     R2                                                                                               */
    /*   A      A     L1     R3                                                                                               */
    /*   A      A     L2     R1                                                                                               */
    /*   A      A     L2     R2                                                                                               */
    /*   A      A     L2     R3                                                                                               */
    /*   B      B     L1     R1                                                                                               */
    /*   B      B     L1     R2                                                                                               */
    /*   B      B     L2     R1                                                                                               */
    /*   B      B     L2     R2                                                                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                     _ _ _
    |___ \   _ __   ___  __ _| (_) |_ ___    ___ _ __ ___  ___ ___
      __) | | `__| / __|/ _` | | | __/ _ \  / __| `__/ _ \/ __/ __|
     / __/  | |    \__ \ (_| | | | ||  __/ | (__| | | (_) \__ \__ \
    |_____| |_|    |___/\__, |_|_|\__\___|  \___|_|  \___/|___/___/
                           |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    leftx<-read_sas("d:/sd1/leftx.sas7bdat")
    rightx<-read_sas("d:/sd1/rightx.sas7bdat")
    want<-sqldf('
      select
        l.lid
       ,r.rid
       ,r.ryt
       ,l.lft
      from
        leftx as l cross join rightx as r
      on
        l.lid = r.rid
      order
        by l.lid, r.ryt, l.lft
      ')
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*                         |                                                                                              */
    /*  R                      |    SAS                                                                                       */
    /*                         |                                                                                              */
    /*     LID RID RYT LFT     |    ROWNAMES    LID    RID    RYT    LFT                                                      */
    /*                         |                                                                                              */
    /*  1    A   A  L1  R1     |        1        A      A     L1     R1                                                       */
    /*  2    A   A  L1  R2     |        2        A      A     L1     R2                                                       */
    /*  3    A   A  L1  R3     |        3        A      A     L1     R3                                                       */
    /*  4    A   A  L2  R1     |        4        A      A     L2     R1                                                       */
    /*  5    A   A  L2  R2     |        5        A      A     L2     R2                                                       */
    /*  6    A   A  L2  R3     |        6        A      A     L2     R3                                                       */
    /*  7    B   B  L1  R1     |        7        B      B     L1     R1                                                       */
    /*  8    B   B  L1  R2     |        8        B      B     L1     R2                                                       */
    /*  9    B   B  L2  R1     |        9        B      B     L2     R1                                                       */
    /*  10   B   B  L2  R2     |       10        B      B     L2     R2                                                       */
    /*                         |                                                                                              */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */

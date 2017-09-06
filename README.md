# utl_proc_corresp_crosstab
Using 'proc corresp' for a comlex crosstab

    ```  Using proc corresp for a complex cross tab dataset  ```
    ```    ```
    ```    This is a very flexible solution?  ```
    ```    ```
    ```    You may be able to produce this report with a complex 'proc report'.  ```
    ```    but templates are neded for arbitrary layouts. This is very close  ```
    ```    to an arbitrary layout because variable names and data appear  ```
    ```    on the same line and descriptions like Vist1finding is confounded with cohort values.  ```
    ```    ```
    ```    WORKING CODE  ```
    ```      Two Solutions  ```
    ```    ```
    ```      1. proc corresp data=have observed dimens=1 print=percent cross=row;  ```
    ```             tables examination visit1finding , cohort;  ```
    ```    ```
    ```         /* output of  ```
    ```         Obs    TYPE             _1       _2     ROWPCT  ```
    ```    ```
    ```          1     ABDOMEN * N    31.25    12.50     43.75  ```
    ```          2     ABDOMEN * Y     0.00     6.25      6.25  ```
    ```          3     SKIN * N       25.00    12.50     37.50  ```
    ```          4     SKIN * Y        6.25     6.25     12.50  ```
    ```          5     Sum            62.50    37.50    100.00  ```
    ```    ```
    ```      2. proc corresp data=have observed dimens=1 print=percent cross=row;  ```
    ```             tables examination visit1finding , cohort;  ```
    ```    ```
    ```          Layout  ```
    ```    ```
    ```          proc format;  ```
    ```            value lyn2des  ```
    ```            1 =  "0 @ABNOMEN @Cohort1 @Cohort2  @RowPercent"  ```
    ```            2 =  "1 @ @#_1 @#_2 @#rowpct"  ```
    ```            3 =  "2 @ @#_1 @#_2 @#rowpct"  ```
    ```            4 =  "0 @SKIN @Cohort1 @Cohort2 @RowPercent"  ```
    ```            5 =  "3 @ @#_1 @#_2 @#rowpct"  ```
    ```            6 =  "4 @ @#_1 @#_2 @#rowpct"  ```
    ```            7 =  "0 @Sum @Cohort1 @Cohort2 @RowPercent"  ```
    ```            8 =  "5 @ @#_1 @#_2 @#rowpct"  ```
    ```    ```
    ```          Do lyn=1 to 8;  ```
    ```            layout=put(lyn,lyn2des.);  ```
    ```            if scan(layout,1,'@')='0' then do;  ```
    ```              examination   =  scan(layout,2,'@');  ```
    ```              visit1finding =  "Visit1finding";  ```
    ```              cohort1       =  scan(layout,3,'@');  ```
    ```              cohort2       =  scan(layout,4,'@');  ```
    ```              RowPercent    =  scan(layout,5,'@');  ```
    ```              output;  ```
    ```            end;  ```
    ```            else do;  ```
    ```             pnt=input(scan(layout,1,'@'),2.);  ```
    ```             set xpopct point=pnt;  ```
    ```               examination   =  "";  ```
    ```               visit1finding =  scan(type,2,'*');  ```
    ```               cohort1       =  cats(put(_1,5.1),'%');  ```
    ```               cohort2       =  cats(put(_2,5.1),'%');  ```
    ```               RowPercent    =  cats(put(rowpct,5.1),'%');  ```
    ```               output;  ```
    ```            end;  ```
    ```          end;  ```
    ```    ```
    ```          ABNOMEN      Visit1finding    Cohort1    Cohort2    RowPercent  ```
    ```                        N               31.3%      12.5%      43.8%  ```
    ```                        Y               0.0%       6.3%       6.3%  ```
    ```    ```
    ```          SKIN         Visit1finding    Cohort1    Cohort2    RowPercent  ```
    ```                        N               25.0%      12.5%      37.5%  ```
    ```                        Y               6.3%       6.3%       12.5%  ```
    ```    ```
    ```          Sum          Visit1finding    Cohort1    Cohort2    RowPercent  ```
    ```                                        2.5%      37.5%      100.0%  ```
    ```  see  ```
    ```  https://goo.gl/1L7ELi  ```
    ```  https://communities.sas.com/t5/Base-SAS-Programming/PROC-FREQ-or-PROC-TABULATE-for-complex-table/m-p/393506  ```
    ```    ```
    ```  *                _              _       _  ```
    ```   _ __ ___   __ _| | _____    __| | __ _| |_ __ _  ```
    ```  | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |  ```
    ```  | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |  ```
    ```  |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|  ```
    ```    ```
    ```  ;  ```
    ```    ```
    ```  data have;  ```
    ```   informat patient $1. examination $7. visit1finding visit2finding cohort $1.;  ```
    ```   input patient examination visit1finding visit2finding cohort;  ```
    ```  cards4;  ```
    ```  1 SKIN N N 1  ```
    ```  1 ABDOMEN N N 1  ```
    ```  2 SKIN Y N 2  ```
    ```  2 ABDOMEN N Y 2  ```
    ```  3 SKIN N Y 1  ```
    ```  3 ABDOMEN N Y 1  ```
    ```  4 SKIN N Y 2  ```
    ```  4 ABDOMEN N Y 2  ```
    ```  5 SKIN Y Y 1  ```
    ```  5 ABDOMEN N Y 1  ```
    ```  6 SKIN N Y 2  ```
    ```  6 ABDOMEN Y Y 2  ```
    ```  7 SKIN N Y 1  ```
    ```  7 ABDOMEN N Y 1  ```
    ```  8 SKIN N Y 1  ```
    ```  8 ABDOMEN N Y 1  ```
    ```  ;;;;  ```
    ```  run;quit;  ```
    ```    ```
    ```  *_     _                _       _   _  ```
    ```  / |___| |_    ___  ___ | |_   _| |_(_) ___  _ __  ```
    ```  | / __| __|  / __|/ _ \| | | | | __| |/ _ \| '_ \  ```
    ```  | \__ \ |_   \__ \ (_) | | |_| | |_| | (_) | | | |  ```
    ```  |_|___/\__|  |___/\___/|_|\__,_|\__|_|\___/|_| |_|  ```
    ```    ```
    ```  ;  ```
    ```    ```
    ```  ods exclude all;  ```
    ```  ods output observedpct=xpopct(rename=(label=type sum=rowpct));  ```
    ```  proc corresp data=have observed dimens=1 print=percent cross=row;  ```
    ```   tables examination visit1finding , cohort;  ```
    ```  run;quit;  ```
    ```  ods select all;  ```
    ```    ```
    ```    ```
    ```  proc report data=xpopct nowd split='#';  ```
    ```  cols TYPE ( "Visit2Finding" _1 _2 rowpct );  ```
    ```  define type / display "";  ```
    ```  define _1 / display "Cohort1";  ```
    ```  define _2 / display "Cohort2";  ```
    ```  define rowpct / "Row#Percent";  ```
    ```  run;quit;  ```
    ```    ```
    ```                        Visit2Finding  ```
    ```                                          Row  ```
    ```                  Cohort1   Cohort2   Percent  ```
    ```    ABDOMEN * N    31.250    12.500    43.750  ```
    ```    ABDOMEN * Y     0.000     6.250     6.250  ```
    ```    SKIN * N       25.000    12.500    37.500  ```
    ```    SKIN * Y        6.250     6.250    12.500  ```
    ```    Sum            62.500    37.500   100.000  ```
    ```    ```
    ```    ```
    ```  *____            _              _       _   _  ```
    ```  |___ \ _ __   __| |   ___  ___ | |_   _| |_(_) ___  _ __  ```
    ```    __) | '_ \ / _` |  / __|/ _ \| | | | | __| |/ _ \| '_ \  ```
    ```   / __/| | | | (_| |  \__ \ (_) | | |_| | |_| | (_) | | | |  ```
    ```  |_____|_| |_|\__,_|  |___/\___/|_|\__,_|\__|_|\___/|_| |_|  ```
    ```    ```
    ```  ;  ```
    ```    ```
    ```  ods exclude all;  ```
    ```  ods output observedpct=xpopct(rename=(label=type sum=rowpct));  ```
    ```  proc corresp data=have observed dimens=1 print=percent cross=row;  ```
    ```   tables examination visit1finding , cohort;  ```
    ```  run;quit;  ```
    ```  ods select all;  ```
    ```    ```
    ```    ```
    ```  proc format;  ```
    ```    value lyn2des  ```
    ```    1 =  "0 @ABNOMEN @Cohort1 @Cohort2  @RowPercent"  ```
    ```    2 =  "1 @ @#_1 @#_2 @#rowpct"  ```
    ```    3 =  "2 @ @#_1 @#_2 @#rowpct"  ```
    ```    4 =  "0 @SKIN @Cohort1 @Cohort2 @RowPercent"  ```
    ```    5 =  "3 @ @#_1 @#_2 @#rowpct"  ```
    ```    6 =  "4 @ @#_1 @#_2 @#rowpct"  ```
    ```    7 =  "0 @Sum @Cohort1 @Cohort2 @RowPercent"  ```
    ```    8 =  "5 @ @#_1 @#_2 @#rowpct"  ```
    ```  ;run;quit;  ```
    ```    ```
    ```    ```
    ```    ```
    ```  data fmtrpt(keep=examination visit1finding cohort1 cohort2 RowPercent);  ```
    ```    retain examination visit1finding cohort1 cohort2;  ```
    ```    length examination visit1finding cohort1 cohort2 RowPercent $18.;  ```
    ```    Do lyn=1 to 8;  ```
    ```      layout=put(lyn,lyn2des.);  ```
    ```      if scan(layout,1,'@')='0' then do;  ```
    ```        examination   =  scan(layout,2,'@');  ```
    ```        visit1finding =  "Visit1finding";  ```
    ```        cohort1       =  scan(layout,3,'@');  ```
    ```        cohort2       =  scan(layout,4,'@');  ```
    ```        RowPercent    =  scan(layout,5,'@');  ```
    ```        output;  ```
    ```      end;  ```
    ```      else do;  ```
    ```       pnt=input(scan(layout,1,'@'),2.);  ```
    ```       set xpopct point=pnt;  ```
    ```         examination   =  "";  ```
    ```         visit1finding =  scan(type,2,'*');  ```
    ```         cohort1       =  cats(put(_1,5.1),'%');  ```
    ```         cohort2       =  cats(put(_2,5.1),'%');  ```
    ```         RowPercent    =  cats(put(rowpct,5.1),'%');  ```
    ```         output;  ```
    ```      end;  ```
    ```    end;  ```
    ```    stop;  ```
    ```  run;quit;  ```
    ```    ```
    ```    ```

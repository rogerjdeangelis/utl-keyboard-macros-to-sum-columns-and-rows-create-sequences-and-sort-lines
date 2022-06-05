# utl-keyboard-macros-to-sum-columns-and-rows-create-sequences-and-sort-lines
Keyboard macros to sum columns and rows, create sequences and sort lines
   %let pgm=utl-keyboard-macros-to-sum-columns-and-rows-create-sequences-and-sort-lines;

    Keyboard macros to sum columns and rows, create sequences and sort lines

    github
    https://tinyurl.com/55ksdmzn
    https://github.com/rogerjdeangelis/utl-keyboard-macros-to-sum-columns-and-rows-create-sequences-and-sort-lines

    Command line macros on end

    Contents
         1. Create editor lines with a sequence  numbers 1 to n. Sequence in paste buffer.
         2. Command macro to sum a column in the program editor. Output in output window
         3. Command macro to sum a row of numbers in the program editor. Output in output window.
         4. Command macro to sort lines in program editor. Sorted lines in paste buffer.

    It is not easy anymore to automaticall replace lines in the program editor, because
    the ISPF cursor command no longer works in the 1980s DMS editor.

    see: https://tinyurl.com/humvtmmu

    This requires the 1980s classic DMS editor not EE,EG,Studio,On Demand ..

    Note: '%sysfunc(dosubl' simplifies the coding of command macros.
          My command macros were written many years ago before dosubl.

    The best part of these command macros is that they are
    pure sas code which you can easily modify.

    /*
     ___  ___  __ _ _   _  ___ _ __   ___ ___
    / __|/ _ \/ _` | | | |/ _ \ `_ \ / __/ _ \
    \__ \  __/ (_| | |_| |  __/ | | | (_|  __/
    |___/\___|\__, |\__,_|\___|_| |_|\___\___|
                 |_|
    */

    Type iota 10 on command line.
    Paste buffer will have


    01
    02
    03
    04
    05
    06
    07
    08
    09
    10

    /*                   _
     ___ _   _ _ __ ___ | |__
    / __| | | | `_ ` _ \| `_ \
    \__ \ |_| | | | | | | | | |
    |___/\__,_|_| |_| |_|_| |_|

    */

    Suppose you want the sum of the
    numbers in the column below
    Just highlight the column and type sumh.
    The sum will be in the output window

    01
    02
    03
    04
    05
    06
    07
    08
    09
    10

    This will appear in your output window

                                                           Lower                   Upper
     N         Sum       Mean    Std Dev     Minimum    Quartile      Median    Quartile      Maximum    
    10  55.0000000  5.5000000  3.0276504   1.0000000   3.0000000   5.5000000   8.0000000   10.0000000
    

    /*
     ___ _   _ _ __ ___  _ __ _____      __
    / __| | | | `_ ` _ \| `__/ _ \ \ /\ / /
    \__ \ |_| | | | | | | | | (_) \ V  V /
    |___/\__,_|_| |_| |_|_|  \___/ \_/\_/

    */

    Suppose you want the sum of the numbers in this row
    Just highlight the row and type sumr


    1 2 3 4 5 6 7 8 9 10

    SUMROW=55  will appear in the output window


    /*              _   _
     ___  ___  _ __| |_| |__
    / __|/ _ \| `__| __| `_ \
    \__ \ (_) | |  | |_| | | |
    |___/\___/|_|   \__|_| |_|

    */
    Suppose you want to sort the lines below.
    Just highlight and type sort on the
    classic 1980s command line.
    The paste buffer will have the sorted values so just
    paste over.
    (EE is the only editor that has a better
    implemenation fof this, see Carpenter 2012, you do not
    need to do the paste. However you cannot modify the
    keyboard shortcut. IE sort descending.

    roger
    kate
    alice
    george

    paste buffer will have (also in output window)

    alice
    george
    kate
    roger
    /*
     _ __ ___   __ _  ___ _ __ ___  ___
    | `_ ` _ \ / _` |/ __| `__/ _ \/ __|
    | | | | | | (_| | (__| | | (_) \__ \
    |_| |_| |_|\__,_|\___|_|  \___/|___/

    */

    %macro sorth / cmd;
       store;note;notesubmit '%sortha;'
       run;
    %mend sorth;

    %macro sortha;

       filename clp clipbrd ;
       data _tmp;
         infile clp;
         input;
         lyn=_infile_;
         putlog lyn;
       run;quit;

       proc sort data=_tmp force;
          by lyn;
       run;quit;

       /* use this if you want to write to the profile catalog */
       /* filename _tmp catalog "sasuser.profile._tmp.source"; */

       filename _clp clipbrd ;
       data _chk;
         set _tmp;
         file _clp;
         put lyn;
         file print;
         put lyn;
       run;quit;

    %mend sortha;


    %macro iota(_end)/cmd
       des="type iota 10 and a list or 01 02 02 - 10 will be added to the editor";
       %let len=%length(&_end);
       rc=%sysfunc(dosubl('
          filename _clp clipbrd;
          data _null_;
             do i=1 to &_end;
               file _clp;
               res=put(i,z&len..);
               put res;
               file print;
               put res;
             end;
             '));
          run;quit;
    %mend iota;



    %macro sumr / cmd parmbuff
        des="highlight row of numeric variables and type sumr on command line for proc means using last dataset";
        store;note;notesubmit '%sumra;';
       run;
    %mend sumr;

    %macro sumra;
       filename clp clipbrd ;
       data _null_;
         file print;
         infile clp;
         input;
         _infile_=trim(_infile_);
         row=translate(_infile_,'+',' ');
         call symputx('row',row);
         rc=dosubl('
            data  _null_;
               sumrow=&row;
               file print;
                 put // sumrow= //;
            run;quit;
            ');
       run;
    %mend sumra;



    %macro sumh / cmd;
       store;note;notesubmit '%sumha;';
       run;
    %mend sumr;

    %macro sumha;
       filename clp clipbrd ;
       data _sumh_;
         infile clp;
         input x;
       run;quit;
       proc means data=_sumh_ n sum mean std min q1 median q3 max;run;quit
    %mend sumha;


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */

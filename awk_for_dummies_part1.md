#### AWK: The Linux Administrators' Wisdom Kit
#### By Emmett Dulaney

Learning Linux? This introduction to the invaluable AWK text-manipulation tool will be invaluable. 
The AWK utility, with its own self-contained language, is one of the most powerful data processing engines in existence — not only in Linux, but anywhere
The limits to what can be done with this programming and data-manipulation language (named for the last initials of its creators, Alfred Aho, Peter Weinberger, and Brian Kernighan) are the boundaries of one's own knowledge
It allows you to create short programs that read input files, sort data, process it, perform arithmetic on the input, and generate reports, among myriad other functions. 

### What Is AWK? 
To put it the simplest way possible, AWK is a programming-language tool used to manipulate text. The language of the AWK utility resembles the shell-programming language in many areas, although AWK's syntax is very much its own
When first created, AWK was designed to work in the text-processing arena, and the language is based on executing a series of instructions whenever a pattern is matched in the input data. The utility scans each line of a file, looking for patterns that match those given on the command line. If a match is found, it takes the next programming step. If no match is found, it then proceeds to the next line. 
While the operations can get complex, the syntax for the command is always: 
```
awk '{pattern + action}' {filenames}
```
where pattern represents what AWK is looking for in the data, and action is a series of commands executed when a match is found
Curly brackets ({}) are not always required around your program, but they are used to group a series of instructions based on a specific pattern  
### Understanding Fields 
The utility separates each input line into records and fields. A record is a single line of input, and each record consists of several fields. The default-field separator is a space or a tab, and the record separator is a new line. Although both tabs and spaces are perceived as field separators by default (multiple blank spaces still count as one delimiter), the delimiter can be changed from white space to any other character. 
To illustrate, look at the following employee-list file saved as emp_names:  
```
46012   DULANEY     EVAN        MOBILE   AL
46013   DURHAM      JEFF        MOBILE   AL
46015   STEEN       BILL        MOBILE   AL
46017   FELDMAN     EVAN        MOBILE   AL
46018   SWIM        STEVE       UNKNOWN  AL
46019   BOGUE       ROBERT      PHOENIX  AR
46021   JUNE        MICAH       PHOENIX  AR
46022   KANE        SHERYL      UNKNOWN  AR
46024   WOOD        WILLIAM     MUNCIE   IN
46026   FERGUS      SARAH       MUNCIE   IN
46027   BUCK        SARAH       MUNCIE   IN
46029   TUTTLE      BOB         MUNCIE   IN
```
As AWK reads the input, the entire record is assigned to the variable $0. Each field, as split with the field separator, is assigned to the variables $1, $2, $3, and so on
A line contains essentially an unlimited number of fields, with each field being accessed by the field number. Thus, the command  
```
awk '{print $1,$2,$3,$4,$5}' names
```  
would result in a printout of  
```
46012 DULANEY EVAN MOBILE A
46013 DURHAM JEFF MOBILE AL
46015 STEEN BILL MOBILE AL
46017 FELDMAN EVAN MOBILE AL
46018 SWIM STEVE UNKNOWN AL
46019 BOGUE ROBERT PHOENIX AR
46021 JUNE MICAH PHOENIX AR
46022 KANE SHERYL UNKNOWN AR
46024 WOOD WILLIAM MUNCIE IN
46026 FERGUS SARAH MUNCIE IN
46027 BUCK SARAH MUNCIE IN
46029 TUTTLE BOB MUNCIE IN
```
An important item of noteworthiness is that AWK interprets the five fields as being separated by white space, but when it prints the display, there is only one space between each field
By virtue of the ability to address each field with a unique number, you can choose to print only certain fields. For example, to print only the names from each record, select only the second and third fields to print:  
```
$ awk '{print $2,$3}' emp_names
DULANEY EVAN
DURHAM JEFF
STEEN BILL
FELDMAN EVAN
SWIM STEVE
BOGUE ROBERT
JUNE MICAH
KANE SHERYL
WOOD WILLIAM
FERGUS SARAH
BUCK SARAH
TUTTLE BOB
```  
You can also specify that the fields print in any order, regardless of how they exist in the record. Thus, to show only the name fields, and reverse them so the first name is shown, then the last:
```
$ awk '{print $3,$2}' emp_names
EVAN DULANEY
JEFF DURHAM
BILL STEEN
EVAN FELDMAN
STEVE SWIM
ROBERT BOGUE
MICAH JUNE
SHERYL KANE
WILLIAM WOOD
SARAH FERGUS
SARAH BUCK
BOB TUTTLE  
```
### Working with Patterns 
You can select the action to take place only on certain records, and not on all records, by including a pattern that must be matched
The simplest form of pattern matching is that of a search, wherein the item to be matched is included in slashes (/pattern/)
For example, to perform the earlier action only on those employees who live in Alabama:   
```
$ awk '/AL/ {print $3,$2}' emp_names
EVAN DULANEY
JEFF DURHAM
BILL STEEN
EVAN FELDMAN
STEVE SWIM
```  
If you do not specify what fields to print, the entire matching entry will print:   
```
$ awk '/AL/' emp_names
46012   DULANEY     EVAN     MOBILE     AL
46013   DURHAM      JEFF     MOBILE     AL
46015   STEEN       BILL     MOBILE     AL
46017   FELDMAN     EVAN     MOBILE     AL
46018   SWIM        STEVE    UNKNOWN    AL
```  
Multiple commands for the same set of data can be separated with a semicolon (;). For example, to print names on one line and city and state on another: 
```
$ awk '/AL/ {print $3,$2 ; print $4,$5}' emp_names
EVAN DULANEY
MOBILE AL
JEFF DURHAM
MOBILE AL
BILL STEEN
MOBILE AL
EVAN FELDMAN
MOBILE AL
STEVE SWIM
UNKNOWN AL
```  
If the semicolon were not used (print $3,$2,$4,$5), all would appear on the same line. On the other hand, if the two print statements were given separately, an altogether different result would occur: 
```
$ awk '/AL/ {print $3,$2} {print $4,$5}' emp_names
EVAN DULANEY
MOBILE AL
JEFF DURHAM
MOBILE AL
BILL STEEN
MOBILE AL
EVAN FELDMAN
MOBILE AL
STEVE SWIM
UNKNOWN AL
PHOENIX AR
PHOENIX AR
UNKNOWN AR
MUNCIE IN
MUNCIE IN
MUNCIE IN
MUNCIE IN
```  
Fields three and two are given only when AL is found in the listing. Fields four and five, however, are unconditional and always print  
Only the commands within the first set of curly braces are active for the command (/AL/) immediately preceding. 
The result is altogether cumbersome to read and can use cleaning up a bit. First, insert a space and comma between city and state. Next, leave a blank line after each two-line display: 
```
$ awk '/AL/ {print $3,$2 ; print $4", "$5"\n"}' emp_names
EVAN DULANEY
MOBILE, AL

JEFF DURHAM
MOBILE, AL

BILL STEEN
MOBILE, AL

EVAN FELDMAN
MOBILE, AL

STEVE SWIM
UNKNOWN, AL
```
Between the fourth and fifth fields, a comma and a space are added (between the quotation marks), and after the fifth field, a new line character (\n) is printed  
All the special characters that can be used with the echo command can be also be used with AWK print statements, including:      
```  
•	\n (new line) 
•	\t (tab) 
•	\b (backspace) 
•	\f (formfeed) 
•	\r (carriage return)
```
Thus, to read all five fields, which were originally separated by tabs, and print them with tabs as well, you could program 
```
$ awk '{print $1"\t"$2"\t"$3"\t"$4"\t"$5}' emp_names
46012   DULANEY     EVAN     MOBILE    AL
46013   DURHAM      JEFF     MOBILE    AL
46015   STEEN       BILL     MOBILE    AL
46017   FELDMAN     EVAN     MOBILE    AL
46018   SWIM        STEVE    UNKNOWN   AL
46019   BOGUE       ROBERT   PHOENIX   AR
46021   JUNE        MICAH    PHOENIX   AR
46022   KANE        SHERYL   UNKNOWN   AR
46024   WOOD        WILLIAM  MUNCIE    IN
46026   FERGUS      SARAH    MUNCIE    IN
46027   BUCK        SARAH    MUNCIE    IN
46029   TUTTLE      BOB      MUNCIE    IN
```  
You can search for more than one pattern match at a time by placing the multiple criteria in consecutive order and separating them with a pipe (|) symbol: 
```
$ awk '/AL|IN/' emp_names
46012   DULANEY     EVAN     MOBILE    AL
46013   DURHAM      JEFF     MOBILE    AL
46015   STEEN       BILL     MOBILE    AL
46017   FELDMAN     EVAN     MOBILE    AL
46018   SWIM        STEVE    UNKNOWN   AL
46024   WOOD        WILLIAM  MUNCIE    IN
46026   FERGUS      SARAH    MUNCIE    IN
46027   BUCK        SARAH    MUNCIE    IN
46029   TUTTLE      BOB      MUNCIE    IN
```  
This finds every match for Alabama and Indiana residents. A problem occurs, however, when you try to find the people who live in Arizona: 
```
$ awk '/AR/' emp_names
46019   BOGUE       ROBERT   PHOENIX   AR
46021   JUNE        MICAH    PHOENIX   AR
46022   KANE        SHERYL   UNKNOWN   AR
46026   FERGUS      SARAH    MUNCIE    IN
46027   BUCK        SARAH    MUNCIE    IN
```  
Employees 46026 and 46027 do not live in Arizona; however, their first names contain the character sequence being searched for  
The important thing to remember is that when pattern matching in AWK, as in grep, sed, or most other Linux/Unix commands, look for a match anywhere in the record (line) unless told to do otherwise  
To solve this problem, it is necessary to tie the search to a particular field. This goal is accomplished by means of a tilde (˜) and a specification to a specific field, as the following example illustrates: 
```
$ awk '$5 ˜ /AR/' emp_names
46019   BOGUE       ROBERT   PHOENIX   AR
46021   JUNE        MICAH    PHOENIX   AR
46022   KANE        SHERYL   UNKNOWN   AR
```  
The opposite of the tilde (signifying a match) is a tilde preceded by an exclamation mark (!˜)  
These characters tell the program to find all lines matching the search sequence, providing the sequence does not appear in the specified field: 
```
$ awk '$5 !˜ /AR/' names
46012   DULANEY     EVAN     MOBILE    AL
46013   DURHAM      JEFF     MOBILE    AL
46015   STEEN       BILL     MOBILE    AL
46017   FELDMAN     EVAN     MOBILE    AL
46018   SWIM        STEVE    UNKNOWN   AL
46024   WOOD        WILLIAM  MUNCIE    IN
46026   FERGUS      SARAH    MUNCIE    IN
46027   BUCK        SARAH    MUNCIE    IN
46029   TUTTLE      BOB      MUNCIE    IN
``` 
In this case, it displayed all lines that do not have AR in the fifth field — including the two Sarah's entries that do have AR, but in the third field instead of the fifth one  
### Braces and Field Separators 
The bracket characters play an important part in the AWK commands. The actions that appear between them spell out what will take place and when it will take place. When only one set of brackets is used:  
```
{print $3,$2}
```
all the actions between occur at the same time. When more than one set of brackets is used:   
``` 
{print $3}{print $2}
``` 
the first sequence of commands is carried out until completion, then the second sequence kicks in. Notice the difference between the two listings that follow:  
```  
$ awk '{print $3,$2}' names
EVAN DULANEY
JEFF DURHAM
BILL STEEN
EVAN FELDMAN
STEVE SWIM
ROBERT BOGUE
MICAH JUNE
SHERYL KANE
WILLIAM WOOD
SARAH FERGUS
SARAH BUCK
BOB TUTTLE
```
``` 
$awk '{print $3}{print $2}' names
EVAN
DULANEY
JEFF
DURHAM
BILL
STEEN
EVAN
FELDMAN
STEVE
SWIM
ROBERT
BOGUE
MICAH
JUNE
SHERYL
KANE
WILLIAM
WOOD
SARAH
FERGUS
SARAH
BUCK
BOB
TUTTLE
```  
To reiterate the findings with multiple sets of brackets, the commands within the first set are carried out until completion; processing then moves to the second set
If there were a third set, it would go to that set on completion of the second set, and so on. In the generated printout, there are two separate print commands, so the first one is carried out, followed by the second, causing the display for each entry to appear on two lines instead of one. 
The field separator differentiating one field from another need not always be white space; it can be any discernible character
To illustrate, assume the emp_names file separated the fields with colons instead of tabs:  
```
$ cat emp_names
46012:DULANEY:EVAN:MOBILE:AL
46013:DURHAM:JEFF:MOBILE:AL
46015:STEEN:BILL:MOBILE:AL
46017:FELDMAN:EVAN:MOBILE:AL
46018:SWIM:STEVE:UNKNOWN:AL
46019:BOGUE:ROBERT:PHOENIX:AR
46021:JUNE:MICAH:PHOENIX:AR
46022:KANE:SHERYL:UNKNOWN:AR
46024:WOOD:WILLIAM:MUNCIE:IN
46026:FERGUS:SARAH:MUNCIE:IN
46027:BUCK:SARAH:MUNCIE:IN
46029:TUTTLE:BOB:MUNCIE:IN
```  
If you attempted to print the last name by specifying that you wanted the second field with 
```
$ awk '{print $2}' emp_names
```  
you would end up with twelve blank lines. Because there are no spaces in the file, there are no discernible fields beyond the first one
To solve the problem, AWK must be told that a character other than white space is the delimiter, and there are two methods by which to inform AWK of the new field separator: Use the command-line parameter -F, or specify the variable FS within the program
Both strategies work equally well, with one exception, as illustrated by the following example: 
```
$ awk '{FS=":"}{print $2}' emp_names

DURHAM
STEEN
FELDMAN
SWIM
BOGUE
JUNE
KANE
WOOD
FERGUS
BUCK
TUTTLE
```
``` 
$ awk -F: '{print $2}' emp_names
DULANEY
DURHAM
STEEN
FELDMAN
SWIM
BOGUE
JUNE
KANE
WOOD
FERGUS
BUCK
TUTTLE
```  
In the first command, a blank line is incorrectly returned for the very first record, while all the others work as they should
It is not until the second record is read that the field separator is recognized and properly acted on. This shortcoming can be corrected by using a BEGIN statement (more on that later)
The -F works much like a BEGIN and is able to correctly read the first record and act on it as it should. 
As I mentioned at the start of this article, the default display/output field separator is a blank space
This feature can be changed within the program by using the Output Field Separator (OFS) variable. For example, to read the file (separated by colons) and display it with dashes, the command would be 
```
$ awk -F":" '{OFS="-"}{print $1,$2,$3,$4,$5}' emp_names
46012-DULANEY-EVAN-MOBILE-AL
46013-DURHAM-JEFF-MOBILE-AL
46015-STEEN-BILL-MOBILE-AL
46017-FELDMAN-EVAN-MOBILE-AL
46018-SWIM-STEVE-UNKNOWN-AL
46019-BOGUE-ROBERT-PHOENIX-AZ
46021-JUNE-MICAH-PHOENIX-AZ
46022-KANE-SHERYL-UNKNOWN-AR
46024-WOOD-WILLIAM-MUNCIE-IN
46026-FERGUS-SARAH-MUNCIE-IN
46027-BUCK-SARAH-MUNCIE-IN
46029-TUTTLE-BOB-MUNCIE-IN
```  
FS and OFS, (input) Field Separator and Output Field Separator, are but a couple of the variables that can be used within the AWK utility  
For example, to number each line as it is printed, use the NR variable in the following manner: 
```
$ awk -F":" '{print NR,$1,$2,$3}' emp_names
1 46012 DULANEY EVAN
2 46013 DURHAM JEFF
3 46015 STEEN BILL
4 46017 FELDMAN EVAN
5 46018 SWIM STEVE
6 46019 BOGUE ROBERT
7 46021 JUNE MICAH
8 46022 KANE SHERYL
9 46024 WOOD WILLIAM
10 46026 FERGUS SARAH
11 46027 BUCK SARAH
12 46029 TUTTLE BOB
```  
To find all lines with employee numbers between 46012 and 46015: 
```
$ awk -F":" '/4601[2-5]/' emp_names
46012   DULANEY EVAN  MOBILE AL
46013   DURHAM  JEFF  MOBILE AL
46015   STEEN   BILL  MOBILE AL
```  
### Adding Text 
Text may be added to the display in the same manner as control sequences or other characters are. For example, to change the delimiter from spaces to colons, the command could be 
```
awk '{print $1":"$2":"$3":"$4":"$5}' emp_names > new_emp_names
```
In this case, the character (:), enclosed in quotation marks ("/"), is added between each of the fields. This value within the quotation marks can be anything. For example, to create a database-like display of the employees living in Alabama: 
```
$ awk '$5 ~ /AL/ {print "NAME: "$2", "$3"\nCITY-STATE:
  "$4", "$5"\n"}' emp_names

NAME: DULANEY, EVAN
CITY-STATE: MOBILE, AL

NAME: DURHAM, JEFF
CITY-STATE: MOBILE, AL

NAME: STEEN, BILL
CITY-STATE: MOBILE, AL

NAME: FELDMAN, EVAN
CITY-STATE: MOBILE, AL

NAME: SWIM, STEVE
CITY-STATE: UNKNOWN, AL
```   
### Math Operations   
In addition to the textual possibilities AWK provides, it also offers a full range of arithmetic operators, including the following: 
```
+ adds numbers together
- subtracts
* multiplies
/ divides
^ performs exponential mathematics
% gives the modulo
++ adds one to the value of a variable
+= assigns the result of an addition operation to a variable
— subtracts one from a variable
-= assigns the result of a subtraction operation to a variable
*= assigns the result of multiplication
/= assigns the result of division
%= assigns the result of a modulo operation 
```
For example, assume the following file exists on your machine detailing the inventory in a hardware store: 
```
$ cat inventory
hammers 5       7.99
drills  2      29.99
punches 7       3.59
drifts  2       4.09
bits   55       1.19
saws  123      14.99
nails 800        .19
screws 80        .29
brads 100        .24
```  
The first order of business is to compute the value of each item's inventory by multiplying the value of the second field (quantity) by the value of the third field (price): 
```  
$ awk '{print $1,"QTY: "$2,"PRICE: "$3,"TOTAL: "$2*$3}' inventory
hammers QTY: 5 PRICE: 7.99 TOTAL: 39.95
drills QTY: 2 PRICE: 29.99 TOTAL: 59.98
punches QTY: 7 PRICE: 3.59 TOTAL: 25.13
drifts QTY: 2 PRICE: 4.09 TOTAL: 8.18
bits QTY: 55 PRICE: 1.19 TOTAL: 65.45
saws QTY: 123 PRICE: 14.99 TOTAL: 1843.77
nails QTY: 800 PRICE: .19 TOTAL: 152
screws QTY: 80 PRICE: .29 TOTAL: 23.2
brads QTY: 100 PRICE: .24 TOTAL: 24
```  
If the lines themselves are unimportant, and you want only to determine exactly how many items are in the store, you can assign a generic variable to increment by the number of items in each record: 
```  
$ awk '{x=x+$2} {print x}' inventory
5
7
14
16
71
194
994
1074
1174
```  
According to this data, 1,174 items are in the store. The first time through, the variable x had no value, so it took the value of the first line's second field
The next time through, it retained the value of the first line and added the value from the second line, and so on, until it arrived at a cumulative total. 
The same process can be applied to determining the total value of the inventory on hand: 
```  
$ awk '{x=x+($2*$3)} {print x}' inventory
39.95
99.93
125.06
133.24
198.69
2042.46
2194.46
2217.66
2241.66
```  
Thus, the value of the 1,174 items is $2,241.66. Although this procedure is good for getting a total, it does not look at all pretty, and it would need sanitizing for an actual report
Sprucing up the display a bit can be easily accomplished with a few additions: 
```  
$ awk '{x=x+($2*$3)}{print $1,"QTY: "$2,"PRICE: "$3,"TOTAL: "$2*$3,"BAL: "x}' inventory
hammers QTY: 5 PRICE: 7.99 TOTAL: 39.95 BAL: 39.95
drills QTY: 2 PRICE: 29.99 TOTAL: 59.98 BAL: 99.93
punches QTY: 7 PRICE: 3.59 TOTAL: 25.13 BAL: 125.06
drifts QTY: 2 PRICE: 4.09 TOTAL: 8.18 BAL: 133.24
bits QTY: 55 PRICE: 1.19 TOTAL: 65.45 BAL: 198.69
saws QTY: 123 PRICE: 14.99 TOTAL: 1843.77 BAL: 2042.46
nails QTY: 800 PRICE: .19 TOTAL: 152 BAL: 2194.46
screws QTY: 80 PRICE: .29 TOTAL: 23.2 BAL: 2217.66
brads QTY: 100 PRICE: .24 TOTAL: 24 BAL: 2241.66
```  
This procedure gives a listing of each record while assigning a total value to the inventory and keeping a running balance of the store's inventory
### BEGIN and END 
Actions can be specified to take place prior to the actual start of processing or after it has been completed with BEGIN and END statements respectively  
BEGIN statements are most commonly used to establish variables or display a header  
END statements, on the other hand, can be used to continue processing after the program has finished. 
In an earlier example, a complete value of the inventory was generated with the routine  
```
awk '{x=x+($2*$3)} {print x}' inventory
```
This routine provided a display for each line in the file as the running total accumulated  
There was no other way to specify it, and not having it print at each line would have resulted in it never printing. With an END statement, however, this problem can be circumvented: 
```
$ awk '{x=x+($2*$3)} END {print "Total Value of Inventory: "x}' inventory
Total Value of Inventory: 2241.66
```
The variable x is defined, and it processes for each line; however, no display is generated until all processing has completed  
While it's useful as a standalone routine, it an also be put with the earlier listing to add even more information and a more complete report: 
```
$ awk '{x=x+($2*$3)} {print $1,"QTY: "$2,"PRICE: 
    "$3,"TOTAL: "$2*$3} END {print "Total Value of Inventory: " x}' inventory

hammers QTY: 5 PRICE: 7.99 TOTAL: 39.95
drills QTY: 2 PRICE: 29.99 TOTAL: 59.98
punches QTY: 7 PRICE: 3.59 TOTAL: 25.13
drifts QTY: 2 PRICE: 4.09 TOTAL: 8.18
bits QTY: 55 PRICE: 1.19 TOTAL: 65.45
saws QTY: 123 PRICE: 14.99 TOTAL: 1843.77
nails QTY: 800 PRICE: .19 TOTAL: 152
screws QTY: 80 PRICE: .29 TOTAL: 23.2
brads QTY: 100 PRICE: .24 TOTAL: 24
Total Value of Inventory: 2241.66
```
The BEGIN command words in the same fashion as END, but it establishes items that need to be done before anything else is accomplished  
The most common purpose of this procedure is to create headers for reports. The syntax for this routine would resemble 
```
$ awk 'BEGIN {print "ITEM   QUANTITY   PRICE   TOTAL"}'
```
### Input, Output, and Source Files 
The AWK tool can read its input from a file, as was done in all examples up to this point, or it can take input from the output of another command. For example: 
```
$ sort emp_names | awk '{print $3,$2}'
```  
The input of the awk command is the output from the sort operation. In addition to sort, any other Linux command can be used — for example, grep  
This procedure allows you to perform other operations on the file before pulling out selected fields  
Like the shell, AWK uses the output-redirection operators > and >> to put its output into a file rather than to standard output  
The symbols react like their counterparts in the shell, so > creates the file if it doesn't exist, and >> appends to the existing file. Examine the following example:  
```
$ awk '{print NR, $1 ) > "/tmp/filez" }' emp_names
$ cat /tmp/filez
1	46012
2	46013
3	46015
4	46017
5	46018
6	46019
7	46021
8	46022
9	46024
10	46026
11	46027
12	46029
```  
Examining the syntax of the statement, you can see that the output redirection is done after the print statement is complete  
You must enclose the file name in quotes, or else it is simply an uninitialized AWK variable, and the combination of instructions generates an error from AWK. (If you use the redirection symbols improperly, AWK gets confused about whether the symbol means "redirection" or is a relation operator.) 
Output into pipes in AWK also resembles the way the same action would be accomplished in a shell. To send the output of a print command into a pipe, follow the print command with a pipe symbol and the name of the command, as in the following: 
```
$ awk '{ print $2 | "sort" }' emp_names
BOGUE
BUCK
DULANEY
DURHAM
FELDMAN
FERGUS
JUNE
KANE
STEEN
SWIM
TUTTLE
WOOD
```  
As was the case with output redirection, you must enclose the command in quotes, and the name of the pipe is the name of the command being executed  
Commands used by AWK can come from two locations. First, they can be specified on the command line, as illustrated. Second, they can come from a source file. If such is the case, AWK is alerted to this occurrence by means of the -f option. To illustrate: 
```
$ cat awklist
{print $3,$2}
{print $4,$5,"\n"}
```
```
$ awk -f awklist emp_names
EVAN DULANEY
MOBILE AL

JEFF DURHAM
MOBILE AL

BILL STEEN
MOBILE AL

EVAN FELDMAN
MOBILE AL

STEVE SWIM
UNKNOWN AL

ROBERT BOGUE
PHOENIX AR

MICAH JUNE
PHOENIX AR

SHERYL KANE
UNKNOWN AR

WILLIAM WOOD
MUNCIE IN

SARAH FERGUS
MUNCIE IN

SARAH BUCK
MUNCIE IN

BOB TUTTLE
MUNCIE IN
```
Notice that the apostrophes are not used anywhere within the source file or when calling it at the command line. They are only for use in differentiating the commands on the command line from file names  
If simple output cannot handle the intricate detail you want in your programs, try the more complex output available with the printf command, the syntax for which is  
```
printf( format, value, value ...)
```
This syntax is like that of the printf command in the C language, and the specifications for the format are the same
You define the format by inserting a specification that defines how the value is to be printed  
The format specification consists of a % followed by a letter. Like the print command, printf does not have to be enclosed in parentheses, but using them is considered good practice  
The following table lists the various specifications available for the printf command  
Specification	Description  
```
%c	Prints a single ASCII character
%d	Prints a decimal number
%e	Prints a scientific notation representation of numbers
%f	Prints a floating-point representation
%g	Prints %e or %f; whichever is shorter
%o	Prints an unsigned octal number
%s	Prints an ASCII string
%x	Prints an unsigned hexadecimal number
%%	Prints a percent sign; no conversion is performed
```
You can supply some additional formatting parameters between the % and the character. These parameters further refine how the value is printed: 
#### Parameter	Description
-	Left justifies the expression in the field
width	Pads the field to the specified width as needed (a leading zero pads the field with zeros) 
.prec	Maximum string width or the maximum number of digits to the right of the decimal point

The printf command enables you to control and translate the value from one format to another. When you want to print the value of a variable, simply provide a specification that instructs printf how to print the information (normally enclosed in double quotes). You must include a specification parameter for each variable that is being passed to printf; if too few parameters are included, printf does not print all the values. 
Working Around Errors 
The AWK tool is notoriously annoying in the manner in which it reports errors that occurred. One error will prevent any of the operations from taking place, and the error given will be cryptic at best: 
awk: syntax error near line 2
awk: bailing out near line 2
You can spend hours looking at line 2 and trying to figure out why it prevented the program from running; this is one strong point in favor of using source files. 

There are two rules to keep in mind to help you avoid causing syntax errors: 
1. Make certain the commands are within brackets and the brackets are within apostrophes. Failure to use one of these characters ensures the program will not run. 
2. Search commands need to be within slashes. To find employees in Indiana, you must use "/IN/," not "IN." 
Conclusion 
While standing for something else altogether, AWK should be an acronym for Administrators' Wisdom Kit. Along with SED, the AWK utility is one of the most powerful and flexible tools a Linux administrator has. By learning some of its language idiosyncrasies, you can open a world of possibilities to simplify tasks that would otherwise be time-consuming and difficult, if not impossible. 


## Common threads: Awk by example, Part 1  
### In defense of awk  
In this series of articles, I'm going to turn you into a proficient awk coder. I'll admit, awk doesn't have a very pretty or particularly "hip" name, and the GNU version of awk, called gawk, sounds downright weird. Those unfamiliar with the language may hear "awk" and think of a mess of code so backwards and antiquated that it's capable of driving even the most knowledgeable UNIX guru to the brink of insanity (causing him to repeatedly yelp "kill -9!" as he runs for coffee machine). 
Sure, awk doesn't have a great name. But it is a great language. Awk is geared toward text processing and report generation, yet features many well-designed features that allow for serious programming. And, unlike some languages, awk's syntax is familiar, and borrows some of the best parts of languages like C, python, and bash (although, technically, awk was created before both python and bash). Awk is one of those languages that, once learned, will become a key part of your strategic coding arsenal.

### The first awk
Let's go ahead and start playing around with awk to see how it works. At the command line, enter the following command:  
```
$ awk '{ print }' /etc/passwd 
```  
You should see the contents of your /etc/passwd file appear before your eyes. Now, for an explanation of what awk did. When we called awk, we specified /etc/passwd as our input file. When we executed awk, it evaluated the print command for each line in /etc/passwd, in order. All output is sent to stdout, and we get a result identical to catting /etc/passwd. Now, for an explanation of the { print } code block. In awk, curly braces are used to group blocks of code together, similar to C. Inside our block of code, we have a single print command. In awk, when a print command appears by itself, the full contents of the current line are printed. 
Here is another awk example that does exactly the same thing:  
```
$ awk '{ print $0 }' /etc/passwd 
```
In awk, the $0 variable represents the entire current line, so print and print $0 do exactly the same thing. If you'd like, you can create an awk program that will output data totally unrelated to the input data. Here's an example:  
```
$ awk '{ print "" }' /etc/passwd 
```
Whenever you pass the "" string to the print command, it prints a blank line. If you test this script, you'll find that awk outputs one blank line for every line in your /etc/passwd file  
Again, this is because awk executes your script for every line in the input file. Here's another example:  
```
$ awk '{ print "hiya" }' /etc/passwd 
```
Running this script will fill your screen with hiya's. :) 
### Multiple fields  
Awk is really good at handling text that has been broken into multiple logical fields, and allows you to effortlessly reference each individual field from inside your awk script  
The following script will print out a list of all user accounts on your system:  
```
$ awk -F":" '{ print $1 }' /etc/passwd 
```
Above, when we called awk, we use the -F option to specify ":" as the field separator. When awk processes the print $1 command, it will print out the first field that appears on each line in the input file. Here's another example:  
```
$ awk -F":" '{ print $1 $3 }' /etc/passwd 
```
Here's an excerpt of the output from this script:  
```
halt7 
operator11 
root0 
shutdown6 
sync5 
bin1 
....etc. 
```
As you can see, awk prints out the first and third fields of the /etc/passwd file, which happen to be the username and uid fields respectively  
Now, while the script did work, it's not perfect -- there aren't any spaces between the two output fields! If you're used to programming in bash or python, you may have expected the print $1 $3 command to insert a space between the two fields  
However, when two strings appear next to each other in an awk program, awk concatenates them without adding an intermediate space. The following command will insert a space between both fields:  
```
$ awk -F":" '{ print $1 " " $3 }' /etc/passwd 
```
When you call print this way, it'll concatenate $1, " ", and $3, creating readable output. Of course, we can also insert some text labels if needed:  
```
$ awk -F":" '{ print "username: " $1 "\t\tuid:" $3" }' /etc/passwd 
```
```
This will cause the output to be:
username: halt     uid:7 
username: operator uid:11 
username: root     uid:0 
username: shutdown uid:6 
username: sync     uid:5 
username: bin      uid:1 
....etc.
```

### External scripts
Passing your scripts to awk as a command line argument can be very handy for small one-liners, but when it comes to complex, multi-line programs, you'll definitely want to compose your script in an external file  
Awk can then be told to source this script file by passing it the -f option:  
```
$ awk -f myscript.awk myfile.in 
```
Putting your scripts in their own text files also allows you to take advantage of additional awk features  
For example, this multi-line script does the same thing as one of our earlier one-liners, printing out the first field of each line in /etc/passwd:  
```
BEGIN { 
        FS=":" 
} 
{ print $1 } 
```
The difference between these two methods has to do with how we set the field separator  
In this script, the field separator is specified within the code itself (by setting the FS variable), while our previous example set FS by passing the -F":" option to awk on the command line  
It's generally best to set the field separator inside the script itself, simply because it means you have one less command line argument to remember to type  

### The BEGIN and END blocks  
Normally, awk executes each block of your script's code once for each input line. However, there are many programming situations where you may need to execute initialization code before awk begins processing the text from the input file  
For such situations, awk allows you to define a BEGIN block. We used a BEGIN block in the previous example  
Because the BEGIN block is evaluated before awk starts processing the input file, it's an excellent place to initialize the FS (field separator) variable, print a heading, or initialize other global variables that you'll reference later in the program  
Awk also provides another special block, called the END block. Awk executes this block after all lines in the input file have been processed  
Typically, the END block is used to perform final calculations or print summaries that should appear at the end of the output stream  

### Regular expressions and blocks
Awk allows the use of regular expressions to selectively execute an individual block of code, depending on whether or not the regular expression matches the current line  
Here's an example script that outputs only those lines that contain the character sequence foo:  
```
/foo/ { print } 
```
Of course, you can use more complicated regular expressions. Here's a script that will print only lines that contain a floating point number:  
```
/[0-9]+\.[0-9]*/ { print } 
```
### Expressions and blocks
There are many other ways to selectively execute a block of code. We can place any kind of boolean expression before a code block to control when a particular block is executed  
Awk will execute a code block only if the preceding boolean expression evaluates to true  
The following example script will output the third field of all lines that have a first field equal to fred  
If the first field of the current line is not equal to fred, awk will continue processing the file and will not execute the print statement for the current line:  
```
$1 == "fred" { print $3 }
```
Awk offers a full selection of comparison operators, including the usual "==", "<", ">", "<=", ">=", and "!="  
In addition, awk provides the "~" and "!~" operators, which mean "matches" and "does not match"  
They're used by specifying a variable on the left side of the operator, and a regular expression on the right side  
Here's an example that will print only the third field on the line if the fifth field on the same line contains the character sequence root:  
```
$5 ~ /root/ { print $3 } 
```
### Conditional statements  
Awk also offers very nice C-like if statements. If you'd like, you could rewrite the previous script using an if statement:  
```
{ 
  if ( $5 ~ /root/ ) { 
          print $3 
  } 
} 
```  
Both scripts function identically. In the first example, the boolean expression is placed outside the block, while in the second example, the block is executed for every input line, and we selectively perform the print command by using an if statement  
Both methods are available, and you can choose the one that best meshes with the other parts of your script  
Here's a more complicated example of an awk if statement  
As you can see, even with complex, nested conditionals, if statements look identical to their C counterparts:  
```
{ 
  if ( $1 == "foo" ) { 
           if ( $2 == "foo" ) { 
                    print "uno" 
           } else { 
                    print "one" 
           } 
  } else if ($1 == "bar" ) { 
           print "two" 
  } else { 
           print "three" 
  } 
} 
```
Using if statements, we can also transform this code:  
```
! /matchme/ { print $1 $3 $4 }
```  
to this:  
```
{ 
  if ( $0 !~ /matchme/ ) { 
          print $1 $3 $4 
  } 
} 
```  
Both scripts will output only those lines that don't contain a matchme character sequence. Again, you can choose the method that works best for your code. They both do the same thing  
Awk also allows the use of boolean operators "||" (for "logical or") and "&&"(for "logical and") to allow the creation of more complex boolean expressions:  
```
( $1 == "foo" ) && ( $2 == "bar" ) { print } 
```
This example will print only those lines where field one equals foo and field two equals bar   

### Numeric variables!  
So far, we've either printed strings, the entire line, or specific fields. However, awk also allows us to perform both integer and floating point math. Using mathematical expressions, it's very easy to write a script that counts the number of blank lines in a file  
Here's one that does just that:
```
BEGIN { x=0 } 
/^$/  { x=x+1 } 
END   { print "I found " x " blank lines. :)" } 
```
In the BEGIN block, we initialize our integer variable x to zero. Then, each time awk encounters a blank line, awk will execute the x=x+1 statement, incrementing x. After all the lines have been processed, the END block will execute, and awk will print out a final summary, specifying the number of blank lines it found    
### Stringy variables  
One of the neat things about awk variables is that they are "simple and stringy."  
I consider awk variables "stringy" because all awk variables are stored internally as strings  
At the same time, awk variables are "simple" because you can perform mathematical operations on a variable, and as long as it contains a valid numeric string, awk automatically takes care of the string-to-number conversion steps  
To see what I mean, check out this example:  
```
x="1.01" 
# We just set x to contain the *string* "1.01" 
x=x+1 
# We just added one to a *string* 
print x 
# Incidentally, these are comments :) 
```
```
Awk will output:
2.01
```
Interesting! Although we assigned the string value 1.01 to the variable x, we were still able to add one to it. We wouldn't be able to do this in bash or python  
First of all, bash doesn't support floating point arithmetic. And, while bash has "stringy" variables, they aren't "simple"; to perform any mathematical operations, bash requires that we enclose our math in an ugly $( ) construct  
If we were using python, we would have to explicitly convert our 1.01 string to a floating point value before performing any arithmetic on it  
While this isn't difficult, it's still an additional step. With awk, it's all automatic, and that makes our code nice and clean  
If we wanted to square and add one to the first field in each input line, we would use this script:  
```
{ print ($1^2)+1 } 
```
If you do a little experimenting, you'll find that if a particular variable doesn't contain a valid number, awk will treat that variable as a numerical zero when it evaluates your mathematical expression  

### Lots of operators
Another nice thing about awk is its full complement of mathematical operators  
In addition to standard addition, subtraction, multiplication, and division, awk allows us to use the previously demonstrated exponent operator "^", the modulo (remainder) operator "%", and a bunch of other handy assignment operators borrowed from C  
These include pre- and post-increment/decrement ( i++, --foo ), add/sub/mult/div assign operators ( a+=3, b*=2, c/=2.2, d-=6.2 )  
But that's not all -- we also get handy modulo/exponent assign ops as well ( a^=2, b%=4 )  

### Field separators
Awk has its own complement of special variables. Some of them allow you to fine-tune how awk functions, while others can be read to glean valuable information about the input. We've already touched on one of these special variables, FS  
As mentioned earlier, this variable allows you to set the character sequence that awk expects to find between fields. When we were using /etc/passwd as input, FS was set to ":"  
While this did the trick, FS allows us even more flexibility  
The FS value is not limited to a single character; it can also be set to a regular expression, specifying a character pattern of any length. If you're processing fields separated by one or more tabs, you'll want to set FS like so:  
```
FS="\t+" 
```
Above, we use the special "+" regular expression character, which means "one or more of the previous character". 
If your fields are separated by whitespace (one or more spaces or tabs), you may be tempted to set FS to the following regular expression:  
```
FS="[[:space:]+]" 
```
While this assignment will do the trick, it's not necessary  
Why?  
Because by default, FS is set to a single space character, which awk interprets to mean "one or more spaces or tabs." In this particular example, the default FS setting was exactly what you wanted in the first place! 
Complex regular expressions are no problem. Even if your records are separated by the word "foo," followed by three digits, the following regular expression will allow your data to be parsed properly:  
```
FS="foo[0-9][0-9][0-9]" 
```
### Number of fields
The next two variables we're going to cover are not normally intended to be written to, but are normally read and used to gain useful information about the input  
The first is the NF variable, also called the "number of fields" variable. Awk will automatically set this variable to the number of fields in the current record  
You can use the NF variable to display only certain input lines:  
```
NF == 3 { print "this particular record has three fields: " $0 } 
```
Of course, you can also use the NF variable in conditional statements, as follows:  
```
{ 
  if ( NF > 2 ) { 
          print $1 " " $2 ":" $3 
  } 
} 
```
### Record number
The record number (NR) is another handy variable. It will always contain the number of the current record (awk counts the first record as record number 1)  
Up until now, we've been dealing with input files that contain one record per line. For these situations, NR will also tell you the current line number  
However, when we start to process multi-line records later in the series, this will no longer be the case, so be careful! NR can be used like the NF variable to print only certain lines of the input:  
```
(NR < 10 ) || (NR > 100) { print "We are on record number 1-9 or 101+" } 
```
Another example:  
```
{ 
  #skip header 
  if ( NR > 10 ) { 
          print "ok, now for the real information!" 
  } 
} 
```
## Common threads: Awk by example, Part 2
### Multi-line records
Awk is an excellent tool for reading in and processing structured data, such as the system's /etc/passwd file. /etc/passwd is the UNIX user database, and is a colon-delimited text file, containing a lot of important information, including all existing user accounts and user IDs, among other things. In my previous article, I showed you how awk could easily parse this file. All we had to do was to set the FS (field separator) variable to ":".
By setting the FS variable correctly, awk can be configured to parse almost any kind of structured data, as long as there is one record per line. However, just setting FS won't do us any good if we want to parse a record that exists over multiple lines. In these situations, we also need to modify the RS record separator variable. The RS variable tells awk when the current record ends and a new record begins.
As an example, let's look at how we'd handle the task of processing an address list of Federal Witness Protection Program participants:
Jimmy the Weasel
100 Pleasant Drive
San Francisco, CA 12345
Big Tony
200 Incognito Ave.
Suburbia, WA 67890

Ideally, we'd like awk to recognize each 3-line address as an individual record, rather than as three separate records. It would make our code a lot simpler if awk would recognize the first line of the address as the first field ($1), the street address as the second field ($2), and the city, state, and zip code as field $3. The following code will do just what we want:
BEGIN {
	FS="\n"
	RS=""
}

Above, setting FS to "\n" tells awk that each field appears on its own line. By setting RS to "", we also tell awk that each address record is separated by a blank line. Once awk knows how the input is formatted, it can do all the parsing work for us, and the rest of the script is simple. Let's look at a complete script that will parse this address list and print out each address record on a single line, separating each field with a comma. 
BEGIN {
	FS="\n"
	RS=""
}
{
	print $1 ", " $2 ", " $3
}

If this script is saved as address.awk, and the address data is stored in a file called address.txt, you can execute this script by typing "awk -f address.awk address.txt". This code produces the following output:
Jimmy the Weasel, 100 Pleasant Drive, San Francisco, CA 12345
Big Tony, 200 Incognito Ave., Suburbia, WA 67890

OFS and ORS
In address.awk's print statement, you can see that awk concatenates (joins) strings that are placed next to each other on a line. We used this feature to insert a comma and a space (", ") between the three address fields that appeared on the line. While this method works, it's a bit ugly looking. Rather than inserting literal ", " strings between our fields, we can have awk do it for us by setting a special awk variable called OFS. Take a look at this code snippet.
print "Hello", "there", "Jim!"

The commas on this line are not part of the actual literal strings. Instead, they tell awk that "Hello", "there", and "Jim!" are separate fields, and that the OFS variable should be printed between each string. By default, awk produces the following output:
Hello there Jim!

This shows us that by default, OFS is set to " ", a single space. However, we can easily redefine OFS so that awk will insert our favorite field separator. Here's a revised version of our original address.awk program that uses OFS to output those intermediate ", " strings:
BEGIN {
	FS="\n"
	RS=""
	OFS=", "
}
{
	print $1, $2, $3
}

Awk also has a special variable called ORS, called the "output record separator". By setting ORS, which defaults to a newline ("\n"), we can control the character that's automatically printed at the end of a print statement. The default ORS value causes awk to output each new print statement on a new line. If we wanted to make the output double-spaced, we would set ORS to "\n\n". Or, if we wanted records to be separated by a single space (and no newline), we would set ORS to " ". 
________________________________________
Multi-line to tabbed
Let's say that we wrote a script that converted our address list to a single-line per record, tab-delimited format for import into a spreadsheet. After using a slightly modified version of address.awk, it would become clear that our program only works for three-line addresses. If awk encountered the following address, the fourth line would be thrown away and not printed:
Cousin Vinnie
Vinnie's Auto Shop
300 City Alley
Sosueme, OR 76543

To handle situations like this, it would be good if our code took the number of records per field into account, printing each one in order. Right now, the code only prints the first three fields of the address. Here's some code that does what we want:
BEGIN { 
    FS="\n" 
    RS="" 
    ORS="" 
} 
 
{  
        x=1 
        while ( x<NF ) { 
                print $x "\t" 
                x++ 
        } 
        print $NF "\n" 
} 

First, we set the field separator FS to "\n" and the record separator RS to "" so that awk parses the multi-line addresses correctly, as before. Then, we set the output record separator ORS to "", which will cause the print statement to not output a newline at the end of each call. This means that if we want any text to start on a new line, we need to explicitly write print "\n". 
In the main code block, we create a variable called x that holds the number of current field that we're processing. Initially, it's set to 1. Then, we use a while loop (an awk looping construct identical to that found in the C language) to iterate through all but the last record, printing the record and a tab character. Finally, we print the last record and a literal newline; again, since ORS is set to "", print won't output newlines for us. Program output looks like this, which is exactly what we wanted:

Our intended output. Not pretty, but tab delimited for easy import into a spreadsheet
Jimmy the Weasel        100 Pleasant Drive      San Francisco, CA 12345 
Big Tony        200 Incognito Ave.      Suburbia, WA 67890
Cousin Vinnie   Vinnie's Auto Shop      300 City Alley  Sosueme, OR 76543


Looping constructs
We've already seen awk's while loop construct, which is identical to its C counterpart. Awk also has a "do...while" loop that evaluates the condition at the end of the code block, rather than at the beginning like a standard while loop. It's similar to "repeat...until" loops that can be found in other languages. Here's an example:

do...while example
{
	count=1
	do {
		print "I get printed at least once no matter what" 
	} while ( count != 1 )
}

Because the condition is evaluated after the code block, a "do...while" loop, unlike a normal while loop, will always execute at least once. On the other hand, a normal while loop will never execute if its condition is false when the loop is first encountered. 


for loops 
Awk allows you to create for loops, which like while loops are identical to their C counterpart:
for ( initial assignment; comparison; increment ) {
	code block
}

Here's a quick example:
for ( x = 1; x <= 4; x++ ) {
	print "iteration",x
}

This snippet will print:
iteration 1
iteration 2
iteration 3
iteration 4

________________________________________
Break and continue
Again, just like C, awk provides break and continue statements. These statements provide better control over awk's various looping constructs. Here's a code snippet that desperately needs a break statement:
while (1) {
	print "forever and ever..."
}

Because 1 is always true, this while loop runs forever. Here's a loop that only executes ten times:
x=1
while(1) {
	print "iteration",x
	if ( x == 10 ) {
		break
	}
	x++
}

Here, the break statement is used to "break out" of the innermost loop. "break" causes the loop to immediately terminate and execution to continue at the line after the loop's code block. 
The continue statement complements break, and works like this:
x=1
while (1) {
	if ( x == 4 ) {
		x++
		continue
	}
	print "iteration",x
	if ( x > 20 ) {
		break
	}
	x++
}

This code will print "iteration 1" through "iteration 21", except for "iteration 4". If iteration equals 4, x is incremented and the continue statement is called, which immediately causes awk to start to the next loop iteration without executing the rest of the code block. The continue statement works for every kind of awk iterative loop, just as break does. When used in the body of a for loop, continue will cause the loop control variable to be automatically incremented. Here's an equivalent for loop:
for ( x=1; x<=21; x++ ) {
	if ( x == 4 ) {
		continue
	}
	print "iteration",x
}

It wasn't necessary to increment x just before calling continue as it was in our while loop, since the for loop increments x automatically. 
________________________________________
Arrays
You'll be pleased to know that awk has arrays. However, under awk, it's customary to start array indices at 1, rather than 0:
myarray[1]="jim"
myarray[2]=456

When awk encounters the first assignment, myarray is created and the element myarray[1] is set to "jim". After the second assignment is evaluated, the array has two elements. 
Iterating over arrays 
Once defined, awk has a handy mechanism to iterate over the elements of an array, as follows:
for ( x in myarray ) {
	print myarray[x]
}

This code will print out every element in the array myarray. When you use this special "in" form of a for loop, awk will assign every existing index of myarray to x (the loop control variable) in turn, executing the loop's code block once after each assignment. While this is a very handy awk feature, it does have one drawback -- when awk cycles through the array indices, it doesn't follow any particular order. That means that there's no way for us to know whether the output of above code will be:
jim
456

or
456
jim

To loosely paraphrase Forrest Gump, iterating over the contents of an array is like a box of chocolates -- you never know what you're going to get. This has something to do with the "stringiness" of awk arrays, which we'll now take a look at. 
________________________________________
Array index stringiness
In my previous article, I showed you that awk actually stores numeric values in a string format. While awk performs the necessary conversions to make this work, it does open the door for some odd-looking code:
a="1"
b="2"
c=a+b+3

After this code executes, c is equal to 6. Since awk is "stringy", adding strings "1" and "2" is functionally no different than adding the numbers 1 and 2. In both cases, awk will successfully perform the math. Awk's "stringy" nature is pretty intriguing -- you may wonder what happens if we use string indexes for arrays. For instance, take the following code:
myarr["1"]="Mr. Whipple"
print myarr["1"]

As you might expect, this code will print "Mr. Whipple". But how about if we drop the quotes around the second "1" index?
myarr["1"]="Mr. Whipple"
print myarr[1]

Guessing the result of this code snippet is a bit more difficult. Does awk consider myarr["1"] and myarr[1] to be two separate elements of the array, or do they refer to the same element? The answer is that they refer to the same element, and awk will print "Mr. Whipple", just as in the first code snippet. Although it may seem strange, behind the scenes awk has been using string indexes for its arrays all this time! 
After learning this strange fact, some of us may be tempted to execute some wacky code that looks like this:
myarr["name"]="Mr. Whipple"
print myarr["name"]

Not only does this code not raise an error, but it's functionally identical to our previous examples, and will print "Mr. Whipple" just as before! As you can see, awk doesn't limit us to using pure integer indexes; we can use string indexes if we want to, without creating any problems. Whenever we use non-integer array indices like myarr["name"], we're using associative arrays. Technically, awk isn't doing anything different behind the scenes than when we use a string index (since even if you use an "integer" index, awk still treats it as a string). However, you should still call 'em associative arrays -- it sounds cool and will impress your boss. The stringy index thing will be our little secret. ;) 
________________________________________


Array tools
When it comes to arrays, awk gives us a lot of flexibility. We can use string indexes, and we aren't required to have a continuous numeric sequence of indices (for example, we can define myarr[1] and myarr[1000], but leave all other elements undefined). While all this can be very helpful, in some circumstances it can create confusion. Fortunately, awk offers a couple of handy features to help make arrays more manageable. 
First, we can delete array elements. If you want to delete element 1 of your array fooarray, type:
delete fooarray[1]

And, if you want to see if a particular array element exists, you can use the special "in" boolean operator as follows:
if ( 1 in fooarray ) {
	print "Ayep!  It's there."
} else {
	print "Nope!  Can't find it."





































Common threads: Awk by example, Part 3
Formatting output
While awk's print statement does do the job most of the time, sometimes more is needed. For those times, awk offers two good old friends called printf() and sprintf(). Yes, these functions, like so many other awk parts, are identical to their C counterparts. printf() will print a formatted string to stdout, while sprintf() returns a formatted string that can be assigned to a variable. If you're not familiar with printf() and sprintf(), an introductory C text will quickly get you up to speed on these two essential printing functions. You can view the printf() man page by typing "man 3 printf" on your Linux system.
Here's some sample awk sprintf() and printf() code. As you can see, everything looks almost identical to C.
x=1
b="foo"
printf("%s got a %d on the last test\n","Jim",83)
myout=("%s-%d",b,x)
print myout

This code will print:
Jim got a 83 on the last test
foo-1

________________________________________
String functions
Awk has a plethora of string functions, and that's a good thing. In awk, you really need string functions, since you can't treat a string as an array of characters as you can in other languages like C, C++, and Python. For example, if you execute the following code:
mystring="How are you doing today?"
print mystring[3]

You'll receive an error that looks something like this: 
awk: string.gawk:59: fatal: attempt to use scalar as array

Oh, well. While not as convenient as Python's sequence types, awk's string functions get the job done. Let's take a look at them. 
First, we have the basic length() function, which returns the length of a string. Here's how to use it: 
print length(mystring)

This code will print the value: 
24

OK, let's keep going. The next string function is called index, and will return the position of the occurrence of a substring in another string, or it will return 0 if the string isn't found. Using mystring, we can call it this way: 
print index(mystring,"you")

Awk prints: 
9

We move on to two more easy functions, tolower() and toupper(). As you might guess, these functions will return the string with all characters converted to lowercase or uppercase respectively. Notice that tolower() and toupper() return the new string, and don't modify the original. This code: 
print tolower(mystring)
print toupper(mystring)
print mystring

....will produce this output: 
how are you doing today?
HOW ARE YOU DOING TODAY?
How are you doing today?

So far so good, but how exactly do we select a substring or even a single character from a string? That's where substr() comes in. Here's how to call substr(): 
	
mysub=substr(mystring,startpos,maxlen)

mystring should be either a string variable or a literal string from which you'd like to extract a substring. startpos should be set to the starting character position, and maxlen should contain the maximum length of the string you'd like to extract. Notice that I said maximum length; if length(mystring) is shorter than startpos+maxlen, your result will be truncated. substr() won't modify the original string, but returns the substring instead. Here's an example: 
print substr(mystring,9,3)

Awk will print: 
you

If you regularly program in a language that uses array indices to access parts of a string (and who doesn't), make a mental note that substr() is your awk substitute. You'll need to use it to extract single characters and substrings; because awk is a string-based language, you'll be using it often. 
Now, we move on to some meatier functions, the first of which is called match(). match() is a lot like index(), except instead of searching for a substring like index() does, it searches for a regular expression. The match() function will return the starting position of the match, or zero if no match is found. In addition, match() will set two variables called RSTART and RLENGTH. RSTART contains the return value (the location of the first match), and RLENGTH specifies its span in characters (or -1 if no match was found). Using RSTART, RLENGTH, substr(), and a small loop, you can easily iterate through every match in your string. Here's an example match() call: 
print match(mystring,/you/), RSTART, RLENGTH

Awk will print: 
9 9 3

________________________________________
String substitution
Now, we're going to look at a couple of string substitution functions, sub() and gsub(). These guys differ slightly from the functions we've looked at so far in that they actually modify the original string. Here's a template that shows how to call sub(): 
sub(regexp,replstring,mystring)

When you call sub(), it'll find the first sequence of characters in mystring that matches regexp, and it'll replace that sequence with replstring. sub() and gsub() have identical arguments; the only way they differ is that sub() will replace the first regexp match (if any), and gsub() will perform a global replace, swapping out all matches in the string. Here's an example sub() and gsub() call: 
sub(/o/,"O",mystring)
print mystring
mystring="How are you doing today?"
gsub(/o/,"O",mystring)
print mystring

We had to reset mystring to its original value because the first sub() call modified mystring directly. When executed, this code will cause awk to output: 
HOw are you doing today?
HOw are yOu dOing tOday?

Of course, more complex regular expressions are possible. I'll leave it up to you to test out some complicated regexps. 
We wrap up our string function coverage by introducing you to a function called split(). split()'s job is to "chop up" a string and place the various parts into an integer-indexed array. Here's an example split() call: 
numelements=split("Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",mymonths,",")

When calling split(), the first argument contains the literal string or string variable to be chopped. In the second argument, you should specify the name of the array that split() will stuff the chopped parts into. In the third element, specify the separator that will be used to chop the strings up. When split() returns, it'll return the number of string elements that were split. split() assigns each one to an array index starting with one, so the following code: 
print mymonths[1],mymonths[numelements]

....will print: 
Jan Dec

________________________________________


Special string forms
A quick note -- when calling length(), sub(), or gsub(), you can drop the last argument and awk will apply the function call to $0 (the entire current line). To print the length of each line in a file, use this awk script: 
		
	
{
	print length() 
}

________________________________________
Financial fun
A few weeks ago, I decided to write my own checkbook balancing program in awk. I decided that I'd like to have a simple tab-delimited text file into which I can enter my most recent deposits and withdrawals. The idea was to hand this data to an awk script that would automatically add up all the amounts and tell me my balance. Here's how I decided to record all my transactions into my "ASCII checkbook": 
23 Aug 2000	food	-	-	Y	Jimmy's Buffet		30.25

Every field in this file is separated by one or more tabs. After the date (field 1, $1), there are two fields called "expense category" and "income category". When I'm entering an expense like on the above line, I put a four-letter nickname in the exp field, and a "-" (blank entry) in the inc field. This signifies that this particular item is a "food expense" :) Here's what a deposit looks like: 
23 Aug 2000	-	inco	-	Y	Boss Man		2001.00

In this case, I put a "-" (blank) in the exp category, and put "inco" in the inc category. "inco" is my nickname for generic (paycheck-style) income. Using category nicknames allows me to generate a breakdown of my income and expenditures by category. As far as the rest of the records, all the other fields are fairly self-explanatory. The cleared? field ("Y" or "N") records whether the transaction has been posted to my account; beyond that, there's a transaction description, and a positive dollar amount. 
The algorithm used to compute the current balance isn't too hard. Awk simply needs to read in each line, one by one. If an expense category is listed but there is no income category (it's "-"), then this item is a debit. If an income category is listed, but no expense category ("-") is there, then the dollar amount is a credit. And, if there is both an expense and income category listed, then this amount is a "category transfer"; that is, the dollar amount will be subtracted from the expense category and added to the income category. Again, all these categories are virtual, but are very useful for tracking income and expenditures, as well as for budgeting. 
________________________________________




The code
Time to look at the code. We'll start off with the first line, the BEGIN block and a function definition: 

balance, part 1
	
#!/usr/bin/env awk -f
BEGIN {
	FS="\t+"
	months="Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec"
}

function monthdigit(mymonth) {
	return (index(months,mymonth)+3)/4
}

Adding the first "#!..." line to any awk script will allow it to be directly executed from the shell, provided that you "chmod +x myscript" first. The remaining lines define our BEGIN block, which gets executed before awk starts processing our checkbook file. We set FS (the field separator) to "\t+", which tells awk that the fields will be separated by one or more tabs. In addition, we define a string called months that's used by our monthdigit() function, which appears next. 
The last three lines show you how to define your own awk function. The format is simple -- type "function", then the function name, and then the parameters separated by commas, inside parentheses. After this, a "{ }" code block contains the code that you'd like this function to execute. All functions can access global variables (like our months variable). In addition, awk provides a "return" statement that allows the function to return a value, and operates similarly to the "return" found in C, Python, and other languages. This particular function converts a month name in a 3-letter string format into its numeric equivalent. For example, this: 
print monthdigit("Mar")

....will print this: 
3

Now, let's move on to some more functions. 
________________________________________
Financial functions
Here are three more functions that perform the bookkeeping for us. Our main code block, which we'll see soon, will process each line of the checkbook file sequentially, calling one of these functions so that the appropriate transactions are recorded in an awk array. There are three basic kinds of transactions, credit (doincome), debit (doexpense) and transfer (dotransfer). You'll notice that all three functions accept one argument, called mybalance. mybalance is a placeholder for a two-dimensional array, which we'll pass in as an argument. Up until now, we haven't dealt with two-dimensional arrays; however, as you can see below, the syntax is quite simple. Just separate each dimension with a comma, and you're in business. 
We'll record information into "mybalance" as follows. The first dimension of the array ranges from 0 to 12, and specifies the month, or zero for the entire year. Our second dimension is a four-letter category, like "food" or "inco"; this is the actual category we're dealing with. So, to find the entire year's balance for the food category, you'd look in mybalance[0,"food"]. To find June's income, you'd look in mybalance[6,"inco"]. 

balance, part 2
			
		
function doincome(mybalance) {
	mybalance[curmonth,$3] += amount
	mybalance[0,$3] += amount	
}

function doexpense(mybalance) {
	mybalance[curmonth,$2] -= amount
	mybalance[0,$2] -= amount	
}

function dotransfer(mybalance) {
	mybalance[0,$2] -= amount
	mybalance[curmonth,$2] -= amount
	mybalance[0,$3] += amount
	mybalance[curmonth,$3] += amount
}

When doincome() or any of the other functions are called, we record the transaction in two places -- mybalance[0,category] and mybalance[curmonth, category], the entire year's category balance and the current month's category balance, respectively. This allows us to easily generate either an annual or monthly breakdown of income/expenditures later on. 
If you look at these functions, you'll notice that the array referenced by mybalance is passed in my reference. In addition, we also refer to several global variables: curmonth, which holds the numeric value of the month of the current record, $2 (the expense category), $3 (the income category), and amount ($7, the dollar amount). When doincome() and friends are called, all these variables have already been set correctly for the current record (line) being processed. 
________________________________________
The main block
Here's the main code block that contains the code that parses each line of input data. Remember, because we have set FS correctly, we can refer to the first field as $1, the second field as $2, etc. When doincome() and friends are called, the functions can access the current values of curmonth, $2, $3 and amount from inside the function. Take a look at the code and meet me on the other side for an explanation. 

balance, part 3
			
		
{
	curmonth=monthdigit(substr($1,4,3))
	amount=$7
	
	#record all the categories encountered
	if ( $2 != "-" )
		globcat[$2]="yes"
	if ( $3 != "-" )
		globcat[$3]="yes"

	#tally up the transaction properly
	if ( $2 == "-" ) {
		if ( $3 == "-" ) {
			print "Error: inc and exp fields are both blank!"
			exit 1
		} else {
			#this is income
			doincome(balance)
			if ( $5 == "Y" )
				doincome(balance2)
		}
	} else if ( $3 == "-" ) {
		#this is an expense 
		doexpense(balance)
		if ( $5 == "Y" ) 
			doexpense(balance2)
	} else {
		#this is a transfer
		dotransfer(balance)
		if ( $5 == "Y" ) 
			dotransfer(balance2)
	}			
}

In the main block, the first two lines set curmonth to an integer between 1 and 12, and set amount to field 7 (to make the code easier to understand). Then, we have four interesting lines, where we write values into an array called globcat. globcat, or the global categories array, is used to record all those categories encountered in the file -- "inco", "misc", "food", "util", etc. For example, if $2 == "inco", we set globcat["inco"] to "yes". Later on, we can iterate through our list of categories with a simple "for (x in globcat)" loop. 
On the next twenty or so lines, we analyze fields $2 and $3, and record the transaction appropriately. If $2=="-" and $3!="-", we have some income, so we call doincome(). If the situation is reversed, we call doexpense(); and if both $2 and $3 contain categories, we call dotransfer(). Each time, we pass the "balance" array to these functions so that the appropriate data is recorded there. 
You'll also notice several lines that say "if ( $5 == "Y" ), record that same transaction in balance2". What exactly are we doing here? You'll recall that $5 contains either a "Y" or a "N", and records whether the transaction has been posted to the account. Because we record the transaction to balance2 only if the transaction has been posted, balance2 will contain the actual account balance, while "balance" will contain all transactions, whether they have been posted or not. You can use balance2 to verify your data entry (since it should match with your current account balance according to your bank), and use "balance" to make sure that you don't overdraw your account (since it will take into account any checks you have written that have not yet been cashed). 
________________________________________




Generating the report
After the main block repeatedly processes each input record, we now have a fairly comprehensive record of debits and credits broken down by category and by month. Now, all we need to do is define an END block that will generate a report, in this case a modest one: 
END {
	bal=0
	bal2=0	
	for (x in globcat) {
		bal=bal+balance[0,x]
		bal2=bal2+balance2[0,x]    
    	}
    	printf("Your available funds: %10.2f\n", bal)
    	printf("Your account balance: %10.2f\n", bal2)	
}

This report prints out a summary that looks something like this: 
Your available funds:    1174.22
Your account balance:    2399.33

In our END block, we used the "for (x in globcat)" construct to iterate through every category, tallying up a master balance based on all the transactions recorded. We actually tally up two balances, one for available funds, and another for the account balance. To execute the program and process your own financial goodies that you've entered into a file called "mycheckbook.txt", put all the above code into a text file called "balance", "chmod +x balance", and then type "./balance mycheckbook.txt". The balance script will then add up all your transactions and print out a two-line balance summary for you. 


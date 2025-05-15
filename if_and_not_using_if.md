### the same  
```
awk '$2 ~ /pattern/ { print }' file
awk '{ if($2 ~ /pattern/) print}' file
```

```
awk '{if ($3 < 1980) print $3, "    ",$5,$6,$7,$8}' file
awk '($3 < 1980) {print $3, "    ",$5,$6,$7,$8}' file
awk '$3 < 1980 {print $3, "    ",$5,$6,$7,$8}' file
```

### Using if statements, we can also transform this code:  
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
### if conditions with print statements
### if conditions with one print statement

```
cat grades

Jones 2143 78 84 77
Gondrol 2321 56 58 45
RinRao 2122 38 37
Edwin 2537 87 97 95
Dayan 2415 30 47
```
### one print statement
### no print statements with conditions
### creating a single variable and assigning it a value for each condition
### print that variable
```
awk '{total=$3+$4+$5
avg=total/3
{ if ( avg >= 90 ) grade="A"
else if ( avg >= 80) grade ="B"
else if (avg >= 70) grade ="C"
else grade="D"
print $0,"=>",grade
}' grades
```
```
Jones 2143 78 84 77 => C
Gondrol 2321 56 58 45 => D
RinRao 2122 38 37 => D
Edwin 2537 87 97 95 => A
Dayan 2415 30 47 => D
```
### print statement with conditions
```
$ awk '{
if ($3 >=35 && $4 >= 35 && $5 >= 35)
	print $0,"=>","Pass";
else
	print $0,"=>","Fail";
}' grades
```
```
Jones 2143 78 84 77 => Pass
Gondrol 2321 56 58 45 => Pass
RinRao 2122 38 37 => Fail
Edwin 2537 87 97 95 => Pass
Dayan 2415 30 47 => Fail
```
################################################

###################################################################
# awk to output extra field dependent on conditions and operators #
###################################################################  
```
#!/usr/bin/gawk -f

BEGIN {FS=OFS="\t"
print "output date",strftime ("%D", systime () )
print ""
      }

{ if ($4 >= 1 && $4 < 31)
  $5="1-30_code"

else if ($4 >= 31 && $4 < 61)
 $5="31-60_code"

else if ($4 >= 61 && $4 <= 99)
 $5="61-99_code"

else if ($4 >= 100)
 $5="100+code"

else $5="no count entered for this line"
  
 print $0
}
```

```
output date     xx/xx/xxxx
alice in chains album   flac    1       1-30_code  
pearl jam       album   flac    30      1-30_code  
nirvana album   mp3     0       no count entered for this line  
stp     single  mp3     32      31-60_code  
soundgarden     album   mp3     60      31-60_code  
tool    album   mp3     62      61-99_code  
green river     single  mp3     99      61-99_code  
temple of the dog       album   mp3     100     100+code  
mother love bone        album   mp3     100     100+code  
```

### note that nirvana gets the "no count entered for this line because, although not blank, the 4th field is 0 and doesn't meet any of the conditions

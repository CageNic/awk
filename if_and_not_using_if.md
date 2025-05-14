## the same  
```
awk '$2 ~ /pattern/ { print }' file
awk '{ if($2 ~ /pattern/) print}' file
```
```
awk '{if ($3 < 1980) print $3, "    ",$5,$6,$7,$8}' file
awk '($3 < 1980) {print $3, "    ",$5,$6,$7,$8}' file
awk '$3 < 1980 {print $3, "    ",$5,$6,$7,$8}' file
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

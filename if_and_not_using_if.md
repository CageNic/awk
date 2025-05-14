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

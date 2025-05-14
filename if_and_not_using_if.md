## the same  
```
awk '$2 ~ /pattern/ { print }' file
awk '{ if($2 ~ /pattern/) print}' file
```

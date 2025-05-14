```
cat coins.txt
   gold     1    1986  USA                 American Eagle
   gold     1    1908  Austria-Hungary     Franz Josef 100 Korona
   silver  10    1981  USA                 ingot
   gold     1    1984  Switzerland         ingot
   gold     1    1979  RSA                 Krugerrand
   gold     0.5  1981  RSA                 Krugerrand
   gold     0.1  1986  PRC                 Panda
   silver   1    1986  USA                 Liberty dollar
   gold     0.25 1986  USA                 Liberty 5-dollar piece
   silver   0.5  1986  USA                 Liberty 50-cent piece
   silver   1    1987  USA                 Constitution dollar
   gold     0.25 1987  USA                 Constitution 5-dollar piece
   gold     1    1988  Canada              Maple Leaf
```
```
cat coins.awk
   # This is an awk program that summarizes a coin collection.
   
   /gold/    { num_gold++; wt_gold += $2 }      # Get weight of gold.
   /silver/  { num_silver++; wt_silver += $2 }  # Get weight of silver.

   END { val_gold = 485 * wt_gold;              # Compute value of gold.
         val_silver = 16 * wt_silver;           # Compute value of silver.
         total = val_gold + val_silver;
         print "Summary data for coin collection:";  # Print results.
         printf ("\n");
         printf ("   Gold pieces: %2d\n", num_gold);
         printf ("   Weight of gold pieces: %5.2f\n", wt_gold);
         printf ("   Value of gold pieces: %7.2f\n",val_gold);
         printf ("\n");
         printf ("   Silver pieces: %2d\n", num_silver);
         printf ("   Weight of silver pieces: %5.2f\n", wt_silver);
         printf ("   Value of silver pieces: %7.2f\n",val_silver);
         printf ("\n");
         printf ("   Total number of pieces: %2d\n", NR);
         printf ("   Value of collection: %7.2f\n", total); }

############
# produces #
############
Gold pieces:                    9
Weight of gold pieces:          6.10
Value of gold pieces:        2958.50

Silver pieces:                  4
Weight of silver pieces:       12.50
Value of silver pieces:       200.00

Total number of pieces:        13
Value of collection:         3158.50


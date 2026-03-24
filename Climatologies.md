## Understanding the Climatalogies
The climatologies of the folders climatologies & climatologiesso were computed through the same algorithm, parallelization (dask) was utilized to accelerate the computation time.

The code for the climatologies works as following:
    1. There are two main loops, one that goes through each model (main loop) and the variable loop (nested into each model).
    2. There are some parameters that you can change, for example time period and depth
    3. The complex variables  (zooc/phyc, average wind, ...) are handled at the beggining through if and elif loops. This is not the most efficient 
    way to do it as it calculates the whole grid and then does everything to it (its a complex ratio instead of a simple one). It would be simpler to export the variables  needed as climatologies and then calculate the simple average (climatologies averaged out, not by everypoint)
    4. The else after the elif 'si*' is what actually preps the data for the climatologies:
        a. checks that a path exists and if it does it opens the dataset
        b. with the dataset open, it then standardizes the coordinates (renames coordinates and selects the depth) using a function defined in a cell above.
        c. units are modified to the most common unit used per variable, which is why there are several ifs and elif == var
    5. Outside of that else is that where climatologies are computed (so that the elifs from the complex variables are done) 
        a. Data is regridded to ensure consistency
        b. Data is then sliced to the region we want to use (This happens a bit differently in the southern ocean fronts as a this would be part of the regions loop)
        c. Data is detrended
            i. The average value of the variable is computed through the 20 years for each spatial point
            ii. The anomaly is computed through time at every point --> Because we are looking at seasonalities, we don't want to see climate change variability itself
            iii. The anomaly is detrended (so that there is no increase or decrease through time (think of slope becoming horizontal again))
            iv.  The anomaly is added again to the average of that variable, giving its seasonality back
            v. The resulting array is then averaged through all its spatial points (loses one dimension) and then its saved.
            
The code for southern fronts works similarly, just with the addition of one extra loop to divide it into fronts instead.

## Accessing the Climatologies 
The climatologies for PAPA are saved at /glade/u/home/diegovar/summer2025/climatologies
and for the southern ocean fronts at /glade/u/home/diegovar/summer2025/climatologiesso

The regions are np for North Pacific (PAPA) and  [pfz, stz, saz, az, siz] for the ocean fronts.

Filenames are constructed in the following manner: 
model + _ + variable + _ + depth (only if it is not at surface) + m + _ + period + region + .txt
for example:
IPSL-CM6A_LR_intpp_19912010_pfz.txt --> Integrated Primary Productivity from the IPSL-CM6A_LR model for the time period 1991-2010 in the pfz region of the Southern Ocean. Its directory would be /glade/u/home/diegovar/summer2025/climatologiesso/IPSL-CM6A-LR_intpp_19912010_pfz.txt

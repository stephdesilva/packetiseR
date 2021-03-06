
<!-- README.md is generated from README.Rmd. Please edit that file -->
packetiseR
==========

packetiseR exists because trying to do data science from a rural area using Australian internet infrastructure is.. frustrating.

The basic idea behind packetiseR is that in order to transmit files from one computer to another, the file is broken up into *packets* and sent in pieces. These pieces are assembled on the other side. If pieces are missing, the receiving computer re-requests packets and the process goes on until the whole file is able to be reassembled .. or there's a time out.

In rural Australia, time outs are very common with largeish files. There's alot of packets, alot get lost and the time out is reached without all the packets being reassembled.

packetiseR is a simple way of getting around this problem - it breaks up the file into many smaller ones so you can send them one-at-a-time. This reduces the number of packets that need to be found and each smaller file has the same time out length as a larger file. Sending files in this way has been - in my experience - more likely to be successful.

Nobody wants to cut their .csv into 100 pieces by hand and the reassemble at the other side: hence packetiseR.

Installation
------------

You can install the packetiseR from [Github](https://www.github.com) with:

``` r
# install.packages("devtools")
devtools::install_github("stephdesilva/packetiseR")
```

Example
-------

Say you've got a big file, let's make a pretend-big file. We'll then go on and divide it up into smaller packets to be sent one-at-a-time. We'll divide the larger file into ten packets, and save is as an .rds because that's more space efficient in my experience. You can also save and send as .csv. We'll also save it in the relative directory `./` using the `makePackets()` function.

``` r
data <- matrix(runif(1,0,1), nrow = 1000, ncol = 3)
packetiseR::makePackets(data, 10, '.rds', './')
```

Once you've sent the smaller files, you want to be able to recombine them on the other side. Typical use case is when you want to get a largish file from a local drive onto the cloud via a really dodgy internet connection.\*

``` r
data_combined <- packetiseR::recombinePackets('./', '.rds', c("a", "b", "c"))
#> Loading required package: dplyr
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
#> Loading required package: purrr
#> Loading required package: data.table
#> 
#> Attaching package: 'data.table'
#> The following object is masked from 'package:purrr':
#> 
#>     transpose
#> The following objects are masked from 'package:dplyr':
#> 
#>     between, first, last
```

Here, we retrieved *all the .rds files from the directory `./`* and recombined them into a single `.rds` file. We also provided column names in the last argument.

-   'Dodgy' is an Australianism for dubious, or likely to be defective.

Tips for using `packetiseR()`
-----------------------------

    * Choose your directory carefully. Using the relative directory `./` like I have here may cause large amounts of confusion if your original data is located there too. I suggest making a specific packeting directory and using the path argument to put the data there.
    * At the moment, the package will make packets for and recombine `.rds` and `.csv` files. You can convert `.xlsx` or `.xls` files fairly easily. If it fits into a `.csv` it's probably fine, but if you're using nested data frames saved as `.rds` you may experience formatting problems at the other end.

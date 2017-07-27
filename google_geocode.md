google\_geocoding
================

R Markdown
----------

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

``` r
library(ggmap)
```

    ## Loading required package: ggplot2

``` r
data <- read.csv('C:/work/geocode/geocode.csv')
data[1,]
```

    ##   num                    address
    ## 1   1 경상북도 의성군 철파리 144

``` r
data$address <- as.character(data$address)
addresses <- enc2utf8(data$address)
#addresses

getGeoDetails <- function(addresses){
  #use the gecode function to query google servers
  geo_reply = geocode(addresses, output='all', messaging=TRUE, override_limit=TRUE)
  #now extract the bits that we need from the returned list
  answer <- data.frame(lat=NA, long=NA, accuracy=NA, formatted_address=NA, address_type=NA, status=NA)
  answer$status <- geo_reply$status
  
  #if we are over the query limit - want to pause for an hour
  while(geo_reply$status == "OVER_QUERY_LIMIT"){
    print("OVER QUERY LIMIT - Pausing for 1 hour at:") 
    time <- Sys.time()
    print(as.character(time))
    Sys.sleep(60*60)
    geo_reply = geocode(addresses, output='all', messaging=TRUE, override_limit=TRUE)
    answer$status <- geo_reply$status
  }
  
  #return Na's if we didn't get a match:
  if (geo_reply$status != "OK"){
    return(answer)
  }
  #else, extract what we need from the Google server reply into a dataframe:
  answer$lat <- geo_reply$results[[1]]$geometry$location$lat
  answer$long <- geo_reply$results[[1]]$geometry$location$lng
  if (length(geo_reply$results[[1]]$types) > 0){
    answer$accuracy <- geo_reply$results[[1]]$types[[1]]
  }
  answer$address_type <- paste(geo_reply$results[[1]]$types, collapse=',')
  answer$formatted_address <- geo_reply$results[[1]]$formatted_address
  
  return(answer)
}

#initialise a dataframe to hold the results
geocoded <- data.frame()
# find out where to start in the address list (if the script was interrupted before):
startindex <- 1
#if a temp file exists - load it up and count the rows!
#tempfilename <- 'temp_geocoded.rds'
#if (file.exists(tempfilename)){
#  print("Found temp file - resuming from index:")
#  geocoded <- readRDS(tempfilename)
#  startindex <- nrow(geocoded)
#  print(startindex)
#}

# Start the geocoding process - address by address. geocode() function takes care of query speed limit.
for (ii in seq(startindex, length(addresses))){
  print(paste("Working on index", ii, "of", length(addresses)))
  #query the google geocoder - this will pause here if we are over the limit.
  result = getGeoDetails(addresses[ii]) 
  print(result$status)
  result$index <- ii
  #append the answer to the results file.
  geocoded <- rbind(geocoded, result)
  #save temporary results as we are going along
  #saveRDS(geocoded, tempfilename)
}
```

    ## [1] "Working on index 1 of 10"

    ## contacting http://maps.googleapis.com/maps/api/geocode/json?address=%EA
    ## %B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%9D%98%EC%84%B1%EA%B5%B0%20%EC
    ## %B2%A0%ED%8C%8C%EB%A6%AC%20144&sensor=false...

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=%EA%B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%9D%98%EC%84%B1%EA%B5%B0%20%EC%B2%A0%ED%8C%8C%EB%A6%AC%20144&sensor=false

    ##  done.

    ## [1] "OK"
    ## [1] "Working on index 2 of 10"

    ## contacting http://maps.googleapis.com/maps/api/geocode/json?address=%EA
    ## %B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%9D%98%EC%84%B1%EA%B5%B0%20%EC
    ## %B2%A0%ED%8C%8C%EB%A6%AC%20157&sensor=false...

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=%EA%B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%9D%98%EC%84%B1%EA%B5%B0%20%EC%B2%A0%ED%8C%8C%EB%A6%AC%20157&sensor=false

    ##  done.

    ## [1] "OK"
    ## [1] "Working on index 3 of 10"

    ## contacting http://maps.googleapis.com/maps/api/geocode/json?address=%EA
    ## %B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%9D%98%EC%84%B1%EA%B5%B0%20%EC
    ## %B2%A0%ED%8C%8C%EB%A6%AC%20158&sensor=false...

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=%EA%B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%9D%98%EC%84%B1%EA%B5%B0%20%EC%B2%A0%ED%8C%8C%EB%A6%AC%20158&sensor=false

    ##  done.

    ## [1] "OK"
    ## [1] "Working on index 4 of 10"

    ## contacting http://maps.googleapis.com/maps/api/geocode/json?address=%EA
    ## %B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%9D%98%EC%84%B1%EA%B5%B0%20%EC
    ## %B2%A0%ED%8C%8C%EB%A6%AC%20164&sensor=false...

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=%EA%B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%9D%98%EC%84%B1%EA%B5%B0%20%EC%B2%A0%ED%8C%8C%EB%A6%AC%20164&sensor=false

    ##  done.

    ## [1] "OK"
    ## [1] "Working on index 5 of 10"

    ## contacting http://maps.googleapis.com/maps/api/geocode/json?address=%EA
    ## %B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%9D%98%EC%84%B1%EA%B5%B0%20%EC
    ## %B2%A0%ED%8C%8C%EB%A6%AC%20388&sensor=false...

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=%EA%B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%9D%98%EC%84%B1%EA%B5%B0%20%EC%B2%A0%ED%8C%8C%EB%A6%AC%20388&sensor=false

    ##  done.

    ## [1] "OK"
    ## [1] "Working on index 6 of 10"

    ## contacting http://maps.googleapis.com/maps/api/geocode/json?address=%EA
    ## %B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%B2%AD%EC%86%A1%EA%B5%B0%20%EB
    ## %91%90%ED%98%84%EB%A6%AC%201103&sensor=false...

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=%EA%B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%B2%AD%EC%86%A1%EA%B5%B0%20%EB%91%90%ED%98%84%EB%A6%AC%201103&sensor=false

    ##  done.

    ## [1] "OK"
    ## [1] "Working on index 7 of 10"

    ## contacting http://maps.googleapis.com/maps/api/geocode/json?address=%EA
    ## %B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%B2%AD%EC%86%A1%EA%B5%B0%20%EB
    ## %91%90%ED%98%84%EB%A6%AC%201104-1&sensor=false...

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=%EA%B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%B2%AD%EC%86%A1%EA%B5%B0%20%EB%91%90%ED%98%84%EB%A6%AC%201104-1&sensor=false

    ##  done.

    ## [1] "OK"
    ## [1] "Working on index 8 of 10"

    ## contacting http://maps.googleapis.com/maps/api/geocode/json?address=%EA
    ## %B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%B2%AD%EC%86%A1%EA%B5%B0%20%EB
    ## %91%90%ED%98%84%EB%A6%AC%201107-2&sensor=false...

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=%EA%B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%B2%AD%EC%86%A1%EA%B5%B0%20%EB%91%90%ED%98%84%EB%A6%AC%201107-2&sensor=false

    ##  done.

    ## [1] "OK"
    ## [1] "Working on index 9 of 10"

    ## contacting http://maps.googleapis.com/maps/api/geocode/json?address=%EA
    ## %B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%B9%A0%EA%B3%A1%EA%B5%B0%20%EC%88%AD
    ## %EC%98%A4%EB%A6%AC%2020-1%20%EB%8C%80%ED%98%B8%EC%A3%BC%ED%83%9D%20202%ED
    ## %98%B8&sensor=false...

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=%EA%B2%BD%EC%83%81%EB%B6%81%EB%8F%84%20%EC%B9%A0%EA%B3%A1%EA%B5%B0%20%EC%88%AD%EC%98%A4%EB%A6%AC%2020-1%20%EB%8C%80%ED%98%B8%EC%A3%BC%ED%83%9D%20202%ED%98%B8&sensor=false

    ##  done.

    ## [1] "ZERO_RESULTS"
    ## [1] "Working on index 10 of 10"

    ## contacting http://maps.googleapis.com/maps/api/geocode/json?address=%EB%8C
    ## %80%EA%B5%AC%EA%B4%91%EC%97%AD%EC%8B%9C%20%EB%8B%AC%EC%84%B1%EA%B5%B0%20%EC
    ## %84%A4%ED%99%94%EB%A6%AC%20739-12%EB%B2%88%EC%A7%80%2025&sensor=false...

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=%EB%8C%80%EA%B5%AC%EA%B4%91%EC%97%AD%EC%8B%9C%20%EB%8B%AC%EC%84%B1%EA%B5%B0%20%EC%84%A4%ED%99%94%EB%A6%AC%20739-12%EB%B2%88%EC%A7%80%2025&sensor=false

    ##  done.

    ## [1] "OK"

``` r
#now we add the latitude and longitude to the main data
data$lat <- geocoded$lat
data$long <- geocoded$long
data$accuracy <- geocoded$accuracy

#finally write it all to the output files
saveRDS(data,"C:/work/geocode/geocode_result.rds")
write.table(data, file="C:/work/geocode/geocode_result.csv", sep=",", row.names=FALSE)
```

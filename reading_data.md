Reading data from the web
================
Binyam Yilma
4/4/2021

## Web scrapping

I want the first table from [this
page](http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm)

1st - read in the html

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"


drug_use_html = read_html(url)
```

extract the table(s); focus on first one

``` r
table_marj = 
drug_use_html %>% 
  html_nodes(css = "table") %>% 
  first() %>% 
  html_table() %>% 
  slice(-1) %>% 
  as_tibble()
  
#numbers are stored as character because some have the letter "a"
#variable names could use cleaning up
```

## star wards movie info

I want the data from [here](https://www.imdb.com/list/ls070150896/)

``` r
url = "https://www.imdb.com/list/ls070150896/"

#swm - star wars movies
swm_html = read_html(url)
```

\#Grab elements I want.

``` r
titles_vec = 
  swm_html %>% 
  html_nodes(css = ".lister-item-header a") %>% 
  html_text()


gross_rev_vec = 
  swm_html %>% 
  html_nodes(css = ".ghost~ .text-muted+ span") %>% 
  html_text() %>% 
  as_tibble() %>% 
  filter(value != "|") %>% 
  as_vector()



run_time_vec = 
  swm_html %>% 
  html_nodes(css = ".runtime") %>% 
  html_text()


#join equal size vectors
swm_df =
  tibble(
    title = titles_vec,
    gross_rev = gross_rev_vec,
    runtime = run_time_vec
  )
```

## Get some “water” data

This is coming from an API

``` r
nyc_water_csv = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") %>% 
  content("parsed")



nyc_water_json = GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json") %>% 
  content("text") %>% 
    jsonlite::fromJSON() %>% 
    as_tibble()
```

## BRFSS

Same process, different data

``` r
brfss_2019 = 
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv",
      query = list("$limit" = 5000)) %>% 
  content("parsed")
```

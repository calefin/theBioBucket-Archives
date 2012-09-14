# Online Questionnaire with Google Docs and Report with knitr markdown
This is how you can do a report for a questionnaire using **Google Docs**, **knitr** and **markdown**.

The input file was named as `Questionnaire.Rmd`
([source](https://github.com/gimoya/theBioBucket-Archives/blob/master/R/knitr/Questionnaire.Rmd)),
and **knitr** will automatically determine the output filename to be
`questionnaire.md` (`*.Rmd --> *.md`) when running:


```r
require(knitr)
setwd("C:\\Users\\Kay\\theBioBucket-Archives\\R\\knitr")
knit("C:\\Users\\Kay\\theBioBucket-Archives\\R\\knitr\\Questionnaire.Rmd")
```


The .md-file that's generated is what you see here..

## The code to retrieve data from Google Docs and the R code to do a barplot with the responses to the questionnaire
I'll use a function ([source](https://github.com/gimoya/theBioBucket-Archives/blob/master/R/Functions/google_ss.R)) to read data from Google Docs

```r
opts_knit$set(upload.fun = imgur_upload)  # upload all images to imgur.com
opts_chunk$set(fig.width = 5, fig.height = 5, cache = TRUE)
```



```r
google_ss <- function(gid = NA, key = NA) {
    if (is.na(gid)) {
        stop("\nWorksheetnumber (gid) is missing\n")
    }
    if (is.na(key)) {
        stop("\nDocumentkey (key) is missing\n")
    }
    require(RCurl)
    url <- getURL(paste("https://docs.google.com/spreadsheet/pub?key=", key, 
        "&single=true&gid=", gid, "&output=csv", sep = ""), cainfo = system.file("CurlSSL", 
        "cacert.pem", package = "RCurl"))
    read.csv(textConnection(url), header = T, sep = ",")
}

## this is the key for the spreadsheet with the questoinaire responses key
## = 0AmwAunwURQNsdFplUTBZUTRLREtLUDhabGxBMHBRWmc taken from the documents
## url and used in the below function

data <- google_ss(gid = 0, key = "0AmwAunwURQNsdFplUTBZUTRLREtLUDhabGxBMHBRWmc")
data[, 1:2]
```

```
##              Timestamp
## 1  13/09/2012 10:33:31
## 2  13/09/2012 10:34:23
## 3  13/09/2012 10:34:29
## 4  13/09/2012 10:34:33
## 5  13/09/2012 10:34:37
## 6  14/09/2012 10:49:08
## 7  14/09/2012 10:49:18
## 8  14/09/2012 10:49:25
## 9  14/09/2012 10:49:30
## 10 14/09/2012 10:49:35
## 11 14/09/2012 10:49:40
## 12 14/09/2012 12:22:10
##    Do.you.think.there.is.enough.emphasis.on.nature.protection.in.national.policies.
## 1                                                                               Yes
## 2                                                                                No
## 3                                                                               Yes
## 4                                                                               Yes
## 5                                                                       I can't say
## 6                                                                                No
## 7                                                                                No
## 8                                                                                No
## 9                                                                                No
## 10                                                                      I can't say
## 11                                                                               No
## 12                                                                              Yes
```


Then produce a plot with the responses to the questionnaire which is uploaded to imgur.com:


```r
## get the qestionnaires' question & make plot
q <- paste(gsub("\\.", " ", colnames(data)[2]), "??", sep = "")
require(lattice)
barplot(table(data[, 2]), main = paste(strwrap(q, width = 40), collapse = "\n"), 
    sub = paste("N = ", nrow(data)))
```

![plot of chunk barplot](http://i.imgur.com/MRUWh.png) 

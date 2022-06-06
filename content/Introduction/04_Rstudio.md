---
title: "4. Rstudio"
---

Rstudio offers a graphical interface 
to facilitate the interaction between a user and an underlying 
programming language (this is sometimes called IDE, or 
integrated development environment). It can be very useful when a user is 
not necessarily proficent with command line-based computing. However, such graphical interfaces are not 
always able to connect to services such as AWS.  

Since most of the preliminary analysis we do is on AWS, 
we'd like to be able to use Rtudio directly from there. Here is how we can do!

## RStudio 

Simply go to [the following address](http://54.187.37.64:8787): 

```sh
"http://54.187.37.64:8787"
```

Don't forget, you can run a `bash` terminal from within RStudio! This may come handy if you want to process some data with `cellranger`, for example. To do this, simply click on the `terminal` button next on the bottom left panel. You should now be in your own `${home}` directory (`~`). 

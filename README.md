Process carbonate budgets data with reefbudgetR
================

``` r
# Set working directory containing data
setwd("T:/Oceanography/Carbonate Budgets/NCEI/ESD_CarbBudget_OAHU_2021/Raw_data/")

# Load benthic, urchin, and fish data
benthic_data <- read.csv("ESD_CarbBudget_Benthic_OAHU_2021.csv", na = "", check.names = FALSE)

urchin_data <- read.csv("ESD_CarbBudget_Urchins_OAHU_2021.csv", na = "", check.names = FALSE)

fish_data <- read.csv("ESD_CarbBudget_FishBelt_OAHU_2021.csv", na = "", check.names = FALSE)
```

``` r
#### Process benthic data by method type

# Process IPRB benthic data
prod_iprb <- process_prod(
  data = benthic_data[benthic_data$CB_METHOD == "IPRB", ],
  method_name = "IPRB"
)

# Process chords benthic data
prod_chords <- process_prod(
  data = benthic_data[benthic_data$CB_METHOD == "Chords", ],
  method_name = "Chords"
)

# Process SfM benthic data
prod_sfm <- process_prod(
  data = benthic_data[benthic_data$CB_METHOD == "SfM", ],
  method_name = "SfM"
)

# Combine site-level production data back together
prod_site <- bind_rows(
  prod_iprb$summary_site,
  prod_chords$summary_site,
  prod_sfm$summary_site
)
```

``` r
#### Process urchin data by method type

# Process IPRB urchin data
urch_iprb <- process_urchins(
  data = urchin_data[urchin_data$CB_METHOD == "IPRB", ],
  method_name = "IPRB"
)

# Process chords urchin data
urch_chords <- process_urchins(
  data = urchin_data[urchin_data$CB_METHOD == "Chords", ],
  method_name = "Chords"
)


# Combine site-level urchin erosion data back together
urch_site <- bind_rows(urch_iprb$site_erosion,
                                 urch_chords$site_erosion)
```

``` r
fish_belt <- process_fish(
  data = fish_data, 
  rates_dbase = "Kindinger", 
  full_summary = TRUE)

fish_site <- fish_belt$fish_erosion_site
```

``` r
# Process net production data
net_site = process_net(
  prod = prod_site,
  urch = urch_site,
  fish = fish_site
)
```

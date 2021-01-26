---
title: "dplyr Superhero"
date: "2021-01-25"
output:
  html_document: 
    theme: spacelab
    toc: yes
    toc_float: yes
    keep_md: yes
---

## Breakout Rooms  
Please take 5-8 minutes to check over your answers to the HW in your group. If you are stuck, please remember that you can check the key in [Joel's repository](https://github.com/jmledford3115/BIS15LW2021_jledford).  

## Learning Goals  
*At the end of this exercise, you will be able to:*    
1. Develop your dplyr superpowers so you can easily and confidently manipulate dataframes.  
2. Learn helpful new functions that are part of the `janitor` package.  

## Instructions
For the second part of lab 5 today, we are going to spend time practicing the dplyr functions we have learned and add a few new ones. We will spend most of the time in our breakout rooms. Your lab 5 homework will be to knit and push this file to your repository.  

## Load the tidyverse

```r
library("tidyverse")
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --
```

```
## √ ggplot2 3.3.3     √ purrr   0.3.4
## √ tibble  3.0.5     √ dplyr   1.0.3
## √ tidyr   1.1.2     √ stringr 1.4.0
## √ readr   1.4.0     √ forcats 0.5.0
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

## Load the superhero data
These are data taken from comic books and assembled by fans. The include a good mix of categorical and continuous data.  Data taken from: https://www.kaggle.com/claudiodavi/superhero-set  

Check out the way I am loading these data. If I know there are NAs, I can take care of them at the beginning. But, we should do this very cautiously. At times it is better to keep the original columns and data intact.  

```r
superhero_info <- readr::read_csv("data/heroes_information.csv", na = c("", "-99", "-"))
```

```
## 
## -- Column specification --------------------------------------------------------
## cols(
##   name = col_character(),
##   Gender = col_character(),
##   `Eye color` = col_character(),
##   Race = col_character(),
##   `Hair color` = col_character(),
##   Height = col_double(),
##   Publisher = col_character(),
##   `Skin color` = col_character(),
##   Alignment = col_character(),
##   Weight = col_double()
## )
```

```r
superhero_powers <- readr::read_csv("data/super_hero_powers.csv", na = c("", "-99", "-"))
```

```
## 
## -- Column specification --------------------------------------------------------
## cols(
##   .default = col_logical(),
##   hero_names = col_character()
## )
## i Use `spec()` for the full column specifications.
```

## Data tidy
1. Some of the names used in the `superhero_info` data are problematic so you should rename them here.  

```r
superhero_info <- rename(superhero_info, gender = "Gender" , eye_color = "Eye color" , race = "Race" , hair_color = "Hair color" , height = "Height" , publisher = "Publisher" , skin_color = "Skin color" , alignment = "Alignment" , weight = "Weight")
```



```r
names(superhero_info)
```

```
##  [1] "name"       "gender"     "eye_color"  "race"       "hair_color"
##  [6] "height"     "publisher"  "skin_color" "alignment"  "weight"
```

Yikes! `superhero_powers` has a lot of variables that are poorly named. We need some R superpowers...

```r
head(superhero_powers)
```

```
## # A tibble: 6 x 168
##   hero_names Agility `Accelerated He~ `Lantern Power ~ `Dimensional Aw~
##   <chr>      <lgl>   <lgl>            <lgl>            <lgl>           
## 1 3-D Man    TRUE    FALSE            FALSE            FALSE           
## 2 A-Bomb     FALSE   TRUE             FALSE            FALSE           
## 3 Abe Sapien TRUE    TRUE             FALSE            FALSE           
## 4 Abin Sur   FALSE   FALSE            TRUE             FALSE           
## 5 Abominati~ FALSE   TRUE             FALSE            FALSE           
## 6 Abraxas    FALSE   FALSE            FALSE            TRUE            
## # ... with 163 more variables: `Cold Resistance` <lgl>, Durability <lgl>,
## #   Stealth <lgl>, `Energy Absorption` <lgl>, Flight <lgl>, `Danger
## #   Sense` <lgl>, `Underwater breathing` <lgl>, Marksmanship <lgl>, `Weapons
## #   Master` <lgl>, `Power Augmentation` <lgl>, `Animal Attributes` <lgl>,
## #   Longevity <lgl>, Intelligence <lgl>, `Super Strength` <lgl>,
## #   Cryokinesis <lgl>, Telepathy <lgl>, `Energy Armor` <lgl>, `Energy
## #   Blasts` <lgl>, Duplication <lgl>, `Size Changing` <lgl>, `Density
## #   Control` <lgl>, Stamina <lgl>, `Astral Travel` <lgl>, `Audio
## #   Control` <lgl>, Dexterity <lgl>, Omnitrix <lgl>, `Super Speed` <lgl>,
## #   Possession <lgl>, `Animal Oriented Powers` <lgl>, `Weapon-based
## #   Powers` <lgl>, Electrokinesis <lgl>, `Darkforce Manipulation` <lgl>, `Death
## #   Touch` <lgl>, Teleportation <lgl>, `Enhanced Senses` <lgl>,
## #   Telekinesis <lgl>, `Energy Beams` <lgl>, Magic <lgl>, Hyperkinesis <lgl>,
## #   Jump <lgl>, Clairvoyance <lgl>, `Dimensional Travel` <lgl>, `Power
## #   Sense` <lgl>, Shapeshifting <lgl>, `Peak Human Condition` <lgl>,
## #   Immortality <lgl>, Camouflage <lgl>, `Element Control` <lgl>,
## #   Phasing <lgl>, `Astral Projection` <lgl>, `Electrical Transport` <lgl>,
## #   `Fire Control` <lgl>, Projection <lgl>, Summoning <lgl>, `Enhanced
## #   Memory` <lgl>, Reflexes <lgl>, Invulnerability <lgl>, `Energy
## #   Constructs` <lgl>, `Force Fields` <lgl>, `Self-Sustenance` <lgl>,
## #   `Anti-Gravity` <lgl>, Empathy <lgl>, `Power Nullifier` <lgl>, `Radiation
## #   Control` <lgl>, `Psionic Powers` <lgl>, Elasticity <lgl>, `Substance
## #   Secretion` <lgl>, `Elemental Transmogrification` <lgl>,
## #   `Technopath/Cyberpath` <lgl>, `Photographic Reflexes` <lgl>, `Seismic
## #   Power` <lgl>, Animation <lgl>, Precognition <lgl>, `Mind Control` <lgl>,
## #   `Fire Resistance` <lgl>, `Power Absorption` <lgl>, `Enhanced
## #   Hearing` <lgl>, `Nova Force` <lgl>, Insanity <lgl>, Hypnokinesis <lgl>,
## #   `Animal Control` <lgl>, `Natural Armor` <lgl>, Intangibility <lgl>,
## #   `Enhanced Sight` <lgl>, `Molecular Manipulation` <lgl>, `Heat
## #   Generation` <lgl>, Adaptation <lgl>, Gliding <lgl>, `Power Suit` <lgl>,
## #   `Mind Blast` <lgl>, `Probability Manipulation` <lgl>, `Gravity
## #   Control` <lgl>, Regeneration <lgl>, `Light Control` <lgl>,
## #   Echolocation <lgl>, Levitation <lgl>, `Toxin and Disease Control` <lgl>,
## #   Banish <lgl>, `Energy Manipulation` <lgl>, `Heat Resistance` <lgl>, ...
```

## `janitor`
The [janitor](https://garthtarr.github.io/meatR/janitor.html) package is your friend. Make sure to install it and then load the library.  

```r
library("janitor")
```

```
## 
## Attaching package: 'janitor'
```

```
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

The `clean_names` function takes care of everything in one line! Now that's a superpower!

```r
superhero_powers <- janitor::clean_names(superhero_powers)
```


```r
head(superhero_powers)
```

```
## # A tibble: 6 x 168
##   hero_names agility accelerated_hea~ lantern_power_r~ dimensional_awa~
##   <chr>      <lgl>   <lgl>            <lgl>            <lgl>           
## 1 3-D Man    TRUE    FALSE            FALSE            FALSE           
## 2 A-Bomb     FALSE   TRUE             FALSE            FALSE           
## 3 Abe Sapien TRUE    TRUE             FALSE            FALSE           
## 4 Abin Sur   FALSE   FALSE            TRUE             FALSE           
## 5 Abominati~ FALSE   TRUE             FALSE            FALSE           
## 6 Abraxas    FALSE   FALSE            FALSE            TRUE            
## # ... with 163 more variables: cold_resistance <lgl>, durability <lgl>,
## #   stealth <lgl>, energy_absorption <lgl>, flight <lgl>, danger_sense <lgl>,
## #   underwater_breathing <lgl>, marksmanship <lgl>, weapons_master <lgl>,
## #   power_augmentation <lgl>, animal_attributes <lgl>, longevity <lgl>,
## #   intelligence <lgl>, super_strength <lgl>, cryokinesis <lgl>,
## #   telepathy <lgl>, energy_armor <lgl>, energy_blasts <lgl>,
## #   duplication <lgl>, size_changing <lgl>, density_control <lgl>,
## #   stamina <lgl>, astral_travel <lgl>, audio_control <lgl>, dexterity <lgl>,
## #   omnitrix <lgl>, super_speed <lgl>, possession <lgl>,
## #   animal_oriented_powers <lgl>, weapon_based_powers <lgl>,
## #   electrokinesis <lgl>, darkforce_manipulation <lgl>, death_touch <lgl>,
## #   teleportation <lgl>, enhanced_senses <lgl>, telekinesis <lgl>,
## #   energy_beams <lgl>, magic <lgl>, hyperkinesis <lgl>, jump <lgl>,
## #   clairvoyance <lgl>, dimensional_travel <lgl>, power_sense <lgl>,
## #   shapeshifting <lgl>, peak_human_condition <lgl>, immortality <lgl>,
## #   camouflage <lgl>, element_control <lgl>, phasing <lgl>,
## #   astral_projection <lgl>, electrical_transport <lgl>, fire_control <lgl>,
## #   projection <lgl>, summoning <lgl>, enhanced_memory <lgl>, reflexes <lgl>,
## #   invulnerability <lgl>, energy_constructs <lgl>, force_fields <lgl>,
## #   self_sustenance <lgl>, anti_gravity <lgl>, empathy <lgl>,
## #   power_nullifier <lgl>, radiation_control <lgl>, psionic_powers <lgl>,
## #   elasticity <lgl>, substance_secretion <lgl>,
## #   elemental_transmogrification <lgl>, technopath_cyberpath <lgl>,
## #   photographic_reflexes <lgl>, seismic_power <lgl>, animation <lgl>,
## #   precognition <lgl>, mind_control <lgl>, fire_resistance <lgl>,
## #   power_absorption <lgl>, enhanced_hearing <lgl>, nova_force <lgl>,
## #   insanity <lgl>, hypnokinesis <lgl>, animal_control <lgl>,
## #   natural_armor <lgl>, intangibility <lgl>, enhanced_sight <lgl>,
## #   molecular_manipulation <lgl>, heat_generation <lgl>, adaptation <lgl>,
## #   gliding <lgl>, power_suit <lgl>, mind_blast <lgl>,
## #   probability_manipulation <lgl>, gravity_control <lgl>, regeneration <lgl>,
## #   light_control <lgl>, echolocation <lgl>, levitation <lgl>,
## #   toxin_and_disease_control <lgl>, banish <lgl>, energy_manipulation <lgl>,
## #   heat_resistance <lgl>, ...
```

## `tabyl`
The `janitor` package has many awesome functions that we will explore. Here is its version of `table` which not only produces counts but also percentages. Very handy! Let's use it to explore the proportion of good guys and bad guys in the `superhero_info` data. 

```r
names(superhero_info)
```

```
##  [1] "name"       "gender"     "eye_color"  "race"       "hair_color"
##  [6] "height"     "publisher"  "skin_color" "alignment"  "weight"
```


```r
tabyl(superhero_info, alignment)
```

```
##  alignment   n     percent valid_percent
##        bad 207 0.282016349    0.28473177
##       good 496 0.675749319    0.68225585
##    neutral  24 0.032697548    0.03301238
##       <NA>   7 0.009536785            NA
```

2. Notice that we have some neutral superheros! Who are they?

```r
superhero_info %>% 
  filter(alignment == "neutral") 
```

```
## # A tibble: 24 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Biza~ Male   black     Biza~ Black         191 DC Comics white      neutral  
##  2 Blac~ Male   <NA>      God ~ <NA>           NA DC Comics <NA>       neutral  
##  3 Capt~ Male   brown     Human Brown          NA DC Comics <NA>       neutral  
##  4 Copy~ Female red       Muta~ White         183 Marvel C~ blue       neutral  
##  5 Dead~ Male   brown     Muta~ No Hair       188 Marvel C~ <NA>       neutral  
##  6 Deat~ Male   blue      Human White         193 DC Comics <NA>       neutral  
##  7 Etri~ Male   red       Demon No Hair       193 DC Comics yellow     neutral  
##  8 Gala~ Male   black     Cosm~ Black         876 Marvel C~ <NA>       neutral  
##  9 Glad~ Male   blue      Stro~ Blue          198 Marvel C~ purple     neutral  
## 10 Indi~ Female <NA>      Alien Purple         NA DC Comics <NA>       neutral  
## # ... with 14 more rows, and 1 more variable: weight <dbl>
```

## `superhero_info`
3. Let's say we are only interested in the variables name, alignment, and "race". How would you isolate these variables from `superhero_info`?

```r
names(superhero_info)
```

```
##  [1] "name"       "gender"     "eye_color"  "race"       "hair_color"
##  [6] "height"     "publisher"  "skin_color" "alignment"  "weight"
```

```r
superhero_info %>% 
  select(name, alignment, race)
```

```
## # A tibble: 734 x 3
##    name          alignment race             
##    <chr>         <chr>     <chr>            
##  1 A-Bomb        good      Human            
##  2 Abe Sapien    good      Icthyo Sapien    
##  3 Abin Sur      good      Ungaran          
##  4 Abomination   bad       Human / Radiation
##  5 Abraxas       bad       Cosmic Entity    
##  6 Absorbing Man bad       Human            
##  7 Adam Monroe   good      <NA>             
##  8 Adam Strange  good      Human            
##  9 Agent 13      good      <NA>             
## 10 Agent Bob     good      Human            
## # ... with 724 more rows
```

## Not Human
4. List all of the superheros that are not human.

```r
superhero_info %>% 
  filter(race != "Human")
```

```
## # A tibble: 222 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abe ~ Male   blue      Icth~ No Hair       191 Dark Hor~ blue       good     
##  2 Abin~ Male   blue      Unga~ No Hair       185 DC Comics red        good     
##  3 Abom~ Male   green     Huma~ No Hair       203 Marvel C~ <NA>       bad      
##  4 Abra~ Male   blue      Cosm~ Black          NA Marvel C~ <NA>       bad      
##  5 Ajax  Male   brown     Cybo~ Black         193 Marvel C~ <NA>       bad      
##  6 Alien Male   <NA>      Xeno~ No Hair       244 Dark Hor~ black      bad      
##  7 Amazo Male   red       Andr~ <NA>          257 DC Comics <NA>       bad      
##  8 Angel Male   <NA>      Vamp~ <NA>           NA Dark Hor~ <NA>       good     
##  9 Ange~ Female yellow    Muta~ Black         165 Marvel C~ <NA>       good     
## 10 Anti~ Male   yellow    God ~ No Hair        61 DC Comics <NA>       bad      
## # ... with 212 more rows, and 1 more variable: weight <dbl>
```

## Good and Evil
5. Let's make two different data frames, one focused on the "good guys" and another focused on the "bad guys".


```r
good_guys <- filter(superhero_info, alignment == 'good')
good_guys
```

```
## # A tibble: 496 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 A-Bo~ Male   yellow    Human No Hair       203 Marvel C~ <NA>       good     
##  2 Abe ~ Male   blue      Icth~ No Hair       191 Dark Hor~ blue       good     
##  3 Abin~ Male   blue      Unga~ No Hair       185 DC Comics red        good     
##  4 Adam~ Male   blue      <NA>  Blond          NA NBC - He~ <NA>       good     
##  5 Adam~ Male   blue      Human Blond         185 DC Comics <NA>       good     
##  6 Agen~ Female blue      <NA>  Blond         173 Marvel C~ <NA>       good     
##  7 Agen~ Male   brown     Human Brown         178 Marvel C~ <NA>       good     
##  8 Agen~ Male   <NA>      <NA>  <NA>          191 Marvel C~ <NA>       good     
##  9 Alan~ Male   blue      <NA>  Blond         180 DC Comics <NA>       good     
## 10 Alex~ Male   <NA>      <NA>  <NA>           NA NBC - He~ <NA>       good     
## # ... with 486 more rows, and 1 more variable: weight <dbl>
```

```r
bad_guys <- filter(superhero_info, alignment == "bad")
bad_guys
```

```
## # A tibble: 207 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abom~ Male   green     Huma~ No Hair       203 Marvel C~ <NA>       bad      
##  2 Abra~ Male   blue      Cosm~ Black          NA Marvel C~ <NA>       bad      
##  3 Abso~ Male   blue      Human No Hair       193 Marvel C~ <NA>       bad      
##  4 Air-~ Male   blue      <NA>  White         188 Marvel C~ <NA>       bad      
##  5 Ajax  Male   brown     Cybo~ Black         193 Marvel C~ <NA>       bad      
##  6 Alex~ Male   <NA>      Human <NA>           NA Wildstorm <NA>       bad      
##  7 Alien Male   <NA>      Xeno~ No Hair       244 Dark Hor~ black      bad      
##  8 Amazo Male   red       Andr~ <NA>          257 DC Comics <NA>       bad      
##  9 Ammo  Male   brown     Human Black         188 Marvel C~ <NA>       bad      
## 10 Ange~ Female <NA>      <NA>  <NA>           NA Image Co~ <NA>       bad      
## # ... with 197 more rows, and 1 more variable: weight <dbl>
```

6. For the good guys, use the `tabyl` function to summarize their "race".

```r
good_guys %>% 
  tabyl(race)
```

```
##               race   n     percent valid_percent
##              Alien   3 0.006048387   0.010752688
##              Alpha   5 0.010080645   0.017921147
##             Amazon   2 0.004032258   0.007168459
##            Android   4 0.008064516   0.014336918
##             Animal   2 0.004032258   0.007168459
##          Asgardian   3 0.006048387   0.010752688
##          Atlantean   4 0.008064516   0.014336918
##         Bolovaxian   1 0.002016129   0.003584229
##              Clone   1 0.002016129   0.003584229
##             Cyborg   3 0.006048387   0.010752688
##           Demi-God   2 0.004032258   0.007168459
##              Demon   3 0.006048387   0.010752688
##            Eternal   1 0.002016129   0.003584229
##     Flora Colossus   1 0.002016129   0.003584229
##        Frost Giant   1 0.002016129   0.003584229
##      God / Eternal   6 0.012096774   0.021505376
##             Gungan   1 0.002016129   0.003584229
##              Human 148 0.298387097   0.530465950
##         Human-Kree   2 0.004032258   0.007168459
##      Human-Spartoi   1 0.002016129   0.003584229
##       Human-Vulcan   1 0.002016129   0.003584229
##    Human-Vuldarian   1 0.002016129   0.003584229
##    Human / Altered   2 0.004032258   0.007168459
##     Human / Cosmic   2 0.004032258   0.007168459
##  Human / Radiation   8 0.016129032   0.028673835
##      Icthyo Sapien   1 0.002016129   0.003584229
##            Inhuman   4 0.008064516   0.014336918
##    Kakarantharaian   1 0.002016129   0.003584229
##         Kryptonian   4 0.008064516   0.014336918
##            Martian   1 0.002016129   0.003584229
##          Metahuman   1 0.002016129   0.003584229
##             Mutant  46 0.092741935   0.164874552
##     Mutant / Clone   1 0.002016129   0.003584229
##             Planet   1 0.002016129   0.003584229
##             Saiyan   1 0.002016129   0.003584229
##           Symbiote   3 0.006048387   0.010752688
##           Talokite   1 0.002016129   0.003584229
##         Tamaranean   1 0.002016129   0.003584229
##            Ungaran   1 0.002016129   0.003584229
##            Vampire   2 0.004032258   0.007168459
##     Yoda's species   1 0.002016129   0.003584229
##      Zen-Whoberian   1 0.002016129   0.003584229
##               <NA> 217 0.437500000            NA
```

7. Among the good guys, Who are the Asgardians?

```r
superhero_info %>% 
  filter(alignment == "good") %>% 
  filter(race =="Asgardian")
```

```
## # A tibble: 3 x 10
##   name  gender eye_color race  hair_color height publisher skin_color alignment
##   <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Sif   Female blue      Asga~ Black         188 Marvel C~ <NA>       good     
## 2 Thor  Male   blue      Asga~ Blond         198 Marvel C~ <NA>       good     
## 3 Thor~ Female blue      Asga~ Blond         175 Marvel C~ <NA>       good     
## # ... with 1 more variable: weight <dbl>
```
<style>
div.blue { background-color:#e6f0ff; border-radius: 5px; padding: 20px;}
</style>
<div class = "blue">

8. Among the bad guys, who are the male humans over 200 inches in height?

```r
bad_guys %>% 
  filter(gender == "Male") %>% 
  filter(height > 200)
```

```
## # A tibble: 22 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abom~ Male   green     Huma~ No Hair       203 Marvel C~ <NA>       bad      
##  2 Alien Male   <NA>      Xeno~ No Hair       244 Dark Hor~ black      bad      
##  3 Amazo Male   red       Andr~ <NA>          257 DC Comics <NA>       bad      
##  4 Apoc~ Male   red       Muta~ Black         213 Marvel C~ grey       bad      
##  5 Bane  Male   <NA>      Human <NA>          203 DC Comics <NA>       bad      
##  6 Dark~ Male   red       New ~ No Hair       267 DC Comics grey       bad      
##  7 Doct~ Male   brown     Human Brown         201 Marvel C~ <NA>       bad      
##  8 Doct~ Male   brown     <NA>  Brown         201 Marvel C~ <NA>       bad      
##  9 Doom~ Male   red       Alien White         244 DC Comics <NA>       bad      
## 10 Kill~ Male   red       Meta~ No Hair       244 DC Comics green      bad      
## # ... with 12 more rows, and 1 more variable: weight <dbl>
```
</div>

9. OK, so are there more good guys or bad guys that are bald (personal interest)?

```r
good_guys %>% 
  filter(hair_color == "No Hair")
```

```
## # A tibble: 37 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 A-Bo~ Male   yellow    Human No Hair       203 Marvel C~ <NA>       good     
##  2 Abe ~ Male   blue      Icth~ No Hair       191 Dark Hor~ blue       good     
##  3 Abin~ Male   blue      Unga~ No Hair       185 DC Comics red        good     
##  4 Beta~ Male   <NA>      <NA>  No Hair       201 Marvel C~ <NA>       good     
##  5 Bish~ Male   brown     Muta~ No Hair       198 Marvel C~ <NA>       good     
##  6 Blac~ Male   brown     <NA>  No Hair       185 DC Comics <NA>       good     
##  7 Blaq~ <NA>   black     <NA>  No Hair        NA Marvel C~ <NA>       good     
##  8 Bloo~ Male   black     Muta~ No Hair        NA Marvel C~ <NA>       good     
##  9 Crim~ Male   brown     <NA>  No Hair       180 Marvel C~ <NA>       good     
## 10 Dona~ Male   green     Muta~ No Hair        NA IDW Publ~ green      good     
## # ... with 27 more rows, and 1 more variable: weight <dbl>
```

```r
bad_guys %>% 
  filter(hair_color == "No Hair")
```

```
## # A tibble: 35 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abom~ Male   green     Huma~ No Hair     203   Marvel C~ <NA>       bad      
##  2 Abso~ Male   blue      Human No Hair     193   Marvel C~ <NA>       bad      
##  3 Alien Male   <NA>      Xeno~ No Hair     244   Dark Hor~ black      bad      
##  4 Anni~ Male   green     <NA>  No Hair     180   Marvel C~ <NA>       bad      
##  5 Anti~ Male   yellow    God ~ No Hair      61   DC Comics <NA>       bad      
##  6 Blac~ Male   black     Human No Hair     188   DC Comics <NA>       bad      
##  7 Bloo~ Male   white     <NA>  No Hair      30.5 Marvel C~ <NA>       bad      
##  8 Brai~ Male   green     Andr~ No Hair     198   DC Comics green      bad      
##  9 Dark~ Male   red       New ~ No Hair     267   DC Comics grey       bad      
## 10 Dart~ Male   yellow    Cybo~ No Hair     198   George L~ <NA>       bad      
## # ... with 25 more rows, and 1 more variable: weight <dbl>
```
There are 37 good guys with no hair and only 35 bad guys with no hair - there are more bald good guys. 

10. Let's explore who the really "big" superheros are. In the `superhero_info` data, which have a height over 200 or weight over 300?

```r
superhero_info %>%  
  filter(height > 200 | weight >300 )
```

```
## # A tibble: 65 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 A-Bo~ Male   yellow    Human No Hair       203 Marvel C~ <NA>       good     
##  2 Abom~ Male   green     Huma~ No Hair       203 Marvel C~ <NA>       bad      
##  3 Alien Male   <NA>      Xeno~ No Hair       244 Dark Hor~ black      bad      
##  4 Amazo Male   red       Andr~ <NA>          257 DC Comics <NA>       bad      
##  5 Ant-~ Male   blue      Human Blond         211 Marvel C~ <NA>       good     
##  6 Anti~ Male   blue      Symb~ Blond         229 Marvel C~ <NA>       <NA>     
##  7 Apoc~ Male   red       Muta~ Black         213 Marvel C~ grey       bad      
##  8 Bane  Male   <NA>      Human <NA>          203 DC Comics <NA>       bad      
##  9 Beta~ Male   <NA>      <NA>  No Hair       201 Marvel C~ <NA>       good     
## 10 Bloo~ Female blue      Human Brown         218 Marvel C~ <NA>       bad      
## # ... with 55 more rows, and 1 more variable: weight <dbl>
```

11. Just to be clear on the `|` operator,  have a look at the superheros over 300 in height...

```r
superhero_info %>% 
  filter(height >"300")
```

```
## # A tibble: 14 x 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Anti~ Male   yellow    God ~ No Hair      61   DC Comics <NA>       bad      
##  2 Fin ~ Male   red       Kaka~ No Hair     975   Marvel C~ green      good     
##  3 Gala~ Male   black     Cosm~ Black       876   Marvel C~ <NA>       neutral  
##  4 Giga~ Female green     <NA>  Red          62.5 DC Comics <NA>       bad      
##  5 Groot Male   yellow    Flor~ <NA>        701   Marvel C~ <NA>       good     
##  6 Howa~ Male   brown     <NA>  Yellow       79   Marvel C~ <NA>       good     
##  7 Jack~ Male   blue      Human Brown        71   Dark Hor~ <NA>       good     
##  8 Kryp~ Male   blue      Kryp~ White        64   DC Comics <NA>       good     
##  9 MODOK Male   white     Cybo~ Brownn      366   Marvel C~ <NA>       bad      
## 10 Onsl~ Male   red       Muta~ No Hair     305   Marvel C~ <NA>       bad      
## 11 Sasq~ Male   red       <NA>  Orange      305   Marvel C~ <NA>       good     
## 12 Wolf~ Female green     <NA>  Auburn      366   Marvel C~ <NA>       good     
## 13 Ymir  Male   white     Fros~ No Hair     305.  Marvel C~ white      good     
## 14 Yoda  Male   brown     Yoda~ White        66   George L~ green      good     
## # ... with 1 more variable: weight <dbl>
```

12. ...and the superheros over 450 in weight. 
Bonus question! Why do we not have 16 rows in question #10?

```r
superhero_info %>% 
  filter(weight >450)
```

```
## # A tibble: 8 x 10
##   name  gender eye_color race  hair_color height publisher skin_color alignment
##   <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Bloo~ Female blue      Human Brown       218   Marvel C~ <NA>       bad      
## 2 Dark~ Male   red       New ~ No Hair     267   DC Comics grey       bad      
## 3 Giga~ Female green     <NA>  Red          62.5 DC Comics <NA>       bad      
## 4 Hulk  Male   green     Huma~ Green       244   Marvel C~ green      good     
## 5 Jugg~ Male   blue      Human Red         287   Marvel C~ <NA>       neutral  
## 6 Red ~ Male   yellow    Huma~ Black       213   Marvel C~ red        neutral  
## 7 Sasq~ Male   red       <NA>  Orange      305   Marvel C~ <NA>       good     
## 8 Wolf~ Female green     <NA>  Auburn      366   Marvel C~ <NA>       good     
## # ... with 1 more variable: weight <dbl>
```
There likely are a few heroes who are meeting the qualifications for both groups and thus are excluded when we filter things with |.
## Height to Weight Ratio
13. It's easy to be strong when you are heavy and tall, but who is heavy and short? Which superheros have the highest height to weight ratio?

```r
superhero_info %>% 
  mutate(height_weight_ratio = height/weight) %>% 
  arrange(height_weight_ratio)
```

```
## # A tibble: 734 x 11
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Giga~ Female green     <NA>  Red          62.5 DC Comics <NA>       bad      
##  2 Utga~ Male   blue      Fros~ White        15.2 Marvel C~ <NA>       bad      
##  3 Dark~ Male   red       New ~ No Hair     267   DC Comics grey       bad      
##  4 Jugg~ Male   blue      Human Red         287   Marvel C~ <NA>       neutral  
##  5 Red ~ Male   yellow    Huma~ Black       213   Marvel C~ red        neutral  
##  6 Sasq~ Male   red       <NA>  Orange      305   Marvel C~ <NA>       good     
##  7 Hulk  Male   green     Huma~ Green       244   Marvel C~ green      good     
##  8 Bloo~ Female blue      Human Brown       218   Marvel C~ <NA>       bad      
##  9 Than~ Male   red       Eter~ No Hair     201   Marvel C~ purple     bad      
## 10 A-Bo~ Male   yellow    Human No Hair     203   Marvel C~ <NA>       good     
## # ... with 724 more rows, and 2 more variables: weight <dbl>,
## #   height_weight_ratio <dbl>
```

```r
superhero_info %>% 
  mutate(height_weight_ratio = height/weight) %>% 
  arrange(desc(height_weight_ratio))
```

```
## # A tibble: 734 x 11
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Groot Male   yellow    Flor~ <NA>          701 Marvel C~ <NA>       good     
##  2 Gala~ Male   black     Cosm~ Black         876 Marvel C~ <NA>       neutral  
##  3 Fin ~ Male   red       Kaka~ No Hair       975 Marvel C~ green      good     
##  4 Long~ Male   blue      Human Blond         188 Marvel C~ <NA>       good     
##  5 Jack~ Male   blue      Human Brown          71 Dark Hor~ <NA>       good     
##  6 Rock~ Male   brown     Anim~ Brown         122 Marvel C~ <NA>       good     
##  7 Dash  Male   blue      Human Blond         122 Dark Hor~ <NA>       good     
##  8 Howa~ Male   brown     <NA>  Yellow         79 Marvel C~ <NA>       good     
##  9 Swarm Male   yellow    Muta~ No Hair       196 Marvel C~ yellow     bad      
## 10 Yoda  Male   brown     Yoda~ White          66 George L~ green      good     
## # ... with 724 more rows, and 2 more variables: weight <dbl>,
## #   height_weight_ratio <dbl>
```

The heaviest in relation to their height is Giganta and the tallest hero relative to their weight is Groot. 
## `superhero_powers`
Have a quick look at the `superhero_powers` data frame.  

```r
glimpse(superhero_powers)
```

```
## Rows: 667
## Columns: 168
## $ hero_names                   <chr> "3-D Man", "A-Bomb", "Abe Sapien", "Ab...
## $ agility                      <lgl> TRUE, FALSE, TRUE, FALSE, FALSE, FALSE...
## $ accelerated_healing          <lgl> FALSE, TRUE, TRUE, FALSE, TRUE, FALSE,...
## $ lantern_power_ring           <lgl> FALSE, FALSE, FALSE, TRUE, FALSE, FALS...
## $ dimensional_awareness        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ cold_resistance              <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ durability                   <lgl> FALSE, TRUE, TRUE, FALSE, FALSE, FALSE...
## $ stealth                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ energy_absorption            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ flight                       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ danger_sense                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ underwater_breathing         <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ marksmanship                 <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ weapons_master               <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ power_augmentation           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ animal_attributes            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ longevity                    <lgl> FALSE, TRUE, TRUE, FALSE, FALSE, FALSE...
## $ intelligence                 <lgl> FALSE, FALSE, TRUE, FALSE, TRUE, TRUE,...
## $ super_strength               <lgl> TRUE, TRUE, TRUE, FALSE, TRUE, TRUE, T...
## $ cryokinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ telepathy                    <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ energy_armor                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ energy_blasts                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ duplication                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ size_changing                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ density_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ stamina                      <lgl> TRUE, TRUE, TRUE, FALSE, TRUE, FALSE, ...
## $ astral_travel                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ audio_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ dexterity                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ omnitrix                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ super_speed                  <lgl> TRUE, FALSE, FALSE, FALSE, TRUE, TRUE,...
## $ possession                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ animal_oriented_powers       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ weapon_based_powers          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ electrokinesis               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ darkforce_manipulation       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ death_touch                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ teleportation                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ enhanced_senses              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ telekinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ energy_beams                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ magic                        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ hyperkinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ jump                         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ clairvoyance                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ dimensional_travel           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ power_sense                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ shapeshifting                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ peak_human_condition         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ immortality                  <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, TRUE...
## $ camouflage                   <lgl> FALSE, TRUE, FALSE, FALSE, FALSE, FALS...
## $ element_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ phasing                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ astral_projection            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ electrical_transport         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ fire_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ projection                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ summoning                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ enhanced_memory              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ reflexes                     <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ invulnerability              <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, TRUE...
## $ energy_constructs            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ force_fields                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ self_sustenance              <lgl> FALSE, TRUE, FALSE, FALSE, FALSE, FALS...
## $ anti_gravity                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ empathy                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ power_nullifier              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ radiation_control            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ psionic_powers               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ elasticity                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ substance_secretion          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ elemental_transmogrification <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ technopath_cyberpath         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ photographic_reflexes        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ seismic_power                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ animation                    <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, FALS...
## $ precognition                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ mind_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ fire_resistance              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ power_absorption             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ enhanced_hearing             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ nova_force                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ insanity                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ hypnokinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ animal_control               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ natural_armor                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ intangibility                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ enhanced_sight               <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ molecular_manipulation       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ heat_generation              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ adaptation                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ gliding                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ power_suit                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ mind_blast                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ probability_manipulation     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ gravity_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ regeneration                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ light_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ echolocation                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ levitation                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ toxin_and_disease_control    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ banish                       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ energy_manipulation          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ heat_resistance              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ natural_weapons              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ time_travel                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ enhanced_smell               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ illusions                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ thirstokinesis               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ hair_manipulation            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ illumination                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ omnipotent                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ cloaking                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ changing_armor               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ power_cosmic                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRU...
## $ biokinesis                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ water_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ radiation_immunity           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_telescopic            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ toxin_and_disease_resistance <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ spatial_awareness            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ energy_resistance            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ telepathy_resistance         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ molecular_combustion         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ omnilingualism               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ portal_creation              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ magnetism                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ mind_control_resistance      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ plant_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ sonar                        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ sonic_scream                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ time_manipulation            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ enhanced_touch               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ magic_resistance             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ invisibility                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ sub_mariner                  <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALS...
## $ radiation_absorption         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ intuitive_aptitude           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_microscopic           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ melting                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ wind_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ super_breath                 <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, FALS...
## $ wallcrawling                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_night                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_infrared              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ grim_reaping                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ matter_absorption            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ the_force                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ resurrection                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ terrakinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_heat                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vitakinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ radar_sense                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ qwardian_power_ring          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ weather_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_x_ray                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_thermal               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ web_creation                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ reality_warping              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ odin_force                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ symbiote_costume             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ speed_force                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ phoenix_force                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ molecular_dissipation        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ vision_cryo                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ omnipresent                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
## $ omniscient                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FAL...
```

14. How many superheros have a combination of accelerated healing, durability, and super strength?

```r
superhero_powers %>% 
  filter(accelerated_healing == "TRUE" , durability == "TRUE" , super_strength == "TRUE")
```

```
## # A tibble: 97 x 168
##    hero_names agility accelerated_hea~ lantern_power_r~ dimensional_awa~
##    <chr>      <lgl>   <lgl>            <lgl>            <lgl>           
##  1 A-Bomb     FALSE   TRUE             FALSE            FALSE           
##  2 Abe Sapien TRUE    TRUE             FALSE            FALSE           
##  3 Angel      TRUE    TRUE             FALSE            FALSE           
##  4 Anti-Moni~ FALSE   TRUE             FALSE            TRUE            
##  5 Anti-Venom FALSE   TRUE             FALSE            FALSE           
##  6 Aquaman    TRUE    TRUE             FALSE            FALSE           
##  7 Arachne    TRUE    TRUE             FALSE            FALSE           
##  8 Archangel  TRUE    TRUE             FALSE            FALSE           
##  9 Ardina     TRUE    TRUE             FALSE            FALSE           
## 10 Ares       TRUE    TRUE             FALSE            FALSE           
## # ... with 87 more rows, and 163 more variables: cold_resistance <lgl>,
## #   durability <lgl>, stealth <lgl>, energy_absorption <lgl>, flight <lgl>,
## #   danger_sense <lgl>, underwater_breathing <lgl>, marksmanship <lgl>,
## #   weapons_master <lgl>, power_augmentation <lgl>, animal_attributes <lgl>,
## #   longevity <lgl>, intelligence <lgl>, super_strength <lgl>,
## #   cryokinesis <lgl>, telepathy <lgl>, energy_armor <lgl>,
## #   energy_blasts <lgl>, duplication <lgl>, size_changing <lgl>,
## #   density_control <lgl>, stamina <lgl>, astral_travel <lgl>,
## #   audio_control <lgl>, dexterity <lgl>, omnitrix <lgl>, super_speed <lgl>,
## #   possession <lgl>, animal_oriented_powers <lgl>, weapon_based_powers <lgl>,
## #   electrokinesis <lgl>, darkforce_manipulation <lgl>, death_touch <lgl>,
## #   teleportation <lgl>, enhanced_senses <lgl>, telekinesis <lgl>,
## #   energy_beams <lgl>, magic <lgl>, hyperkinesis <lgl>, jump <lgl>,
## #   clairvoyance <lgl>, dimensional_travel <lgl>, power_sense <lgl>,
## #   shapeshifting <lgl>, peak_human_condition <lgl>, immortality <lgl>,
## #   camouflage <lgl>, element_control <lgl>, phasing <lgl>,
## #   astral_projection <lgl>, electrical_transport <lgl>, fire_control <lgl>,
## #   projection <lgl>, summoning <lgl>, enhanced_memory <lgl>, reflexes <lgl>,
## #   invulnerability <lgl>, energy_constructs <lgl>, force_fields <lgl>,
## #   self_sustenance <lgl>, anti_gravity <lgl>, empathy <lgl>,
## #   power_nullifier <lgl>, radiation_control <lgl>, psionic_powers <lgl>,
## #   elasticity <lgl>, substance_secretion <lgl>,
## #   elemental_transmogrification <lgl>, technopath_cyberpath <lgl>,
## #   photographic_reflexes <lgl>, seismic_power <lgl>, animation <lgl>,
## #   precognition <lgl>, mind_control <lgl>, fire_resistance <lgl>,
## #   power_absorption <lgl>, enhanced_hearing <lgl>, nova_force <lgl>,
## #   insanity <lgl>, hypnokinesis <lgl>, animal_control <lgl>,
## #   natural_armor <lgl>, intangibility <lgl>, enhanced_sight <lgl>,
## #   molecular_manipulation <lgl>, heat_generation <lgl>, adaptation <lgl>,
## #   gliding <lgl>, power_suit <lgl>, mind_blast <lgl>,
## #   probability_manipulation <lgl>, gravity_control <lgl>, regeneration <lgl>,
## #   light_control <lgl>, echolocation <lgl>, levitation <lgl>,
## #   toxin_and_disease_control <lgl>, banish <lgl>, energy_manipulation <lgl>,
## #   heat_resistance <lgl>, ...
```
There are 97 heros with all these powers. 

<style>
div.blue { background-color:#e6f0ff; border-radius: 5px; padding: 20px;}
</style>
<div class = "blue">

## `kinesis`
15. We are only interested in the superheros that do some kind of "kinesis". How would we isolate them from the `superhero_powers` data?

```r
superhero_powers %>% 
  select(ends_with("kinesis"))
```

```
## # A tibble: 667 x 9
##    cryokinesis electrokinesis telekinesis hyperkinesis hypnokinesis
##    <lgl>       <lgl>          <lgl>       <lgl>        <lgl>       
##  1 FALSE       FALSE          FALSE       FALSE        FALSE       
##  2 FALSE       FALSE          FALSE       FALSE        FALSE       
##  3 FALSE       FALSE          FALSE       FALSE        FALSE       
##  4 FALSE       FALSE          FALSE       FALSE        FALSE       
##  5 FALSE       FALSE          FALSE       FALSE        FALSE       
##  6 FALSE       FALSE          FALSE       FALSE        FALSE       
##  7 FALSE       FALSE          FALSE       FALSE        FALSE       
##  8 FALSE       FALSE          FALSE       FALSE        FALSE       
##  9 FALSE       FALSE          FALSE       FALSE        FALSE       
## 10 FALSE       FALSE          FALSE       FALSE        FALSE       
## # ... with 657 more rows, and 4 more variables: thirstokinesis <lgl>,
## #   biokinesis <lgl>, terrakinesis <lgl>, vitakinesis <lgl>
```
</div>

16. Pick your favorite superhero and let's see their powers!

```r
black_panther <- superhero_powers %>% 
  filter(hero_names == "Black Panther") 
glimpse(black_panther)
```

```
## Rows: 1
## Columns: 168
## $ hero_names                   <chr> "Black Panther"
## $ agility                      <lgl> TRUE
## $ accelerated_healing          <lgl> FALSE
## $ lantern_power_ring           <lgl> FALSE
## $ dimensional_awareness        <lgl> FALSE
## $ cold_resistance              <lgl> FALSE
## $ durability                   <lgl> FALSE
## $ stealth                      <lgl> TRUE
## $ energy_absorption            <lgl> FALSE
## $ flight                       <lgl> FALSE
## $ danger_sense                 <lgl> FALSE
## $ underwater_breathing         <lgl> FALSE
## $ marksmanship                 <lgl> TRUE
## $ weapons_master               <lgl> TRUE
## $ power_augmentation           <lgl> FALSE
## $ animal_attributes            <lgl> FALSE
## $ longevity                    <lgl> FALSE
## $ intelligence                 <lgl> FALSE
## $ super_strength               <lgl> FALSE
## $ cryokinesis                  <lgl> FALSE
## $ telepathy                    <lgl> FALSE
## $ energy_armor                 <lgl> FALSE
## $ energy_blasts                <lgl> FALSE
## $ duplication                  <lgl> FALSE
## $ size_changing                <lgl> FALSE
## $ density_control              <lgl> FALSE
## $ stamina                      <lgl> TRUE
## $ astral_travel                <lgl> FALSE
## $ audio_control                <lgl> FALSE
## $ dexterity                    <lgl> FALSE
## $ omnitrix                     <lgl> FALSE
## $ super_speed                  <lgl> FALSE
## $ possession                   <lgl> FALSE
## $ animal_oriented_powers       <lgl> FALSE
## $ weapon_based_powers          <lgl> FALSE
## $ electrokinesis               <lgl> FALSE
## $ darkforce_manipulation       <lgl> FALSE
## $ death_touch                  <lgl> FALSE
## $ teleportation                <lgl> FALSE
## $ enhanced_senses              <lgl> TRUE
## $ telekinesis                  <lgl> FALSE
## $ energy_beams                 <lgl> FALSE
## $ magic                        <lgl> FALSE
## $ hyperkinesis                 <lgl> FALSE
## $ jump                         <lgl> FALSE
## $ clairvoyance                 <lgl> FALSE
## $ dimensional_travel           <lgl> FALSE
## $ power_sense                  <lgl> FALSE
## $ shapeshifting                <lgl> FALSE
## $ peak_human_condition         <lgl> TRUE
## $ immortality                  <lgl> FALSE
## $ camouflage                   <lgl> FALSE
## $ element_control              <lgl> FALSE
## $ phasing                      <lgl> FALSE
## $ astral_projection            <lgl> FALSE
## $ electrical_transport         <lgl> FALSE
## $ fire_control                 <lgl> FALSE
## $ projection                   <lgl> FALSE
## $ summoning                    <lgl> FALSE
## $ enhanced_memory              <lgl> TRUE
## $ reflexes                     <lgl> TRUE
## $ invulnerability              <lgl> FALSE
## $ energy_constructs            <lgl> FALSE
## $ force_fields                 <lgl> FALSE
## $ self_sustenance              <lgl> FALSE
## $ anti_gravity                 <lgl> FALSE
## $ empathy                      <lgl> FALSE
## $ power_nullifier              <lgl> FALSE
## $ radiation_control            <lgl> FALSE
## $ psionic_powers               <lgl> FALSE
## $ elasticity                   <lgl> FALSE
## $ substance_secretion          <lgl> FALSE
## $ elemental_transmogrification <lgl> FALSE
## $ technopath_cyberpath         <lgl> FALSE
## $ photographic_reflexes        <lgl> FALSE
## $ seismic_power                <lgl> FALSE
## $ animation                    <lgl> FALSE
## $ precognition                 <lgl> FALSE
## $ mind_control                 <lgl> FALSE
## $ fire_resistance              <lgl> FALSE
## $ power_absorption             <lgl> FALSE
## $ enhanced_hearing             <lgl> TRUE
## $ nova_force                   <lgl> FALSE
## $ insanity                     <lgl> FALSE
## $ hypnokinesis                 <lgl> FALSE
## $ animal_control               <lgl> FALSE
## $ natural_armor                <lgl> FALSE
## $ intangibility                <lgl> FALSE
## $ enhanced_sight               <lgl> FALSE
## $ molecular_manipulation       <lgl> FALSE
## $ heat_generation              <lgl> FALSE
## $ adaptation                   <lgl> FALSE
## $ gliding                      <lgl> FALSE
## $ power_suit                   <lgl> FALSE
## $ mind_blast                   <lgl> FALSE
## $ probability_manipulation     <lgl> FALSE
## $ gravity_control              <lgl> FALSE
## $ regeneration                 <lgl> FALSE
## $ light_control                <lgl> FALSE
## $ echolocation                 <lgl> FALSE
## $ levitation                   <lgl> FALSE
## $ toxin_and_disease_control    <lgl> FALSE
## $ banish                       <lgl> FALSE
## $ energy_manipulation          <lgl> FALSE
## $ heat_resistance              <lgl> FALSE
## $ natural_weapons              <lgl> FALSE
## $ time_travel                  <lgl> FALSE
## $ enhanced_smell               <lgl> TRUE
## $ illusions                    <lgl> FALSE
## $ thirstokinesis               <lgl> FALSE
## $ hair_manipulation            <lgl> FALSE
## $ illumination                 <lgl> FALSE
## $ omnipotent                   <lgl> FALSE
## $ cloaking                     <lgl> FALSE
## $ changing_armor               <lgl> FALSE
## $ power_cosmic                 <lgl> FALSE
## $ biokinesis                   <lgl> FALSE
## $ water_control                <lgl> FALSE
## $ radiation_immunity           <lgl> FALSE
## $ vision_telescopic            <lgl> TRUE
## $ toxin_and_disease_resistance <lgl> FALSE
## $ spatial_awareness            <lgl> FALSE
## $ energy_resistance            <lgl> FALSE
## $ telepathy_resistance         <lgl> FALSE
## $ molecular_combustion         <lgl> FALSE
## $ omnilingualism               <lgl> FALSE
## $ portal_creation              <lgl> FALSE
## $ magnetism                    <lgl> FALSE
## $ mind_control_resistance      <lgl> FALSE
## $ plant_control                <lgl> FALSE
## $ sonar                        <lgl> FALSE
## $ sonic_scream                 <lgl> FALSE
## $ time_manipulation            <lgl> FALSE
## $ enhanced_touch               <lgl> FALSE
## $ magic_resistance             <lgl> FALSE
## $ invisibility                 <lgl> FALSE
## $ sub_mariner                  <lgl> FALSE
## $ radiation_absorption         <lgl> FALSE
## $ intuitive_aptitude           <lgl> FALSE
## $ vision_microscopic           <lgl> FALSE
## $ melting                      <lgl> FALSE
## $ wind_control                 <lgl> FALSE
## $ super_breath                 <lgl> FALSE
## $ wallcrawling                 <lgl> FALSE
## $ vision_night                 <lgl> TRUE
## $ vision_infrared              <lgl> FALSE
## $ grim_reaping                 <lgl> FALSE
## $ matter_absorption            <lgl> FALSE
## $ the_force                    <lgl> FALSE
## $ resurrection                 <lgl> FALSE
## $ terrakinesis                 <lgl> FALSE
## $ vision_heat                  <lgl> FALSE
## $ vitakinesis                  <lgl> FALSE
## $ radar_sense                  <lgl> FALSE
## $ qwardian_power_ring          <lgl> FALSE
## $ weather_control              <lgl> FALSE
## $ vision_x_ray                 <lgl> FALSE
## $ vision_thermal               <lgl> FALSE
## $ web_creation                 <lgl> FALSE
## $ reality_warping              <lgl> FALSE
## $ odin_force                   <lgl> FALSE
## $ symbiote_costume             <lgl> FALSE
## $ speed_force                  <lgl> FALSE
## $ phoenix_force                <lgl> FALSE
## $ molecular_dissipation        <lgl> FALSE
## $ vision_cryo                  <lgl> FALSE
## $ omnipresent                  <lgl> FALSE
## $ omniscient                   <lgl> FALSE
```
I can see from the glimpse tool that the BP has night vision, telescopic vision, enhanced smell, agility, stealth, enhanced hearing, enhanced memory, stamina, and is in peak human condition, and a marksman. 

## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.  

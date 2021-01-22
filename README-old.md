---
title: "Spatial mapping oregon AQ"
output: html_document
---

```{r setup, include=FALSE}
install.packages(leaflet)
install.packages(rgdal)
library(ggplot2)
library(leaflet)
library(rgdal)
```

```{r}
# Libraries
library(ggplot2)
library(dplyr)
 
# Get the world polygon and extract oregon
library(maps)

UK <- map_data("world") %>% filter(region=="UK")
?map_data()

# Get a data frame with longitude, latitude, and size of bubbles (a bubble = a city)
data <- world.cities %>% filter(country.etc=="UK")

ggplot() +
  geom_polygon(data = UK, aes(x=long, y = lat, group = group), fill="grey", alpha=0.3) +
  geom_point( data=data, aes(x=long, y=lat)) +
  theme_void() + ylim(50,59) + coord_map() 

```

```{r}
# virids package for the color palette
library(viridis)
 
# Left: use size and color
ggplot() +
  geom_polygon(data = UK, aes(x=long, y = lat, group = group), fill="grey", alpha=0.3) +
  geom_point( data=data, aes(x=long, y=lat, size=pop, color=pop)) +
  scale_size_continuous(range=c(1,12)) +
  scale_color_viridis(trans="log") +
  theme_void() + ylim(50,59) + coord_map() 
 
# Center: reorder your dataset first! Big cities appear later = on top
data %>%
 arrange(pop) %>% 
 mutate( name=factor(name, unique(name))) %>% 
 ggplot() +
    geom_polygon(data = UK, aes(x=long, y = lat, group = group), fill="grey", alpha=0.3) +
    geom_point( aes(x=long, y=lat, size=pop, color=pop), alpha=0.9) +
    scale_size_continuous(range=c(1,12)) +
    scale_color_viridis(trans="log") +
    theme_void() + ylim(50,59) + coord_map()
 
# Right: just use arrange(desc(pop)) instead
data %>%
 arrange(desc(pop)) %>% 
 mutate( name=factor(name, unique(name))) %>% 
 ggplot() +
    geom_polygon(data = UK, aes(x=long, y = lat, group = group), fill="grey", alpha=0.3) +
    geom_point( aes(x=long, y=lat, size=pop, color=pop), alpha=0.9) +
    scale_size_continuous(range=c(1,12)) +
    scale_color_viridis(trans="log") +
    theme_void() + ylim(50,59) + coord_map()
```

```{r} 
#Interactive version of the same map 

# Load the plotly package
library(plotly)
 
# Rorder data + Add a new column with tooltip text
data <- data %>%
  arrange(pop) %>%
  mutate( name=factor(name, unique(name))) %>%
  mutate( mytext=paste(
    "City: ", name, "\n", 
    "Population: ", pop, sep="")
  )
 
# Make the map (static)
p <- data %>%
  ggplot() +
    geom_polygon(data = UK, aes(x=long, y = lat, group = group), fill="grey", alpha=0.3) +
    geom_point(aes(x=long, y=lat, size=pop, color=pop, text=mytext, alpha=pop) ) +
    scale_size_continuous(range=c(1,15)) +
    scale_color_viridis(option="inferno", trans="log" ) +
    scale_alpha_continuous(trans="log") +
    theme_void() +
    ylim(50,59) +
    coord_map() +
    theme(legend.position = "none")
 
p <- ggplotly(p, tooltip="text")
p

# save the widget in a html file if needed.
# library(htmlwidgets)
# saveWidget(p, file=paste0( getwd(), "/HtmlWidget/bubblemapUK.html"))

```

```{r}
USA <- map_data("world") %>% filter(region=="USA")

# Get a data frame with longitude, latitude, and size of bubbles (a bubble = a city)
USA_data <- world.cities %>% filter(country.etc=="USA")

ggplot() +
  geom_polygon(data = USA, aes(x=long, y = lat, group = group), fill="grey", alpha=0.3) +
  geom_point( data=USA_data, aes(x=long, y=lat)) +
  theme_void() + ylim(22,52) + xlim(-130,-60) + coord_map() 
```
```{r}
# virids package for the color palette
library(viridis)
 
# Left: use size and color
ggplot() +
  geom_polygon(data = USA, aes(x=long, y = lat, group = group), fill="grey", alpha=0.3) +
  geom_point( data=USA_data, aes(x=long, y=lat, size=pop, color=pop)) +
  scale_size_continuous(range=c(1,12)) +
  scale_color_viridis(trans="log") +
  theme_void()+ ylim(22,52) + xlim(-130,-60)+ coord_map() 
 
# Center: reorder your dataset first! Big cities appear later = on top
data %>%
 arrange(pop) %>% 
 mutate( name=factor(name, unique(name))) %>% 
 ggplot() +
    geom_polygon(data = USA, aes(x=long, y = lat, group = group), fill="grey", alpha=0.3) +
    geom_point(data=USA_data,  aes(x=long, y=lat, size=pop, color=pop), alpha=0.9) +
    scale_size_continuous(range=c(1,12)) +
    scale_color_viridis(trans="log") +
    theme_void() + ylim(22,52) + xlim(-130,-60) + coord_map()
 
# Right: just use arrange(desc(pop)) instead
data %>%
 arrange(desc(pop)) %>% 
 mutate( name=factor(name, unique(name))) %>% 
 ggplot() +
    geom_polygon(data = USA, aes(x=long, y = lat, group = group), fill="grey", alpha=0.3) +
    geom_point(data=USA_data,  aes(x=long, y=lat, size=pop, color=pop), alpha=0.9) +
    scale_size_continuous(range=c(1,12)) +
    scale_color_viridis(trans="log") +
    theme_void() + ylim(22,52) + xlim(-130,-60) + coord_map()
```

```{r} 
#Interactive version of the same map 

# Load the plotly package
library(plotly)
 
# Rorder data + Add a new column with tooltip text
USA_data <- USA_data %>%
  arrange(pop) %>%
  mutate( name=factor(name, unique(name))) %>%
  mutate( mytext=paste(
    "City: ", name, "\n", 
    "Population: ", pop, sep="")
  )
 
# Make the map (static)
 U <- USA_data %>%
  ggplot() +
    geom_polygon(data = USA, aes(x=long, y = lat, group = group), fill="grey", alpha=0.3) +
    geom_point( aes(x=long, y=lat, size=pop, color=pop, text=mytext, alpha=pop) ) +
    scale_size_continuous(range=c(1,15)) +
    scale_color_viridis(option="inferno", trans="log" ) +
    scale_alpha_continuous(trans="log") +
    theme_void() +
   ylim(22,52) + xlim(-130,-60) +
    coord_map() +
    theme(legend.position = "none")
 
U <-  ggplotly(U, tooltip="text")
U
```

```{r}
library(raster)
or_shp <- shapefile("/Users/kiwharton/Desktop/PA/oregon_highway")

or_shp <- readORG(dsn = "//Users/kiwharton/Desktop/PA/oregon_highway", layer = "oregon_highway")


```


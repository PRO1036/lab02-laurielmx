Lab 02 - Plastic waste
================
Laurie Lemieux
Le 22 septembre 2025

## Chargement des packages et des données

``` r
library(tidyverse) 
```

``` r
plastic_waste <- read_csv("data/plastic-waste.csv")
```

Commençons par filtrer les données pour retirer le point représenté par
Trinité et Tobago (TTO) qui est un outlier.

``` r
plastic_waste <- plastic_waste %>%
  filter(plastic_waste_per_cap < 3.5)
```

## Exercices

### Exercise 1

``` r
ggplot(plastic_waste, aes(x = plastic_waste_per_cap )) +
  geom_histogram(binwidth = 0.2) +
  facet_wrap(~ continent) + 
  labs(
    title = "Distribution de la production de déchets plastiques par habitant",
    subtitle = "Comparaison entre continents",
    x = "Déchets plastiques par habitant (kg/jour)",
    y = "Fréquence"
  )
```

![](lab-02_files/figure-gfm/plastic-waste-continent-1.png)<!-- -->

### Exercise 2

``` r
ggplot(plastic_waste, aes(x = plastic_waste_per_cap, color = continent, fill = continent )) + 
  geom_density(alpha = 0.3) +
  labs( title = "Distribution des déchets plastiques par habitant", subtitle = "Par continents", x = "Déchets plastiques par habitant (kg/jour)", y = "Densité")+
  scale_color_discrete(name = "Continents") +
  scale_fill_discrete(name = "Continents")
```

![](lab-02_files/figure-gfm/plastic-waste-density-1.png)<!-- -->

La couleur (color et fill) change selon une variable, ici le continent,
c’est pourquoi elle se trouve dans aes(). La transparence (alpha) est
plutôt une valeur esthétique fixe, donc elle se trouve dans
geom_density().

### Exercise 3

Boxplot:

``` r
ggplot(plastic_waste, aes(x = continent, y = plastic_waste_per_cap)) +
  geom_boxplot() +
  labs(
    title = "Répartition des déchets plastiques par habitant", 
    subtitle = "Par continents", 
    x = "Continents",
    y = "Déchets plastiques par habitant (kg/jour)"
  )
```

![](lab-02_files/figure-gfm/plastic-waste-boxplot-1.png)<!-- -->

Violin plot:

``` r
ggplot(plastic_waste, aes(x = continent, y = plastic_waste_per_cap)) +
  geom_violin() +
  labs(
    title = "Répartition des déchets plastiques par habitant",
    subtitle = "Par continents",
    x = "Continents",
    y = "Déchets plastiques par habitant (kg/jour)"
  )
```

![](lab-02_files/figure-gfm/plastic-waste-violin-1.png)<!-- -->

Un boxplot résume les données avec la médiane, les quartiles et les
valeurs extrêmes, mais il ne montre pas comment les valeurs sont
réparties à l’intérieur de la boîte. Le violin plot ajoute cette
information en affichant la forme de la distribution : on peut voir si
les données sont concentrées, dispersées, symétriques, etc.

### Exercise 4

``` r
ggplot(plastic_waste, aes(x = plastic_waste_per_cap,  
                          y = mismanaged_plastic_waste_per_cap, 
                          color = continent)) + 
  geom_point() +
  labs(
    title = "Déchets plastiques mal gérés en fonction des déchets produits",
    subtitle = "Par continents",
    x = "Déchets plastiques par habitant (kg/jour)",
    y = "Déchets plastiques mal gérés par habitant (kg/jour)",
    color = "Continents"
  )
```

![](lab-02_files/figure-gfm/plastic-waste-mismanaged-1.png)<!-- -->

On observe une relation positive : plus la quantité de déchets
plastiques par habitant augmente, plus la quantité de déchets mal gérés
tend aussi à augmenter. La tendance varie selon le continent, l’Asie et
l’Amérique du Sud ayant davantage de déchets non gérés, tandis que
l’Europe et l’Amérique du Nord restent plus bas même avec une production
moyenne.

### Exercise 5

``` r
ggplot(plastic_waste, aes(x = plastic_waste_per_cap, y = total_pop)) + 
  geom_point() +
  labs(
    title = "Relation entre les déchets plastiques par habitant et la population totale",
    x = "Déchets plastiques par habitant (kg/jour)",
    y = "Population totale"
  )
```

    ## Warning: Removed 10 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](lab-02_files/figure-gfm/plastic-waste-population-total-1.png)<!-- -->

``` r
ggplot(plastic_waste, aes(x = plastic_waste_per_cap, y = coastal_pop)) + 
  geom_point() +
  labs(
    title = "Relation entre les déchets plastiques par habitant et la population côtière",
    x = "Déchets plastiques par habitant (kg/jour)",
    y = "Population vivant près des côtes"
  )
```

![](lab-02_files/figure-gfm/plastic-waste-population-coastal-1.png)<!-- -->

Les deux graphiques montrent surtout une grande concentration de points
près de zéro, avec quelques valeurs extrêmes qui dominent l’échelle.
Visuellement, il n’y a pas de tendance claire ni de différence notable
entre la population totale et la population côtière par rapport aux
déchets plastiques par habitant.

## Conclusion

Recréez la visualisation:

``` r
plastic_waste_coastal <- plastic_waste %>% 
  mutate(coastal_pop_prop = coastal_pop / total_pop) %>% 
  filter(plastic_waste_per_cap < 3)

ggplot(plastic_waste_coastal, aes(x = coastal_pop_prop, 
                                  y = plastic_waste_per_cap, 
                                  color = continent)) +
  geom_point() +
  geom_smooth(method = "loess", se = TRUE, color = "black") +
  labs(title = "Quantité de déchets plastiques vs Proportion de la population côtière",
       subtitle = "Selon le continent",
       x = "Proportion de la population côtière (Coastal / total population)",
       y = "Nombre de déchets plastiques par habitant", color = "Continent") +
  theme_minimal()
```

    ## `geom_smooth()` using formula = 'y ~ x'

    ## Warning: Removed 10 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 10 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](lab-02_files/figure-gfm/recreate-viz-1.png)<!-- --> On observe une
tendance légèrement positive : plus la proportion de population vivant
près des côtes augmente, plus la production de déchets plastiques par
habitant tend à croître. La relation reste faible et très variable selon
les continents.

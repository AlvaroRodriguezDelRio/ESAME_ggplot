
Content adapted from <https://r-statistics.co/Complete-Ggplot2-Tutorial-Part1-With-R-Code.html>.


# installations

```{r cars}
install.packages("ggplot2")
library(ggplot2)
install.packages("MedDataSets")
library(MedDataSets)
data(babies_tbl_df)
View(babies_tbl_df)
library(tidyr)
library(dplyr)

```

####
# one dimension
###


```{r pressure, echo=FALSE}

ggplot(babies_tbl_df)
ggplot(babies_tbl_df, aes(x=bwt))

# histogram
ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_histogram()

# density
ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_density()

```

####
# two dimensions
####

```{r pressure, echo=FALSE}

### scatter plots
ggplot(babies_tbl_df, aes(y=bwt,x = gestation))

ggplot(babies_tbl_df, aes(y=bwt,x = gestation))+ 
  geom_point()

# add regresion line
ggplot(babies_tbl_df, aes(y=bwt,x = gestation))+ 
  geom_point() + 
  geom_smooth(method="lm") 

### boxplots
babies_tbl_df$smoke = as.factor(babies_tbl_df$smoke)
ggplot(babies_tbl_df, aes(x=smoke, y=bwt)) + 
  geom_boxplot()

ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_boxplot()


library(ggpubr)
ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_boxplot()+
  stat_compare_means(aes(group=smoke),label = "p.signif", method = "wilcox.test",
                     ref.group = "0",hide.ns = TRUE, tip.length = 0,paired = F)


ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_violin()

```

####
# axis control
#### 


```{r pressure, echo=FALSE}
# limit x and y axis
ggplot(babies_tbl_df, aes(y=bwt,x = gestation))+ 
  geom_point() + 
  geom_smooth(method="lm") +
  ylim(c(0, 200)) + 
  xlim(c(0, 500)) 

ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_boxplot()+
  ylim(c(0, 200))


# titles and axis labels
ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point() + 
  geom_smooth(method="lm") +
  ggtitle("Relation between body weight and gestation time", subtitle="From babies_tbl_df dataset") + xlab("Gestation time (weeks)") + ylab("Body weight (grams)")


```

#####
# coordinates
#####

```{r pressure, echo=FALSE}
# coord flip
ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_boxplot()+
  coord_flip()

ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_histogram()+
  coord_polar()


```

#####
# color control
#####

```{r pressure, echo=FALSE}

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(color = 'blue', size=3)


ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_histogram(aes(fill = smoke))
  
ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = smoke), size=3)

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = age), size=3)

ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_boxplot(aes(fill = smoke))

# color palletes
ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = smoke), size=3)+
  scale_color_brewer(palette = "Set1")

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = age), size=3)+
  scale_color_continuous(type = "viridis")


```

####
# facets 
#### 

```{r pressure, echo=FALSE}

ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_histogram(aes(fill = parity))+
  facet_grid(~smoke)

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = age), size=3)+
  facet_grid(~smoke)

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = age), size=3)+
  facet_grid(parity~smoke)

```
#####
# themes
#####

```{r pressure, echo=FALSE}

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = age), size=3)+
  scale_color_continuous(type = "viridis")+
  theme_classic()

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = age), size=3)+
  scale_color_continuous(type = "viridis")+
  theme(plot.background = element_rect(fill = "green"),
        axis.text.x = element_text(angle = 45, hjust = 1))



```


####
# combine plots
####

```{r pressure, echo=FALSE}

library(patchwork)

p1 = ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_histogram(aes(fill = smoke))+
  facet_grid(~smoke)

p2 = ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = age), size=3)+
  facet_grid(~smoke)

p1 + p2
p1 / p2 +   plot_annotation(tag_levels = 'A')

```

# save high quality images

```{r pressure, echo=FALSE}

svg("~/analysis/test.svg")
print(p1)
graphics.off()

```




Content adapted from <https://r-statistics.co/Complete-Ggplot2-Tutorial-Part1-With-R-Code.html>.


# installations

```{r cars}
install.packages("ggplot2")
library(ggplot2)
library(tidyr)
library(dplyr)

```

####
# data
###

We will use data from the MedDataSets collection (more information on the datasets included in https://cran.r-project.org/web/packages/MedDataSets/MedDataSets.pdf)

```{r cars}
# install and load library
install.packages("MedDataSets") 
library(MedDataSets)

# explore the data
data(babies_tbl_df)
View(babies_tbl_df)
names(babies_tbl_df)

# data: first layer of plots
ggplot(babies_tbl_df)
```

####
# aesthetic & geometry
###


```{r pressure, echo=FALSE}

# variables to use for plotting are indicated with aes()
ggplot(babies_tbl_df, aes(x=bwt))
```

After aesthetics are defined, we can specify geometries (geom_X)

```{r pressure, echo=FALSE}

# histograms
ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_histogram()

# density plots
ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_density()

# scatter plots
ggplot(babies_tbl_df, aes(y=bwt,x = gestation))+ 
  geom_point()


# boxplots
ggplot(babies_tbl_df, aes(x=smoke, y=bwt)) + 
  geom_boxplot()

ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_boxplot()

# violin plots
ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_violin()

# and many more...
```

#####
# color control and palletes
#####

Colors add additional layers of information to plots. 

```{r pressure, echo=FALSE}

# discrete
ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_histogram(aes(fill = smoke))
  
ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = smoke), size=3)

ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_boxplot(aes(fill = smoke))

# continuous
ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = age), size=3)

# color palletes
ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = smoke), size=3)+
  scale_color_brewer(palette = "Set1")

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = age), size=3)+
  scale_color_continuous(type = "viridis")

```

You can find a list of colour palletes included in ggplot in https://www.sthda.com/english/wiki/ggplot2-colors-how-to-change-colors-automatically-and-manually, but many other exist in other libraries


####
# facets 
#### 

Facets are useful for visualizing data separatelly for different categories

```{r pressure, echo=FALSE}

ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_histogram(aes(fill = parity))+
  facet_grid(~smoke)

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = age), size=3)+
  facet_grid(~smoke)

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = age), size=3)+
  facet_grid(parity~smoke)
```

####
# statistics
####

Statistics allow to include statistical tests into our plots 

```{r pressure, echo=FALSE}
# add regresion line
ggplot(babies_tbl_df, aes(y=bwt,x = gestation))+ 
  geom_point() + 
  geom_smooth(method="lm") 

# add significance to boxplots
library(ggpubr)
ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_boxplot()+
  stat_compare_means(aes(group=smoke),label = "p.signif", method = "wilcox.test",
                     ref.group = "0",hide.ns = TRUE, tip.length = 0,paired = F)

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


####
# themes
#### 

Themes are applied for improving the appearance and legibility of the plots

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
# combining plots
####

For combining ggplots, the most widely used package is patchwork (https://patchwork.data-imaginist.com/)

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
p1 / p2 + plot_annotation(tag_levels = 'A')

```


###
# save high quality images
###

```{r pressure, echo=FALSE}

install.packages('svglite')
library(svglite)
ggsave(file="~/analysis/test.svg", plot = p1, width=10, height=8)

```

#####
# tables
#####

Tables can usually be adjusted easily in Word, you can format the data in any specific format, dowload the csv file, and load it to Word. 

```{r pressure, echo=FALSE}

d_table = babies_tbl_df %>% 
  group_by(age) %>% 
  summarise(mean_weight = mean(weight),
            n = n()) %>% 
  filter(!is.na(mean_weight))

write.csv(d_table,'~/analysis/table.csv')
```

####
# excersises
####

Load the colmozzie_tbl_df dataframe from the MedDataSets library and: 

1) plot the distribution of dengue cases per week
2) Visualize how dengue cases have changed with time
3) Plot the relation of Dengue cases and maximum temperature every year for which data was gathered
4) Combine the last two plots into a nicely formatted figure


```{r pressure, echo=FALSE}
data(colmozzie_tbl_df)
names(colmozzie_tbl_df)


# distribution of dengue cases
ggplot(colmozzie_tbl_df,aes(x = Cases))+
  geom_histogram()


#Have dengue cases increase in time?
ggplot(colmozzie_tbl_df,aes(x = as.factor(Year), y = Cases))+
  geom_boxplot()+
    stat_compare_means(aes(group=Year),label = "p.signif", method = "wilcox.test",
                     ref.group = "2009",hide.ns = TRUE, tip.length = 0,paired = F)


ggplot(colmozzie_tbl_df,aes(x = Year, y = Cases))+
  geom_point()+
  geom_smooth(method = 'lm')


# plot relation of dengue cases with mean temperature, per year
ggplot(colmozzie_tbl_df,aes(x = V, y = Cases))+
  geom_point()+
  geom_smooth() 

ggplot(colmozzie_tbl_df,aes(x = TMAX, y = Cases, color = as.factor(Year)))+
  geom_point()+
  geom_smooth()+
  facet_grid(~Year,scales = 'free')


# combine the two previous plots into a nicely formatted plot 
p1 = ggplot(colmozzie_tbl_df,aes(x = Year, y = Cases))+
  geom_point()+
  geom_smooth(method = 'lm')+
  theme_classic()
  

p1

p2 = ggplot(colmozzie_tbl_df,aes(x = TMAX, y = Cases, color = as.factor(Year)))+
  geom_point()+
  geom_smooth()+
  facet_wrap(~Year)+
  theme_classic()+
  scale_color_brewer(palette = "Set1")+
  xlab('Maximum temperature')+
  scale_x_continuous(breaks=seq(28, 32, 2))+
  labs(color = "Year")
  

p2

p1 + p2 + plot_annotation(tag_levels = 'A')

```

```{r pressure, echo=FALSE}
# which variables are correlated with dengue cases?
library(corrplot)
colmozzie_tbl_df$SLP = as.numeric(colmozzie_tbl_df$SLP)
correlations = cor(colmozzie_tbl_df)
corrplot(correlations)

```

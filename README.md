
Material for Mbioindi data visualization seminar.  

# installations

```{r cars}
install.packages("ggplot2")
library(ggplot2)
library(tidyr)
library(dplyr)

```

####
# Data
###

We will use data from the MedDataSets collection (more information on the datasets in https://cran.r-project.org/web/packages/MedDataSets/MedDataSets.pdf)

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

![1](1.png)

####
# Aesthetics
###

Aesthetics indicates which variables are plotted in the x axis, in the y axis, and which are used for plotting different colors / sizes / shapes etc. Using the aes() command, we can map variables from the dataframe to each of these. 


```{r pressure, echo=FALSE}

# variables to use for plotting are indicated with aes()
names(babies_tbl_df)
ggplot(babies_tbl_df, aes(x=bwt,y = age))
```

#####
# Geometries
#####

After aesthetics are defined, we can specify geometries (geom_XXXX() commands). Geometries indicate the way in which you want to represent the data indicated in the aesthetics. There are many predifined geometries in ggplot, the most common are the following.

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

ggplot(babies_tbl_df, aes(x=smoke,y = bwt))+ 
  geom_point()

# boxplots
# require discrete values (factors with different levels) in 
# the x-axis
ggplot(babies_tbl_df, aes(x=as.factor(smoke), y=bwt)) + 
  geom_boxplot()

# boxplots 
# NA factors can be discarded by previous manipulation of the data
ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=as.factor(smoke), y=bwt)) + 
  geom_boxplot()

# violin plots
ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_violin()


```

#####
# Color control and palletes
#####

Colors (also shapes and sizes) add additional layers of information to plots, and can help to distinguish between different groups.


```{r pressure, echo=FALSE}

# discrete coloring
ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_histogram(aes(fill = as.factor(smoke)))
  
ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=bwt))+ 
  geom_density(aes(fill = as.factor(smoke)),alpha = 0.5)

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = smoke), size=3)

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(shape = smoke), size=3)

ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_boxplot(aes(fill = smoke))

# continuous coloring
ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = age), size=3)

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(size = age), size=3)

```

Choosing the right color pallete is important for distinguishing the different groups clearly. It is also important to bear in mind vision deficiencies like color blidness for choosing color palletes. 

There are many pre-built color palletes which account for this. You can find a list of colour palletes included in ggplot in https://www.sthda.com/english/wiki/ggplot2-colors-how-to-change-colors-automatically-and-manually, but many other exist in external libraries.

```{r pressure, echo=FALSE}

# color palletes
ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=bwt))+ 
  geom_density(aes(fill = as.factor(smoke)),alpha = 0.5)+
  scale_fill_brewer(palette = "Set4")

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = as.factor(smoke)), size=3)+
  scale_color_brewer(palette = "Set2")

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = as.factor(smoke)), size=3)+
  scale_color_brewer(palette = "PuOr")

ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point(aes(color = age), size=3)+
  scale_color_continuous(type = "viridis")

```

####
# facets 
#### 

Facets are useful for visualizing data separately for different categories (similarly as colors, but having each categorical variable in an independent panel)

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

Allow to include statistical tests into our plots. 

```{r pressure, echo=FALSE}
# add regresion line to scatter plots
ggplot(babies_tbl_df, aes(y=bwt,x = gestation))+ 
  geom_point() + 
  geom_smooth(method="lm") 

# add correlation coefficients
correlation = cor.test(babies_tbl_df$bwt,babies_tbl_df$gestation)$estimate
pvalue = cor.test(babies_tbl_df$bwt,babies_tbl_df$gestation)$p.value
label = paste('R =',round(correlation,2),'\n','p-value =',round(pvalue,4))

ggplot(babies_tbl_df, aes(y=bwt,x = gestation))+ 
  geom_point() + 
  geom_smooth(method="lm")+
  geom_text(x=150, y=150, label = label)

# add significance to boxplots
install.packages('ggpubr')
library(ggpubr)
ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=as.factor(smoke), y=bwt)) + 
  geom_boxplot()+
  stat_compare_means(aes(group=smoke),label = "p.signif", method = "wilcox.test",
                     ref.group = "0",hide.ns = TRUE)

```

#####
# coordinate systems
#####


```{r pressure, echo=FALSE}
# Flip coordinates
ggplot(babies_tbl_df %>% 
         filter(!is.na(smoke)), aes(x=smoke, y=bwt)) + 
  geom_boxplot()+
  coord_flip()


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

# change coordinate systems
ggplot(babies_tbl_df, aes(x=bwt))+ 
  geom_histogram()+
  coord_polar()
```


####
# themes
####

Themes are applied for boosting the appearance and legibility of the plots. 

```{r pressure, echo=FALSE}

# titles and axis labels
ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_point() + 
  geom_smooth(method="lm") +
  ggtitle("Relation between body weight and gestation time", subtitle="From babies_tbl_df dataset") + 
  xlab("Gestation time (weeks)") + 
  ylab("Body weight (grams)")

# backround color
ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = age), size=3)+
  scale_color_continuous(type = "viridis")+
  theme(plot.background = element_rect(fill = "green"),
        axis.text.x = element_text(angle = 45, hjust = 1))

# predefined themes
ggplot(babies_tbl_df, aes(x=bwt,y = gestation))+ 
  geom_smooth(method="lm") +
  geom_point(aes(color = age), size=3)+
  scale_color_continuous(type = "viridis")+
  theme_classic()

```

####
# combining plots
####

For combining ggplots, the most widely used package is patchwork (https://patchwork.data-imaginist.com/)

```{r pressure, echo=FALSE}

install.packages('patchwork')
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
# Save high quality images
###

You can save HQ figures figure directly from Rstudio without the need for scripting (export > save as image > select svg format). The scripting equivalent is:  

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

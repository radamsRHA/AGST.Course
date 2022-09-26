---
output:
  html_document: default
  pdf_document: default
---

---
# AGST.Course: Introduction to agricultural statistcs

## Running R studio cloud
Please make an Rstudio account and login here: https://rstudio.cloud/

Also you can download and install Rstudio on your own computer: https://www.rstudio.com


## Installing R package AGST.Course from github
The R package AGST.Course is freely available to download and distribute from github <https://github.com/radamsRHA/AGST.Course/>. To install and load ROBRT, you must first install the R package `devtools`, 

```
install.packages("devtools")
```
Now using devtools we can install `AGST.Course` from github:

```
library(devtools)
install_github("radamsRHA/AGST.Course")
library(AGST.Course) # Load package 
```
to follow along with this tutorial, `AGST.Course` also requires the following dependencies to be installed:

```
install.packages("agridat")
install.packages(”devtools")
install.packages(”glmnet")
install.packages(”ggplot2")

library(devtools)
library(agridat)
library(AGST.Course)
library(ggplot2)

```

## Tutorial with logistic regression
Step 1) load input packages

```
library(devtools) 
install_github("radamsRHA/AGST.Course")
library(AGST.Course)
library(ggplot2)
library(dplyr)
```

Step 2) visualize FungInfectData

```
head(FungInfectData) 
```

Step 4) always visualize your data!

```
plot(FungInfect ~ InsectCount, data = FungInfectData)
boxplot(InsectCount ~ FungInfect, data = FungInfectData)
```

Step 5) fit linear model

```
lm.fit <- lm(FungInfect ~ InsectCount, FungInfectData)
summary(lm.fit)
```

Step 6) Visualize data and model

```
plot(FungInfect ~ InsectCount, data = FungInfectData)
abline(lm.fit, col = "red")
```

Step 7) Visualize logistic regression model

```
FungInfectData %>%
	ggplot(mapping = aes(x = InsectCount, y = FungInfect)) + 	geom_point() +
    	geom_smooth(method = "glm", 
			method.args = c(family = "binomial"))
```

Step 8) Fit GLM model for logistic regression

```
glm.fit <- glm(FungInfect ~ InsectCount, FungInfectData,
		     family = "binomial")
summary(glm.fit)
```

Step 9) Make a prediction for a plot with 2000 insect counts

```
predict(glm.fit, tibble(InsectCount = c(2000)), type = "response")
```


Step 10) Compute probabilities on training data

```
glm.probs <- predict(glm.fit, type = "response")
```

Step 11) Append computed probabilities to a new data frame

```
FungInfectData.withProbs <- FungInfectData %>%
  mutate(probs = glm.probs, 
         pred = ifelse(probs > 0.5, 1, 0))
```

Step 12) Compute confusion matrix

```
FungInfectData.withProbs %>% select(FungInfect, pred) %>% table()
```

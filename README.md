# STA309-Final-Hera-Dashnyam
# Author: Hera Dashnyam
# Date: 2024-12-10

# --------------------------------------------------

# SETUP

# Knitr setup
knitr::opts_chunk$set(echo = TRUE)

# --------------------------------------------------

# PART 1: DATA HANDLING & MODELING

## Load the Data

# Required libraries
library(tidyverse)
library(caret)
library(randomForest)
library(rpart)
library(rpart.plot)
library(gridExtra)
library(ggplot2)

# The data
diabetes_data <- read_csv("diabetes_data.csv")

## Data Cleaning

# Categorical variables into factors
diabetes_data <- diabetes_data %>%
  mutate(
    gender = as.factor(gender),
    hypertension = as.factor(hypertension),
    heart_disease = as.factor(heart_disease),
    smoking_history = as.factor(smoking_history),
    diabetes = as.factor(diabetes)
  )

## Splitting the Data

# Seed
set.seed(123)

# Create training (80%) and test (20%) datasets
train_index <- createDataPartition(diabetes_data$diabetes, p = 0.8, list = FALSE)
train_data <- diabetes_data[train_index, ]
test_data <- diabetes_data[-train_index, ]

# --------------------------------------------------

# PART 2: MODELING

## Logistic Regression

# Fit logistic regression model
log_model <- glm(diabetes ~ ., data = train_data, family = binomial)

# Summary of the logistic regression
summary(log_model)

## Decision Tree

# Fit a decision tree
tree_model <- rpart(diabetes ~ ., data = train_data, method = "class")

# Plot the tree
rpart.plot(tree_model)

## Random Forest

# Fit a random forest
rf_model <- randomForest(diabetes ~ ., data = train_data, ntree = 100)

# Model performance
print(rf_model)

# --------------------------------------------------

# PART 3: EVALUATION

## Predictions

# Logistic Regression predictions
log_pred <- predict(log_model, test_data, type = "response")
log_class <- ifelse(log_pred > 0.5, "1", "0")

# Decision Tree predictions
tree_pred <- predict(tree_model, test_data, type = "class")

# Random Forest predictions
rf_pred <- predict(rf_model, test_data, type = "response")

## Confusion Matrices

# Confusion matrix for logistic regression
confusionMatrix(as.factor(log_class), test_data$diabetes)

# Confusion matrix for decision tree
confusionMatrix(tree_pred, test_data$diabetes)

# Confusion matrix for random forest
confusionMatrix(rf_pred, test_data$diabetes)

# --------------------------------------------------

# PART 4: VISUALIZATION

## Visualization: BMI Distribution

# Plot BMI distribution
ggplot(diabetes_data, aes(x = BMI)) +
  geom_histogram(binwidth = 1, fill = "blue", color = "black") +
  theme_minimal() +
  labs(title = "BMI Distribution", x = "BMI", y = "Frequency")

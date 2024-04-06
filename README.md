## An EDA of US Used Cars Datasets
### Exploring Trends and Factors Affecting Pricing of USA Used Cars (1990-2015)
### **Project Overview** 
This project aims to conduct an exploratory analysis of data on used cars in the USA from 1990 to 2015. The objective is to uncover insights into the trends of car prices over this timeframe and analyze the impact of factors such as car age, mileage, transmission type, and car colors on pricing. By examining a comprehensive dataset of used car listings, we seek to understand the underlying patterns and correlations contributing to pricing variations in the US automotive market during this period.

### **Data Sources and Description**
The dataset used in this project was sourced from Kaggel. The dataset contains historical car auction sales prices scraped from outside internet sources. According to the author, the dataset was collected in 2015 and will not be updated. The dataset contains information on used cars in the USA, including car make, model, year, mileage, transmission type, color, price, etc. The dataset has ~559k entries covering the period from 1990 to 2015.

### **Tools Used**
Python programming language and its data analysis libraries, including Pandas, NumPy, Matplotlib, Seaborn, and Plotly, were utilized for data manipulation, visualization, and analysis in this project. Jupyter Notebook was employed as the development environment for coding and documentation.

#### _Import Necessary Libraries_
```py
  # Import necessary libraries
  import numpy as np
  import pandas as pd
  import matplotlib.pyplot as plt
  import seaborn as sns
  import datetime
  import calendar
  import plotly.express as px
  import plotly.graph_objects as go
  from dash import Dash, dcc, html
  sns.set_theme(style='darkgrid', palette='pastel')
  %matplotlib inline
```


### **Data Cleaning and Preparation**
The dataset underwent thorough cleaning and preparation to ensure data quality and consistency. Tasks included handling missing values, removing duplicates, standardizing data formats, and encoding categorical variables. This process aimed to create a clean and structured dataset suitable for analysis.
#### _Read The Dataset and View it_
```py
  # import the car data
  df = pd.read_csv(r"C:\Users\Martin\Downloads\car_prices.csv", error_bad_lines=False, warn_bad_lines=True)
  df.head()
```
#### _Checked the data type & Summary Statistics_
```py
  # Check the data info i.e. shape & data type
  df.info()
  # Check the shape
  df.shape
```

#### _Dropped Null Values & Check the Summary Statistics_
```py
    #Removing all rows with instances of null values
    df.dropna(inplace=True)
    
    # Check the df for duplicate values
    df.duplicated().sum()
    
    # Check for statistical details of the df
    df.describe().round()
```

#### _Transform the dataframe: Extract Day, Month, and Year from Date Column_
```py
  # Convert saledate type to datetime
  df["saledate"] = pd.to_datetime(df["saledate"])
  
  # split day of week from from sale date
  df["day"] = df["saledate"].apply(lambda saledate: saledate.split()[0])
  
  # Use lambda and .apply to create new columns "Hour", "Month", and "Day of Week" through extracting them from the saledate
  df["sale month"] = df["saledate"].apply(lambda time: time.month)  # Extracts month from the saledate
  df["sale year"] = df["saledate"].apply(lambda time: time.year)  # Extracts day of week from the saledate
```

#### _Transform the Dataframe: Add, Remove Columns_
```py
  # Add a car age at sale time column
  df["age"] = df["sale year"]-df["year"]
  
  # Change the values of the State column to upper case
  df["state"] = df["state"].str.upper()
  
  # map the colors with numbers
  color_map ={'black': 1,'white': 2,'silver': 3,'gray': 4,'blue': 5,'red': 6,'—': 20,'gold': 7,'green': 8,'burgundy': 9,'beige': 10,
              'brown': 11,'orange': 12,'purple': 13,'off-white': 14,'yellow': 15,'charcoal': 16,'turquoise': 17,'pink': 18,'lime': 19}
  
  # Create Color Id column 
  df["color id"] = df["color"].replace(color_map)
  
  
  # drop the columns that might not be of use for now.
  df.drop(["Vin","Mmr", "Saledate","Seller"], axis=1, inplace=True)
```



### **Analysis**
Exploratory data analysis (EDA) techniques were employed to uncover insights from the dataset. This involved visualizing distributions, trends, and correlations between variables using statistical measures and visualization techniques such as histograms, scatter plots, heat maps, and correlation matrices.

#### _Now, we'll visually explore the dataset to find trends, patterns, and outliers_
**- Histogram of all numerical variables**
```py
  # create an object of all the numerical variables
  numerical_columns = df.select_dtypes(include=["int64", "float64"]).columns
  
  # Plot a histogram of each numerical feature
  plt.figure(figsize=(14, len(numerical_columns)*3))
  # Use the enumerate object which yields pairs containing a count (from start, which defaults to zero) and a value yielded by the iterable argument .enumerate is useful for obtaining an indexed list: (0, seq[0]),(1, seq[01]),(2, seq[2]),...
  for idx, feature in enumerate(numerical_columns, 1):
      plt.subplot(len(numerical_columns), 2, idx)
      sns.histplot(df[feature], kde=True)
      plt.title(f"{feature} | skewness: {round(df[feature].skew(), 2)} | Std Dev: {round(df[feature].std(), 2)}")
      plt.axvline(df[feature].mean(), 0,1, c="blue")
      plt.axvline(df[feature].median(), 0,1, c="magenta")
  # adjust layout and show plots
  plt.tight_layout()
  #plt.show
```

**  - Box plot of all numerical variables**
```py
  # Boxplot of each numeriacal feature
  plt.figure(figsize=(14, len(numerical_columns)*3))
  # Use the enumerate object which yields pairs containing a count (from start, which defaults to zero) and a value yielded by the iterable argument .enumerate is useful for obtaining an indexed list: (0, seq[0]),(1, seq[01]),(2, seq[2]),...
  for idx, feature in enumerate(numerical_columns, 1):
      plt.subplot(len(numerical_columns), 2, idx)
      sns.boxplot(df[feature])
      plt.title(f"{feature} | skewness: {round(df[feature].skew(), 2)} | Std Dev: {round(df[feature].std(), 2)}")
  
  # adjust layout and show plots
  plt.tight_layout()
  plt.show
```

**- Violin plot of all numerical variables**
```py
  # Violinplot of each numeriacal feature
  plt.figure(figsize=(14, len(numerical_columns)*3))
  # Use the enumerate object which yields pairs containing a count (from start, which defaults to zero) and a value yielded by the iterable argument .enumerate is useful for obtaining an indexed list: (0, seq[0]),(1, seq[01]),(2, seq[2]),...
  for idx, feature in enumerate(numerical_columns, 1):
      plt.subplot(len(numerical_columns), 2, idx)
      sns.violinplot(df[feature])
      plt.title(f"{feature} | skewness: {round(df[feature].skew(), 2)} | Mean: {round(df[feature].mean(), 2)}")
  
  # adjust layout and show plots
  plt.tight_layout()
  plt.show
```

**  - Pair plot of all numerical variables: shows a quick view of correlation among the numerical variables**
```py
  # Create a chart to show relationships between the various numerical columns
  plt.figure(figsize=(12,10))
  sns.pairplot(df)
```

**  - Display the correlation of the numerical variables by numbers**
```py
  # check the correlation of the numerical columns
  df.corr()
```

**  - Create a heat map of all numerical variables**
```py
  # create a heatmap of the numerical columns
  plt.figure(figsize=(8,6))
  sns.heatmap(df.corr(), annot=True, fmt=".2f", cmap="Pastel1", linewidths=1)
  plt.title("Correlation Heatmap")
```

**  - Violin plot of all numerical variables**
```py

```

**  - Violin plot of all numerical variables**
```py

```

**  - Violin plot of all numerical variables**
```py

```

**  - Violin plot of all numerical variables**
```py

```


### **Results and Finding**s
The analysis revealed several key findings regarding trends in car prices and factors influencing pricing. Notable insights include the impact of car age and mileage on pricing, differences in pricing based on transmission type, and potential correlations between car colors and prices.

### **Recommendations**
Based on the findings of the analysis, recommendations are provided for stakeholders in the automotive industry, including dealerships, manufacturers, and consumers. Suggestions may include adjusting pricing strategies, considering factors influencing pricing in inventory management, and understanding consumer preferences.

### **Limitations**
It's important to acknowledge the limitations of the analysis, such as potential biases in the dataset, limitations of the chosen analytical methods, and constraints of available data. These factors may impact the generalizability and reliability of the findings.

### References
[background and datasets from kaggle.com can be assessed here] --https://www.kaggle.com/datasets/tunguz/used-car-auction-prices?resource=download



















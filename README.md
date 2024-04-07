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
![df-head](https://github.com/Martinquetz/Python_Projects/assets/92187086/7eed09a5-f302-41f1-9df6-0edb125fa1ce)


#### _Checked the data type & Summary Statistics_
```py
  # Check the data info i.e. shape & data type
  df.info()
  # Check the shape
  df.shape
```
![df-info](https://github.com/Martinquetz/Python_Projects/assets/92187086/58862d42-408b-4335-9afd-69b56ce2e453)


#### _Dropped Null Values & Check the Summary Statistics_
```py
    #Removing all rows with instances of null values
    df.dropna(inplace=True)
    
    # Check the df for duplicate values
    df.duplicated().sum()
    
    # Check for statistical details of the df
    df.describe().round()
```
![df-summary-statistics](https://github.com/Martinquetz/Python_Projects/assets/92187086/f5496c10-e830-4371-8bdc-93b9572e7142)


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
 - Histogram of all numerical variables
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
![num-df-hist](https://github.com/Martinquetz/Python_Projects/assets/92187086/36d76f1b-dbb4-4fa7-85c6-c897a7443d34)


 - Box plot of all numerical variables
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
![num-df-boxplot](https://github.com/Martinquetz/Python_Projects/assets/92187086/bad61619-a5b0-448f-a49f-4e6168c9d8ae)


 - Violin plot of all numerical variables
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
![num-df-violin](https://github.com/Martinquetz/Python_Projects/assets/92187086/88f327b4-2aa5-4ac3-9b47-cd69c925af08)


 - Pair plot of all numerical variables: shows a quick view of correlation among the numerical variables
```py
  # Create a chart to show relationships between the various numerical columns
  plt.figure(figsize=(12,10))
  sns.pairplot(df)
```
![df-pairplot](https://github.com/Martinquetz/Python_Projects/assets/92187086/6c2a06cc-9f3a-4e79-a627-bd4e53cdf1c9)


 - Display the correlation of the numerical variables by numbers
```py
  # check the correlation of the numerical columns
  df.corr()
```
![df-corr](https://github.com/Martinquetz/Python_Projects/assets/92187086/387237f7-5ea0-451a-bb77-52a33f6cc8b2)


 - Create a heat map of all numerical variables
```py
  # create a heatmap of the numerical columns
  plt.figure(figsize=(8,6))
  sns.heatmap(df.corr(), annot=True, fmt=".2f", cmap="Pastel1", linewidths=1)
  plt.title("Correlation Heatmap")
```
![corr-heatmap](https://github.com/Martinquetz/Python_Projects/assets/92187086/3244e334-eaca-4b7f-9248-ed73d6cf7d64)

#### _Let's look closer into the relationship between the pairs of variables from our stated objectives_

 - Examining the relationship between Colour and Price.
```py
  # Create a jointplot of Colour Id vs Selling Price by car condition
  plt.figure(figsize=(7,5))
  sns.jointplot(data=df, y="Color Id", x="Price", palette="pastel1", height=7, kind="reg")
```
![color-vs-price-jointplot](https://github.com/Martinquetz/Python_Projects/assets/92187086/e26eef48-2c45-4ed8-b6f1-67d7b3556301)


 - Examining the relationship between Mileage and Price
```py
  # Create a jointplot of Odometer vs Selling Price by car condition
  plt.figure(figsize=(7,5))
  sns.jointplot(data=df, y="Odometer", x="Price", hue="Transmission", palette="husl", height=7)
```
![mileage-vs-price-jointplot](https://github.com/Martinquetz/Python_Projects/assets/92187086/3b930f1c-1de3-474f-a44b-d85b4e2ae361)


 - Box Plot Automatic Transmissions vs Manual
```py
  # Boxplot of Selling price by Transmission type
  plt.figure(figsize=(7,5))
  sns.boxplot(y="Price", x="Transmission", data=df, palette="Set3")
```
![price-by-transmission-boxplot](https://github.com/Martinquetz/Python_Projects/assets/92187086/7c8fc87a-1d22-411e-8e48-1eec8d56bd6a)


 - Regression plot of all Car Age vs Price by Transmission type
```py
  # Create subplots of Age and Selling Price by transmission type 
  g = sns.FacetGrid(df, col="Transmission", height=5,palette="husl")
  g.map_dataframe(sns.regplot, x="Age", y="Price")
  g.add_legend()
```
![price-vs-age-regplot](https://github.com/Martinquetz/Python_Projects/assets/92187086/8ac85003-6604-497b-8028-2b4124dcf9c5)


#### _Now, let's visually examine the categorical variables_

 - Visualizing the categorical variables
```py
  # Create an object of a list of the numerical columns
  categorical_columns = ['Transmission', 'State', 'Color','Interior', 'Sale Day']
  
  # plot distribution of each categorical column
  fig, axes = plt.subplots(nrows=3, ncols=2, figsize=(14, 13))
  axes = axes.ravel()
  # Use the enumerate object which yields pairs containing a count (from start, which defaults to zero) and a value yielded by the iterable argument .enumerate is useful for obtaining an indexed list: (0, seq[0]),(1, seq[01]),(2, seq[2]),...
  for idx, column in enumerate(categorical_columns):
      #plt.subplot(len(categorical_columns), 2, idx)
      g=sns.countplot(x=df[column], data=df, ax=axes[idx])
      for container in axes[idx].containers:
          axes[idx].bar_label(container, color='grey', size=10)
      g.set_xticklabels(g.get_xticklabels(), rotation=90)
      axes[idx].set_title(f'Count Plot of {column.title()}')
      axes[idx].set_xlabel(column.title())
      axes[idx].set_ylabel('Count')    
      #plt.title(f"{column} | skewness: {round(df[column].skew(), 2)}")
      #plt.axvline(df[column].mean(), 0,1, c="blue")
      #plt.axvline(df[column].median(), 0,1, c="magenta")
  # adjust layout and show plots
  plt.tight_layout()
  #plt.show
```
![cate-dfcountplot](https://github.com/Martinquetz/Python_Projects/assets/92187086/5fde4ecf-be24-4217-88fd-f539857bf9ba)


#### _We'll now check out popular brands, their average price, and price trends over time_
 - Chart of the top 30 car brands
```py
  # find the 30 most popular car Brand
  n = 30 # The top x number of cars to view
  popular = df["Make"].value_counts().head(n)
  plt.figure(figsize=(12,7))
  sns.barplot(x=popular.values, y=popular.index)
  #g.set_xticklabels(g.get_xticklabels(), rotation=90)  # this code helps set the xticklabel rotation without listing the x variables
  plt.title(f"Top {n} Popular Car Brands")
  plt.xlabel('Frequency')
  plt.ylabel('Car Make')
  plt.bar_label(container, color='grey', size=8)
  plt.tight_layout()
  #plt.xticks(rotation=90) :- Although this code sets the xticks at 90 degree, it however, lists all the x variables first before the chart
```
![top-popular](https://github.com/Martinquetz/Python_Projects/assets/92187086/0b59538a-6020-428f-86ff-b202f5e33c5d)


 - Chart of 20 Most Expensive Brands
```py
  # Show cars Makes average prices. Group df by car's year of manufactured
  dfg_make=df.groupby("Make")["Price"].mean().round().sort_values(ascending=False) # converts to series
  
  # Set variable to hold the top-n number to review
  n =20 # top n cars by price view
  #Plot the chart
  pricey = dfg_make.head(n)
  plt.figure(figsize=(12,9))
  sns.barplot(x=dfg_make.values, y=dfg_make.index)
  plt.title(f"Top {n} Pricey Car Brands")
  plt.xlabel('Average Price ($)')
  plt.ylabel('Car Make')
  plt.bar_label(container, color='black', size=8)
  plt.tight_layout()
```
![top-20-pricey](https://github.com/Martinquetz/Python_Projects/assets/92187086/d0828032-8778-4602-9efc-887ab41eb99f)


 - Trend of Car Prices Over Time
```py
  # Show how car prices have trended over the years. Group df by car's year of manufactured
  dfg=df.groupby("Car Year")["Price"].mean().round() # converts to series
  
  # plot the line chart
  plt.figure(figsize=(10,6))
  sns.lineplot(x=dfg.index, y=dfg.values)
  plt.title("Historic Yearly Average Car Sale Price")
  plt.ylabel('Avg. Car Prices ($)')
```
![price-trend](https://github.com/Martinquetz/Python_Projects/assets/92187086/d76283af-1b82-4e9c-b698-a05f907784c3)


 - Checking the Distribution of Prices by Transmission Type
```py
  # Create an histogram of the selling Price
  plt.figure(figsize=(10,6))
  sns.histplot(data=df,x="Price",hue="Transmission", kde=True,bins=60)
  plt.axvline(df["Price"].mean(),0,1,c="blue" )
  plt.axvline(df["Price"].median(),0,1,c="magenta" )
  plt.title(f"{'Price'} | Skewness: {round(df['Price'].skew(), 2)}")
```
![price-hist-by-transmission](https://github.com/Martinquetz/Python_Projects/assets/92187086/9c82f38e-fe23-4604-9a16-601c7ac76d2d)

There seems to be an entry error in one or more car prices. One or more prices are significantly off. Let's dig into this.

 - We'll identify car prices Over $170K
```py
# The boxplot shows several outliers, but two huge ones stand out. Let's identify them.
  # Find the cars with prices more than $170K
  df[df["Price"]>=170000].head()
```
![df-price-outlier](https://github.com/Martinquetz/Python_Projects/assets/92187086/7b6da011-cbb1-44af-9064-6d0c44875aee)


It is unlikely a Ford car will be priced at $230K. Let's find out the price range for this Ford brand of same model
 
```py
  # Find the Ford Escape model prices with same year and trim
  df[(df["Model"]=="Escape") & (df["Trim"]=="Titanium") & (df["Car Year"]==2014)]
```
![ford -brand-prices](https://github.com/Martinquetz/Python_Projects/assets/92187086/c448ae99-f1a5-4319-9c16-246ae54ca37b)


We can notice that this Ford model sold for between $20K and $25K, so this car may have been priced at $23K and not $230K. There, I'll update that entry accordingly.

```py
  # It is clear that the 2014 Ford Titanium $230000 entry was an entry error. We'll correct it to 23K, which is about its price level.
  df.loc[344905, "Price"]= 23000 # Replace the 230K value with 23K using the replace by index
  # Find the cars with prices more than $170K to check if it's been corrected
  df[df["Price"]>=170000].head()
```

#### _Next, we'll categorize the cars into two buckets, i.e., "Standard" and "Luxury"_

```py
  # Use lambda and .apply with an If statement to create a new column to categorize the cars into Luxury or standard cars
  df['Type'] = df['Price'].apply(lambda x: 'Luxury' if x >= 80000 else 'Standard')
```

- Visualizing the histogram of standard cars
```py
  # Histogram of Standard cars.
  # Create subplots histogram of the Selling Price by transmission type 
  g = sns.FacetGrid(df[df["Type"]=="Standard"] , col="Transmission", height=5,palette="husl")
  g.map_dataframe(sns.histplot, x="Price", kde=True, bins=32)
  g.add_legend()
```
![std-car-prices](https://github.com/Martinquetz/Python_Projects/assets/92187086/224512a7-154e-4c2a-a058-5cdc13a29dab)


- Visualizing the Violin plot of standard cars for a closer look at the distribution
```py
  #Violinplot of Standard cars.
  # Create Violinplots of the Selling Price by transmission type 
  print("Violinplot of Standard cars by Transmission Type")
  g = sns.FacetGrid(df[df["Type"]=="Standard"] , col="Transmission", height=5,palette="husl")
  g.map_dataframe(sns.violinplot, x="Price")
  g.add_legend()
```
![std-cars-violinplot](https://github.com/Martinquetz/Python_Projects/assets/92187086/08958785-a129-49c9-a7ee-2aaaed3a26ed)


- Visualizing the histogram of Luxury cars
```py
  # Histogram of luxurious cars.
  # Create subplots histogram of the Selling Price by transmission type 
  g = sns.FacetGrid(df[df["Type"]=="Luxury"] , col="Transmission", height=5, palette="husl")
  g.map_dataframe(sns.histplot, x="Price", kde=True, bins=32)
  g.add_legend()
```
![lux-cars-prices](https://github.com/Martinquetz/Python_Projects/assets/92187086/fe1d5c40-db66-4951-a358-3f6ba54c8622)


- Visualizing the Violin plot of Luxury cars for a closer look at the distribution
```py
  # Violinplot of Luxury cars.
  # Create violinplots of the Selling Price by transmission type 
  print("Violinplot of Luxury cars by Transmission Type")
  g = sns.FacetGrid(df[df["Type"]=="Luxury"] , col="Transmission", height=5,palette="husl")
  g.map_dataframe(sns.violinplot, x="Price")
  g.add_legend()
```
![lux-prices-violinplot](https://github.com/Martinquetz/Python_Projects/assets/92187086/4ab6fec9-2378-4ec3-b3be-07b688fa32e7)


 - An Interactive bar chart to check the trends of various variables over time
```py
  fig = px.bar(data_frame=dfg)
  fig_widget = go.FigureWidget(fig)
  fig_widget
```
![price-plotly](https://github.com/Martinquetz/Python_Projects/assets/92187086/9c33d197-691a-40ef-9d33-5e0e6f597c81)


### **Results and Findings**
The analysis revealed several key findings regarding trends in car prices and factors influencing pricing. Notable insights include the impact of car age and mileage on pricing, differences in pricing based on transmission type, and potential correlations between car colors and prices. The findings are summarized as follows:
 - The cars dataset has ~559K entries; some records had null values. I decided to drop rows with null values since there is still a considerable dataset size.
 - The cars' prices, on average, have grown significantly over the years, with automatic transition cars being not just the most popular but higher in price than their manual counterpart. Ford, Chevrolet, Nissan, Toyota, and Dodge are the top five popular cars in the US for the years in review.
 - The cars' year of manufacture and condition positively correlate with price, i.e., car prices tend to increase as the cars' condition and recency increase. Conversely, car prices tend to decrease with an increase in age and mileage.
 - The analysis showed an insignificant relationship between car colors and price. However, colors gray, blue, and red are slightly higher in price than other car colors.

### **Recommendations**
Since this project has a limited scope, more analysis will be needed to allow stakeholders to price their cars appropriately. However, adjusting pricing strategies based on factors such as the aforementioned ones, body type, location, dealership type, market segmentation, etc., is highly recommended for considering factors influencing pricing in inventory management and understanding consumer preferences.

### **Limitations**
It's important to acknowledge the limitations of the analysis, such as potential biases in the dataset, limitations of the chosen analytical methods, and constraints of available data. These factors may impact the generalizability and reliability of the findings.

### References
[background and datasets from kaggle.com can be assessed here] --https://www.kaggle.com/datasets/tunguz/used-car-auction-prices?resource=download



















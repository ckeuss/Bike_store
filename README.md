# Bike store
## SQL and Python
### Project Overview
In this data analysis project a data base and queries are first created with SQL and then visualised using Python. 
It aims to provide insight into the range of products, customer behavior and business of the bike store for the period 2016 to 2018.

It can answer key questions such as:
- What type of bike is most popular?
- Which bike category makes the most revenue?
- Which store processes the most orders?

### Data Sources
The retail bike store data is originally from a sample database from sqlservertutorial.net. 
I found and downloaded the csv files for the different tables on Kaggle.
[more information](https://www.kaggle.com/datasets/dillonmyrick/bike-store-sample-database)

### Tools
- Python, incl. mysql.connector 
- SQL
  
### References
I used the following article by Craig Dickson to learn about connecting python to SQL and building a database.  
https://www.freecodecamp.org/news/connect-python-with-sql/

### Data Analysis
Some interesting code worked with:

```
with open("./csv/categories.csv", newline= "") as csvfile:
    reader = csv.reader(csvfile)
    next(reader)  #Skip the header
    
    for row in reader:
        value1 = row[0]
        value2 = row[1]
        
        #SQL INSERT statement
        query = f"INSERT IGNORE INTO categories(category_id, category_name) VALUES ('{value1}', '{value2}')"
        execute_query(connection, query)
```
        
```
q4 ="""
WITH sales_per_category AS (
    SELECT
        oi.order_id,
        oi.quantity,
        oi.list_price,
        (oi.quantity * oi.list_price) * (1 - oi.discount) AS revenue,
        p.category_id,
        ca.category_name
    FROM order_items oi
    INNER JOIN products p ON oi.product_id = p.product_id
    INNER JOIN categories ca ON p.category_id = ca.category_id
)
SELECT
    category_id,
    category_name,
    SUM(revenue) AS total_revenue,
    SUM(quantity) AS sold_items
FROM 
    sales_per_category
GROUP BY
    category_id
ORDER BY
    category_id;

    """

results = read_query(connection, q4)

#Returns a list of lists and then creates a pandas DataFrame
result_list = []

for result in results:
  result = list(result)
  result_list.append(result)


columns = ["Category_id", "Category", "Total_revenue", "Sold_items"]
df4 = pd.DataFrame(result_list, columns=columns)

df4
```
```
#barplot
plt.bar(df4["Category"], df4["Sold_items"], color = colors)

#number on top of bars
for i, items in enumerate(df4["Sold_items"]):
    plt.text(i, items - 30, str(items), ha= "center", va="top")

#title and labels
plt.xlabel("Category")
plt.ylabel("Sold items")
plt.title("Sold items per category")
plt.xticks(rotation=45, ha="right")  # Rotate x-axis labels
plt.tight_layout()
plt.show()
```

### Insights
The analysed bike store sells seven different types of bikes, the most products fall into the categories _Mountain Bikes_, _Cruisers Bicycles_ and _Road Bikes_, which are also the most sold items. Other categories are _Children Bicycles_, _Electric Bikes_, _Comfort Bicycles_ and _Cyclocross Bicycles_. 

![image](https://github.com/ckeuss/Bike_store/assets/147528104/3dd3d6fe-87e5-47b0-9426-70ed989cfff6)

March and April seem to be the months with the highest number of orders and the majority of the orders were processed by Baldwin Bikes in NY (Store 2), one of the three branches. 

![image](https://github.com/ckeuss/Bike_store/assets/147528104/0f9267f5-686f-4a01-917d-0f7cd2d54787)


The most popular bikes in 2016 to 2018 were _Mountain Bikes_ and _Cruisers Bicycles_. The by far highest total revenue was made with _Mountain Bikes_ with around 2.71 million dollar, although bikes of this category have only the fourth highest average list price. The most expensive bikes are on average _Electric Bikes_ and _Road Bikes_. 

![image](https://github.com/ckeuss/Bike_store/assets/147528104/e7025d5c-fc44-4b3a-9ee2-b3db434f3cb5)


From the revenue development of the three most profitable bike categories _Mountain Bikes_, _Road Bikes_ and _Electric Bikes_ from 2016 to 2018 it can be concluded that the revenue of _Electric Bikes_ is rsing while the revenue of _Mountain Bikes_ and _Road Bikes_ is on a decline, most severely concerning the _Mountain Bikes_ with a decline of around 2/3 of the original revenue in 2016. The decline could be due to many different factors and could be analyzed further by looking at the customers needs, marketing funnels, customer service, customer loyalty, price development and competition.

![image](https://github.com/ckeuss/Bike_store/assets/147528104/6b4467d6-d8c9-4636-9f20-82c84a68ce4e)

In terms of punctual deliveries the analysis shows that most deliveries arrive on time, however in 2016 to 2018 around one third of the orders in the months January to March, July, October and November were delivered after the required date.









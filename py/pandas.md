



# Pandas

```py
import pandas as pd
```

## creating data

```py
pd.Series([1, 2, 3], name="numbers")

pd.DataFrame({
    "name": ["Ali", "Sara", "Reza"],
    "age": [25, 30, 22],
    "city": ["Tehran", "Shiraz", "Tabriz"]
})

```


### Reading / Writing Data

```py
pd.read_csv("file.csv")          # Read CSV
pd.read_excel("file.xlsx")       # Read Excel
pd.read_json("file.json")        # Read JSON
pd.read_sql(query, conn)         # Read SQL

df.to_csv("out.csv", index=False)
df.to_excel("out.xlsx", index=False)
df.to_json("out.json")
```


### Inspecting Data

```py
df.head()                        # First 5 rows
df.tail(3)                       # Last 3 rows
df.info()                        # Summary
df.describe()                    # Stats
df.shape                         # Rows, cols
df.columns                       # Column names
df.dtypes                        # Column types
```



### Selecting Data

```py
df["name"]                       # Column (Series)
df[["name", "age"]]              # Multiple columns
df.iloc[0]                       # First row
df.iloc[0:3]                     # Rows by position
df.loc[0, "name"]                # By label
df.loc[:, "age"]                 # All rows, age column
df.at[0, "name"]                 # Fast scalar access
```



### Filtering

```py
df[df["age"] > 25]
df[(df["age"] > 25) & (df["city"] == "Tehran")]
df.query("age > 25 and city == 'Tehran'")
```

### Adding & Modifying

```py
df["new_col"] = df["age"] * 2
df.rename(columns={"age": "years"}, inplace=True)
df.drop(columns=["new_col"], inplace=True)
```

### Sorting

```py
df.sort_values("age")              # Ascending
df.sort_values("age", ascending=False)  
df.sort_values(["city", "age"])    # Multi-column
```


### Grouping & Aggregation

```py
df.groupby("city")["age"].mean()
df.groupby("city").agg({"age": ["mean", "max"], "name": "count"})
df["age"].value_counts()
```


### Missing Data

```py
df.isnull().sum()
df.dropna()                        # Drop missing
df.fillna(0)                       # Fill with value
df.fillna(df["age"].mean())        # Fill with mean
```


### Merging & Joining

```py
pd.concat([df1, df2])                     # Stack vertically
pd.concat([df1, df2], axis=1)             # Side by side
pd.merge(df1, df2, on="id")               # Inner join
pd.merge(df1, df2, on="id", how="left")   # Left join
```

### Useful Operations


```py
df["age"].unique()                 # Unique values
df["age"].nunique()                # Count unique
df["age"].apply(lambda x: x * 2)   # Apply function
df.applymap(str)                   # Apply on all cells
df["age"].between(20, 30)          # Range filter
```

### Date/Time

```py
df["date"] = pd.to_datetime(df["date"])
df["year"] = df["date"].dt.year
df["month"] = df["date"].dt.month
df["weekday"] = df["date"].dt.day_name()
```


### Export to Other Formats


```py
df.to_clipboard(index=False)       # Copy to clipboard
df.to_sql("table", conn, if_exists="replace")
```











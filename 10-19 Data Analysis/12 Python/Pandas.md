# Pandas Cheat Sheet

## 1. Importing & Setup
```python
import pandas as pd
import numpy as np
```
## 2. Creating Data
```python
# From dictionary
df = pd.DataFrame({
    'A': [1, 2, 3],
    'B': ['x', 'y', 'z']
})

# From list of lists
df = pd.DataFrame([[1, 2], [3, 4]], columns=['A', 'B'])

# From NumPy array
arr = np.array([[1, 2], [3, 4]])
df = pd.DataFrame(arr, columns=['A', 'B'])

# From CSV / Excel / JSON / SQL
df = pd.read_csv("file.csv")
df = pd.read_excel("file.xlsx", sheet_name="Sheet1")
df = pd.read_json("file.json")
df = pd.read_sql("SELECT * FROM table", conn)

# Save to file
df.to_csv("out.csv", index=False)
df.to_excel("out.xlsx", index=False)
```
## 3. Inspecting Data
```python
df.head()        # First 5 rows
df.tail(3)       # Last 3 rows
df.info()        # DataFrame summary
df.shape         # (rows, columns)
df.columns       # Column names
df.index         # Index info
df.dtypes        # Data types
df.describe()    # Summary statistics
```
## 4. Selecting Data
```python
# Single column
df['A']
df.A

# Multiple columns
df[['A', 'B']]

# By index (rows)
df.iloc[0]         # First row
df.iloc[0:3]       # First 3 rows

# By label
df.loc[0, 'A']     # Single value
df.loc[0:2, ['A','B']]  # Rows 0–2, columns A & B

# Conditional selection
df[df['A'] > 2]
df[(df['A'] > 2) & (df['B'] == 'x')]
```
## 5. Modifying Data
```python
# Add new column
df['C'] = df['A'] + df['B'].astype(str)

# Update values
df.loc[0, 'A'] = 100

# Drop columns/rows
df.drop('C', axis=1, inplace=True)    # Drop column
df.drop(0, axis=0, inplace=True)      # Drop row

# Rename columns
df.rename(columns={'A': 'Alpha'}, inplace=True)

# Replace values
df['B'].replace({'x': 'X'}, inplace=True)
```
## 6. Handling Missing Data
```python
df.isna().sum()              # Count NaNs
df.dropna()                  # Drop rows with NaN
df.fillna(0)                 # Fill NaN with 0
df.fillna(df.mean(), inplace=True)  # Fill with mean
```
## 7. Aggregations & Grouping
```python
df['A'].sum()
df['A'].mean()
df['A'].value_counts()

# Group by
df.groupby('B')['A'].mean()
df.groupby('B').agg({'A': ['mean', 'sum']})
```
## 8. Sorting & Ranking
```python
df.sort_values('A')
df.sort_values(['B','A'], ascending=[True, False])
df.sort_index()

df['A'].rank()
```
## 9. Merging & Joining
```python
# Concatenate
pd.concat([df1, df2], axis=0)

# Merge on key
pd.merge(df1, df2, on='id')

# Join by index
df1.join(df2, lsuffix='_L', rsuffix='_R')
```
## 10. Working with Dates
```python
df['date'] = pd.to_datetime(df['date'])
df['year'] = df['date'].dt.year
df['month'] = df['date'].dt.month
df['weekday'] = df['date'].dt.day_name()
```
## 11. Useful Tricks
```python
df['A'].unique()         # Unique values
df['A'].nunique()        # Count unique
df.sample(5)             # Random sample
df.corr()                # Correlation matrix
df.apply(np.sqrt)        # Apply function
df['A'].apply(lambda x: x**2)
```
## 12. Pivot Tables
```python
pd.pivot_table(df, values='A', index='B', aggfunc='mean')
```
## 13. Reshaping Data
```python
# Wide → Long
df.melt(id_vars=['id'], value_vars=['A','B'])

# Long → Wide
df.pivot(index='id', columns='variable', values='value')

# Stack / Unstack
df.stack()
df.unstack()
```
## 14. String Operations
```python
df['B'].str.upper()
df['B'].str.contains('x')
df['B'].str.replace('x', 'X')
```
## 15. Exporting Data
```python
df.to_csv("out.csv", index=False)
df.to_excel("out.xlsx", sheet_name="Sheet1")
df.to_json("out.json")
df.to_sql("table", conn, if_exists="replace")
```
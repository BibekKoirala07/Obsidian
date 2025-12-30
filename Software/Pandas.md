## 🟢 **Beginner → Intermediate**

If you're not already here, get comfortable with:

### 🔹 Core Concepts

- `Series` vs `DataFrame`
    
- Indexing and slicing: `.loc[]`, `.iloc[]`
    
- Filtering rows/columns
    
- Sorting: `.sort_values()`, `.sort_index()`
    
- Missing data: `.isna()`, `.fillna()`, `.dropna()`
    

### 🔹 Data I/O

- Read/write CSV, Excel, JSON, Parquet, SQL:
    
    python
    
    CopyEdit
    
    `df = pd.read_csv("file.csv") df.to_parquet("out.parquet")`
    

### 🔹 Data Cleaning

- Rename columns: `.rename()`
    
- Remove/replace bad data: `.replace()`, `.map()`
    
- String operations: `.str.contains()`, `.str.strip()`
    
- Datetime parsing: `pd.to_datetime()`, `.dt` accessor
    

---

## 🟡 **Intermediate → Advanced**

### 🔹 Grouping & Aggregation

- `.groupby()` + `.agg()`
    
- Multi-level aggregations:
    
    python
    
    CopyEdit
    
    `df.groupby("category").agg({"value": ["mean", "sum"]})`
    

### 🔹 Pivoting & Reshaping

- `.pivot()`, `.pivot_table()`
    
- `.melt()`, `.stack()`, `.unstack()`
    

### 🔹 Apply & Vectorization

- `.apply()` vs `.map()` vs `.applymap()`
    
- Custom functions in `groupby().apply()`
    
- Avoiding `for` loops → vectorize!
    

### 🔹 Merge, Join, and Concatenation

- `pd.merge()` (SQL-like joins)
    
- `pd.concat()` (combine across axes)
    
- `merge_asof()` for time-series joins
    

---

## 🔵 **Advanced Topics (Pro Level)**

### 🔹 Performance Optimization

- Use `.query()` for fast filtering
    
- Use `Categorical` types for memory efficiency
    
- Use `numba` or `cython` to accelerate slow operations
    
- Profile with `%timeit`, `df.info(memory_usage="deep")`, etc.
    

### 🔹 Time Series

- `resample()`, rolling windows
    
- Time-aware indexing and slicing
    
- Lag features and shifting
    

### 🔹 MultiIndex (Hierarchical Indexing)

- `.set_index()`, `.reset_index()`
    
- Accessing with `.xs()`, `.loc[()]`
    
- Working with grouped data with multiple levels
    

### 🔹 Custom DataFrame Manipulation

- Complex reshaping
    
- Using `.pipe()` for clean method chaining
    
- Designing custom transformation pipelines
    

---

## 🧠 Mental Models to Build

|Concept|Mental Shift|
|---|---|
|Loops → Vectorization|Think in terms of **entire columns**|
|SQL → Pandas|Translate joins, filters, groupings|
|Excel → Pandas|Replace VLOOKUP, PivotTables, filters|
|Pipelines|Chain methods cleanly → `.pipe()`|

---

## 📚 Learning Resources

### Books:

- **Python for Data Analysis** by Wes McKinney (creator of Pandas)
    
- **Effective Pandas** by Matt Harrison
    

### Courses:

- **DataCamp Pandas Tracks**
    
- **Kaggle's Pandas Course** (free, hands-on)
    
- **Fast.ai Data Wrangling**
    

### Practice:

- **Kaggle Datasets**: https://www.kaggle.com/datasets
    
- **100 Pandas Exercises**: [https://github.com/guipsamora/pandas_exercises](https://github.com/guipsamora/pandas_exercises)
    
- **Leetcode SQL → Pandas translations** (if you know SQL)
    

---

## 🧩 Challenge Yourself

Try these to test your skills:

- Can you reshape messy survey data into a usable table?
    
- Can you write a chain of methods with `.pipe()` to clean a dataset in 1 line?
    
- Can you group by two variables and calculate conditional stats?
    
- Can you process 10 million rows efficiently?
    

---

## 🛠️ Tools Pros Use

- **JupyterLab** or **VS Code + Python extension**
    
- `%timeit` / `%prun` for profiling
    
- `df.style` for display
    
- `pandas-profiling` for EDA
    
- **Polars** or **Dask** if you want to scale beyond Pandas
### Series
### Data Frames
It is a 2 dimensional data structure,


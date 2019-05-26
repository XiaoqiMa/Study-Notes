## Table of Contents

[TOC]

### tools_pandas

- handling time

```python
# Let's start by creating a time series using pd.date_range(). This returns a DatetimeIndex containing one datetime per hour for 12 hours starting on October 29th 2016 at 5:30pm.
dates = pd.date_range('2016/10/29 5:30pm', periods=12, freq='H')
# Periods: quarters = pd.period_range('2016Q1', periods=8, freq='Q')

# Upsampling and interpolation
temp_series_freq_15min = temp_series.resample("15Min").mean()
temp_series_freq_15min.head(n=10) # `head` displays the top n values

temp_series_freq_15min = temp_series.resample("15Min").interpolate(method="cubic")
temp_series_freq_15min.head(n=10)

#timezones
temp_series_ny = temp_series.tz_localize("America/New_York")
temp_series_paris = temp_series_ny.tz_convert("Europe/Paris")
#Fortunately using the ambiguous argument we can tell pandas to infer the right DST (Daylight Saving Time) based on the order of the ambiguous timestamps:
temp_series_paris_naive.tz_localize("Europe/Paris", ambiguous="infer")
```

- DataFrame objects

```python
# Stacking and unstacking levels
# Calling the stack() method will push the lowest column level after the lowest index. Calling unstack() will do the reverse, once again creating many NaN values.

# The loc attribute lets you access rows instead of columns. The result is a Series object in which the DataFrame's column names are mapped to row index labels. You can also access rows by integer location using the iloc attribute.

# When adding a new column, it is added at the end (on the right) by default. You can also insert a column anywhere else using the insert() method:
people.insert(1, "height", [172, 181, 185])

# You can also create new columns by calling the assign() method. Note that this returns a new DataFrame object, the original is not modified:
people.assign(
    body_mass_index = people["weight"] / (people["height"] / 100) ** 2,
    has_pets = people["pets"] > 0
)
# But fear not, there is a simple solution. You can pass a function to the assign() method (typically a lambda function), and this function will be called with the DataFrame as a parameter:
(people
     .assign(body_mass_index = lambda df: df["weight"] / (df["height"] / 100) ** 2)
     .assign(overweight = lambda df: df["body_mass_index"] > 25)
)

# Evaluating an expression
people.eval("body_mass_index = weight / (height/100) ** 2", inplace=True)

# You can use a local or global variable in an expression by prefixing it with '@':
overweight_threshold = 30
people.eval("overweight = body_mass_index > @overweight_threshold", inplace=True)

# Note that sort_index returned a sorted copy of the DataFrame. To modify people directly, we can set the inplace argument to True. Also, we can sort the columns instead of the rows by setting axis=1:
people.sort_index(axis=1, inplace=True)

# Pandas supports spreadsheet-like pivot tables that allow quick data summarization. To illustrate this, let's create a simple DataFrame:
pd.pivot_table(more_grades, index="name")
# We can change the aggregation function by setting the aggfunc argument, and we can also specify the list of columns whose values will be aggregated:
pd.pivot_table(more_grades, index="name", values=["grade","bonus"], aggfunc=np.max)

# Of course LEFT OUTER JOIN is also available by setting how="left": only the cities present in the left DataFrame end up in the result. Similarly, with how="right" only cities in the right DataFrame appear in the result. For example:
pd.merge(left=city_loc, right=city_pop, on="city", how="right")

# Rather than joining DataFrames, we may just want to concatenate them. That's what concat() is for. Note that this operation aligned the data horizontally (by columns) but not vertically (by rows). In this example, we end up with multiple rows having the same index (eg. 3)
pd.concat([city_loc, city_pop], ignore_index=True)

```



### tool_matplotlib

- line style and color

```python
# In a Jupyter notebook, we can simply output the graphs within the notebook itself by running the %matplotlib inline magic command.

# You can plot multiple lines on one graph very simply: just pass x1, y1, [style1], x2, y2, [style2], ...
plt.plot([0, 100, 100, 0, 0], [0, 0, 100, 100, 0], "r-", [0, 100, 50, 0, 100], [0, 100, 130, 100, 0], "g--")
plt.axis([-10, 110, -10, 140])
plt.show()
```

- Pylab *vs* Pyplot *vs* Matplotlib

There is some confusion around the relationship between pylab, pyplot and matplotlib. It's simple: matplotlib is the full library, it contains everything including pylab and pyplot.

Pyplot provides a number of tools to plot graphs, including the state-machine interface to the underlying object-oriented plotting library.

Pylab is a convenience module that imports matplotlib.pyplot and NumPy in a single name space. You will find many examples using pylab, but it is no longer recommended (because *explicit* imports are better than *implicit* ones).

- drawing text 

```python
x = np.linspace(-1.5, 1.5, 30)
px = 0.8
py = px**2

plt.plot(x, x**2, "b-", px, py, "ro")

plt.text(0, 1.5, "Square function\n$y = x^2$", fontsize=20, color='blue', horizontalalignment="center")
plt.text(px - 0.08, py, "Beautiful point", ha="right", weight="heavy")
plt.text(px, py, "x = %0.2f\ny = %0.2f"%(px, py), rotation=50, color='gray')

plt.show()

# It is quite frequent to annotate elements of a graph, such as the beautiful point above. The annotate function makes this easy: just indicate the location of the point of interest, and the position of the text, plus optionally some extra attributes for the text and the arrow.
plt.annotate("Beautiful point", xy=(px, py), xytext=(px-1.3,py+0.5),
                           color="green", weight="heavy", fontsize=14,
                           arrowprops={"facecolor": "lightgreen"})
# You can also add a bounding box around your text by using the bbox attribute:
plt.plot(x, x**2, px, py, "ro")

bbox_props = dict(boxstyle="rarrow,pad=0.3", ec="b", lw=2, fc="lightblue")
plt.text(px-0.2, py, "Beautiful point", bbox=bbox_props, ha="right")

bbox_props = dict(boxstyle="round4,pad=1,rounding_size=0.2", ec="black", fc="#EEEEFF", lw=5)
plt.text(0, 1.5, "Square function\n$y = x^2$", fontsize=20, color='black', ha="center", bbox=bbox_props)

plt.show()

# The simplest way to add a legend is to set a label on all lines, then just call the legend function.
plt.legend(loc="best")
```

- lines

```python
# You can draw lines simply using the plot function, as we have done so far. However, it is often convenient to create a utility function that plots a (seemingly) infinite line across the graph, given a slope and an intercept. You can also use the hlines and vlines functions that plot horizontal and vertical line segments. For example:
from numpy.random import randn

def plot_line(axis, slope, intercept, **kargs):
    xmin, xmax = axis.get_xlim()
    plt.plot([xmin, xmax], [xmin*slope+intercept, xmax*slope+intercept], **kargs)

x = randn(1000)
y = 0.5*x + 5 + randn(1000)*2
plt.axis([-2.5, 2.5, -5, 15])
plt.scatter(x, y, alpha=0.2)
plt.plot(1, 0, "ro")
plt.vlines(1, -5, 0, color="red")
plt.hlines(0, -2.5, 1, color="red")
plot_line(axis=plt.gca(), slope=0.5, intercept=5, color="magenta")
plt.grid(True)
plt.show()
```

- animations

```python
%matplotlib nbagg
import matplotlib.animation as animation
import matplotlib.pyplot as plt

x = np.linspace(-1, 1, 100)
y = np.sin(x**2*25)
data = np.array([x, y])

fig = plt.figure()
line, = plt.plot([], [], "r-") # start with an empty plot
plt.axis([-1.1, 1.1, -1.1, 1.1])
plt.plot([-0.5, 0.5], [0, 0], "b-", [0, 0], [-0.5, 0.5], "b-", 0, 0, "ro")
plt.grid(True)
plt.title("Marvelous animation")

# this function will be called at every iteration
def update_line(num, data, line):
    line.set_data(data[..., :num] + np.random.rand(2, num) / 25)  # we only plot the first `num` data points.
    return line,

line_ani = animation.FuncAnimation(fig, update_line, frames=100, fargs=(data, line), interval=67)
plt.show()

# Matplotlib relies on 3rd-party libraries to write videos such as FFMPEG or mencoder. In this example we will be using FFMPEG so be sure to install it first
Writer = animation.writers['ffmpeg']
writer = Writer(fps=15, metadata=dict(artist='Me'), bitrate=1800)
line_ani.save('my_wiggly_animation.mp4', writer=writer)
```



### 01_the_machine_learning_landscape

- save images

  ```python
  # Where to save the figures
  PROJECT_ROOT_DIR = "."
  CHAPTER_ID = "fundamentals"
  
  def save_fig(fig_id, tight_layout=True):
      path = os.path.join(PROJECT_ROOT_DIR, "images", CHAPTER_ID, fig_id + ".png")
      print("Saving figure", fig_id)
      if tight_layout:
          plt.tight_layout()
      plt.savefig(path, format='png', dpi=300)
  ```

- prepare data using pandas (code example)

```python
def prepare_country_stats(oecd_bli, gdp_per_capita):
    oecd_bli = oecd_bli[oecd_bli["INEQUALITY"]=="TOT"]
    oecd_bli = oecd_bli.pivot(index="Country", columns="Indicator", values="Value")
    gdp_per_capita.rename(columns={"2015": "GDP per capita"}, inplace=True)
    gdp_per_capita.set_index("Country", inplace=True)
    full_country_stats = pd.merge(left=oecd_bli, right=gdp_per_capita,
                                  left_index=True, right_index=True)
    full_country_stats.sort_values(by="GDP per capita", inplace=True)
    remove_indices = [0, 1, 6, 8, 33, 34, 35]
    keep_indices = list(set(range(36)) - set(remove_indices))
    return full_country_stats[["GDP per capita", 'Life satisfaction']].iloc[keep_indices]
```

- plot annotation

```python
position_text2 = {
    "Brazil": (1000, 9.0),
    "Mexico": (11000, 9.0),
    "Chile": (25000, 9.0),
    "Czech Republic": (35000, 9.0),
    "Norway": (60000, 3),
    "Switzerland": (72000, 3.0),
    "Luxembourg": (90000, 3.0),
}
for country, pos_text in position_text2.items():
    pos_data_x, pos_data_y = missing_data.loc[country]
    plt.annotate(country, xy=(pos_data_x, pos_data_y), xytext=pos_text,
            arrowprops=dict(facecolor='black', width=0.5, shrink=0.1, headwidth=5))
    plt.plot(pos_data_x, pos_data_y, "rs")
```

- Pipeline regression model

```python
from sklearn import preprocessing
from sklearn import pipeline

poly = preprocessing.PolynomialFeatures(degree=60, include_bias=False)
scaler = preprocessing.StandardScaler()
lin_reg2 = linear_model.LinearRegression()

pipeline_reg = pipeline.Pipeline([('poly', poly), ('scal', scaler), ('lin', lin_reg2)])
pipeline_reg.fit(Xfull, yfull)
curve = pipeline_reg.predict(X[:, np.newaxis])
plt.plot(X, curve)
save_fig('overfitting_model_plot')
plt.show()
```



### 02_end_to_end_machine_learning_project

- train test split

```python
# For illustration only. Sklearn has train_test_split()
def split_train_test(data, test_ratio):
    shuffled_indices = np.random.permutation(len(data))
    test_set_size = int(len(data) * test_ratio)
    test_indices = shuffled_indices[:test_set_size]
    train_indices = shuffled_indices[test_set_size:]
    return data.iloc[train_indices], data.iloc[test_indices]

def test_set_check(identifier, test_ratio):
    return crc32(np.int64(identifier)) & 0xffffffff < test_ratio * 2**32

# https://github.com/ageron/handson-ml/issues/71
import hashlib
def test_set_check(identifier, test_ratio, hash=hashlib.md5):
    return hash(np.int64(identifier)).digest()[-1] < 256 * test_ratio

def split_train_test_by_id(data, test_ratio, id_column):
    ids = data[id_column]
    in_test_set = ids.apply(lambda id_: test_set_check(id_, test_ratio))
    return data.loc[~in_test_set], data.loc[in_test_set]
# Or
from sklearn.model_selection import train_test_split
train_set, test_set = train_test_split(housing, test_size=0.2, random_state=42)
```

- Using Pandas to prepare data

```python
# drop data
housing = strat_train_set.drop("median_house_value", axis=1) # drop labels for training set
# option 1
sample_incomplete_rows.dropna(subset=["total_bedrooms"])    # option 1
# option 2
sample_incomplete_rows.drop("total_bedrooms", axis=1)
# option 3
median = housing["total_bedrooms"].median()
sample_incomplete_rows["total_bedrooms"].fillna(median, inplace=True)
# option 4 - using Imputer
from sklearn.preprocessing import Imputer
imputer = Imputer(strategy="median")
housing_num = housing.drop('ocean_proximity', axis=1)
# alternatively: housing_num = housing.select_dtypes(include=[np.number])
imputer.fit(housing_num)
X = imputer.transform(housing_num)
housing_tr = pd.DataFrame(X, columns=housing_num.columns,
                          index = list(housing.index.values))

# Now let's build a pipeline for preprocessing the numerical attributes:
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler

num_pipeline = Pipeline([
        ('imputer', Imputer(strategy="median")),
        ('attribs_adder', CombinedAttributesAdder()),
        ('std_scaler', StandardScaler()),
    ])

housing_num_tr = num_pipeline.fit_transform(housing_num)
```

- Fine-tune model

```python
from sklearn.tree import DecisionTreeRegressor

tree_reg = DecisionTreeRegressor(random_state=42)
tree_reg.fit(housing_prepared, housing_labels)

from sklearn.model_selection import cross_val_score

scores = cross_val_score(tree_reg, housing_prepared, housing_labels,
                         scoring="neg_mean_squared_error", cv=10)
tree_rmse_scores = np.sqrt(-scores)
# lin_scores = cross_val_score(lin_reg, housing_prepared, housing_labels,
#                            scoring="neg_mean_squared_error", cv=10)
#lin_rmse_scores = np.sqrt(-lin_scores)

#from sklearn.ensemble import RandomForestRegressor
#forest_reg = RandomForestRegressor(random_state=42)
#forest_reg.fit(housing_prepared, housing_labels)
#forest_scores = cross_val_score(forest_reg, housing_prepared, housing_labels,
#                                scoring="neg_mean_squared_error", cv=10)

from sklearn.model_selection import GridSearchCV

param_grid = [
    # try 12 (3×4) combinations of hyperparameters
    {'n_estimators': [3, 10, 30], 'max_features': [2, 4, 6, 8]},
    # then try 6 (2×3) combinations with bootstrap set as False
    {'bootstrap': [False], 'n_estimators': [3, 10], 'max_features': [2, 3, 4]},
  ]

forest_reg = RandomForestRegressor(random_state=42)
# train across 5 folds, that's a total of (12+6)*5=90 rounds of training 
grid_search = GridSearchCV(forest_reg, param_grid, cv=5,
                           scoring='neg_mean_squared_error', return_train_score=True)
grid_search.fit(housing_prepared, housing_labels)

# results:
pd.DataFrame(grid_search.cv_results_)
```

- Model persistence with Joblib

```python
# Joblib is a set of tools to provide lightweight pipelining in Python
from sklearn.externals import joblib
joblib.dump(my_model, "my_model.pkl") # DIFF
#...
my_model_loaded = joblib.load("my_model.pkl") # DIFF
```




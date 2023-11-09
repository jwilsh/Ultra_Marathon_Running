# Ultra_Marathon_Running

### Intoduction

Welcome to this portfolio practice project of mine where I will be showcasing some of my Python data analysis skills. I will be using the numpy, pandas and seaborn built in Python libraries in order to clean a large dataset about Ulta marathon runnings results and then transform it into something usable for analysis. Finally the seaborn library will be used to create some data visualisations and compare the data.

```
import numpy as np # linear algebra
import pandas as pd # data processing
import seaborn as sns # data visualisation

import os
for dirname, _, filenames in os.walk('/kaggle/input'):
    for filename in filenames:
        print(os.path.join(dirname, filename))

df = pd.read_csv("/kaggle/input/the-big-dataset-of-ultra-marathon-running/TWO_CENTURIES_OF_UM_RACES.csv")

#Lets have a preview of the data

df.head(10)
df.shape
df.dtypes
```

![Data Preview](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/25a3e42b-205a-47db-8ab0-70e19f4e524c)

Next step is to clean up the data.

For this project we are only going to use races in the USA which are either 50k or 50Mi and took place in the year 2020.

First step lets find 50Mi or 50k races. . 50km . 50mi

```
df[df['Event distance/length'] == '50mi']
```

Next we will combine 50k/50Mi races together with isin.

```
df[df['Event distance/length'].isin(['50km','50mi'])]

df[(df['Event distance/length'].isin(['50km','50mi'])) &  (df['Year of event'] == 2020)]

df[df['Event name'] == 'Everglades 50 Mile Ultra Run (USA)']['Event name'].str.split('(').str.get(1).str.split(')').str.get(0)

df[df['Event name'].str.split('(').str.get(1).str.split(')').str.get(0) == 'USA']
```

Now we can combine all of the filters together.

```
df[(df['Event distance/length'].isin(['50km','50mi'])) & (df['Year of event'] == 2020) & (df['Event name'].str.split('(').str.get(1).str.split(')').str.get(0) == 'USA')]

df2 = df[(df['Event distance/length'].isin(['50km','50mi'])) & (df['Year of event'] == 2020) & (df['Event name'].str.split('(').str.get(1).str.split(')').str.get(0) == 'USA')]
```

Next i want to remove (USA) from event name.

```
df2['Event name'].str.split('(').str.get(0)

df2['Event name'] = df2['Event name'].str.split('(').str.get(0)
```


Up next i will clean up athlete age.

```
df2['athlete_age'] = 2020 - df2['Athlete year of birth']
```

Remove h from athlete performance.

```
df2['Athlete performance'] = df2['Athlete performance'].str.split(' ').str.get(0)
```

Now i'm going to drop the following columns: Athlete Club, Athlete Country, Athlete year of birth, Athlete Age Category

```
df2 = df2.drop(['Athlete club', 'Athlete country', 'Athlete year of birth', 'Athlete age category'], axis = 1)
```

Next I will clean up any null values in the data.

```
df2.isna().sum()

df2[df2['athlete_age'].isna()==1]

df2 = df2.dropna()
```

I will now check for duplicate values as well.

```
df2[df2.duplicated() == True]

df2.reset_index(drop = True)
```

I will fix typos if needed

```
df2['athlete_age'] = df2['athlete_age'].astype(int)

df2['Athlete average speed'] = df2['Athlete average speed'].astype(float)

df2.dtypes
```

Next i will rename columns.

```
df2 = df2.rename(columns = {'Year of event':'year',
                        'Event dates':'race_day',
                        'Event name':'race_name',
                        'Event distance/length':'race_length',
                        'Event number of finishers':'race_number_of_finishers',
                        'Athlete performance':'athlete_performance',
                        'Athlete gender':'athlete_gender',
                        'Athlete average speed':'athlete_average_speed',
                        'Athlete ID':'athlete_id'
                        })
```

And now reorder the columns too.

```
df3 = df2[['race_day', 'race_name', 'race_length', 'race_number_of_finishers', 'athlete_id', 'athlete_gender', 'athlete_age', 'athlete_performance', 'athlete_average_speed']]

df3.head()
```

![Final data](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/55b5e941-0673-4e4e-a8ee-9d9dcffe7810)


After cleaning, filtering, sorting and transforming the data. I am now ready to do some analysis.

First of all lets find the 2 races which athlee_id 222509 ran in 2020.

```
df3[df3['race_name'] == 'Everglades 50 Mile Ultra Run ']

df3[df3['athlete_id'] == 222509]
```

![Athlete 222509](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/fd3408f0-2faa-4a87-a5b8-faf97a3fd095)


I'm now going to create a few different charts and graphs in order to represent the data.

```
sns.histplot(df3, x = 'race_length', hue = 'athlete_gender')
```

![Bar_Athelete_Gender](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/99729e0a-0340-4301-8b41-7d4b2e83240a)

The chart shows that Men & Women both equqlly pqrticipqted in the 50km races during 2020 in the USA however when it comes to the 50Mi races significally more Men participated than Women.

```
sns.displot(df3[df3['race_length'] == '50mi']['athlete_average_speed'])
```


![Average_Speed_Histo](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/d1a0b39a-90b6-40ac-91c6-a0399f5ce8b3)


This is a great way of showing how fast the majority of people can run in these races and what is considered fast or slow.

```
sns.violinplot(data = df3, x='race_length', y='athlete_average_speed', hue = 'athlete_gender', split = True, inner = 'quart', linewidth = 1)
```

![Average_Speed_Histo2](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/41635a27-3877-49d4-aa17-002d3a3998f9)


This graph gives us an even better insight into the average speeds of both Male & Female participants across both race types.

```
sns.lmplot(data=df3, x= 'athlete_age', y= 'athlete_average_speed', hue = 'athlete_gender')
```

![Athlete_Age_Scat](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/b8e22361-72c8-4f70-9074-5484d3b618a9)

This graph is another way of demonstrating the same data however it doesnt loook as effective because in this example there are too many data points plotted.

Lets finally get round to getting some answers to questions that I want to find out from the data.

. Difference in speed for the 50k, 50mi male to female.

```
df3.groupby(['race_length', 'athlete_gender'])['athlete_average_speed'].mean()
```

![Speed_Difference](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/a1e8606e-8430-4f02-8d03-088de7d0685c)


. What age groups are the best in the 50km Race (20 + races min) (Show top 15)

```
df3.query('race_length == "50mi"').groupby('athlete_age')['athlete_average_speed'].agg(['mean', 'count']).sort_values('mean', ascending = False).query('count>19').head(15)
```

![Mean_age](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/4ec49b6f-a06a-4f65-804b-b83d3b6e3f04)


. What age groups are the worst in the 50km Race (10 + races min) (Show top 20)

```
df3.query('race_length == "50mi"').groupby('athlete_age')['athlete_average_speed'].agg(['mean', 'count']).sort_values('mean', ascending = True).query('count>9').head(20)
```

![Mean_age2](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/a4cd74d4-e7f9-4ed9-8604-dd16eb5c1d14)


Are runners faster in summer or winter?

```
df3['race_month'] = df3['race_day'].str.split('.').str.get(1).astype(int)

df3['race_season'] = df3['race_month'].apply(lambda x: 'Winter' if x > 11 else 'Fall' if x > 8 else 'Summer' if x > 5 else 'Spring' if x > 2 else 'Winter')

df3.groupby('race_season')['athlete_average_speed'].agg(['mean', 'count']).sort_values('mean', ascending = False)
```

![Faster](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/28e6e959-6fb6-4305-95bb-74b58962ea56)


. For the 50Mi race only.

```
df3.query('race_length == "50mi"').groupby('race_season')['athlete_average_speed'].agg(['mean', 'count']).sort_values('mean', ascending = False)
```

![Faster2](https://github.com/jwilsh/Ultra_Marathon_Running/assets/98908958/ee79ce80-6696-4db6-8537-536380442a87)


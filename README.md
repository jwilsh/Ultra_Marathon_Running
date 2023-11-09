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
```


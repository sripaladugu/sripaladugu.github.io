

```python
import pandas as pd
import numpy as np
import matplotlib as mpl
%matplotlib inline
from ggplot import *
theme_bw()
mpl.rcParams["figure.figsize"] = "7.5, 7.5"
mpl.style.use('ggplot')

#gsize = theme_matplotlib(rc={"figure.figsize": "5.0, 5.0"}, matplotlib_defaults=False)

maxLevels = 3
file = "/Users/paladugs/workspace/Genia/161117_Magby_02/raw/final_table_2.csv"
df = pd.read_csv(file)
df.columns.values[0] = 'Sample#'
#print(df["Median"].tail(200))
baseline = df["Median"][1]
levelCnt = 0
barCodeEvents = []

dictBCevents = {}
for i in range(maxLevels+1):
     dictBCevents[i] = []

otherBCevents = []

for i in range(1,len(df["Median"])):
    if abs(baseline - df["Median"][i])/baseline < 0.1 or baseline < df["Median"][i]:
        baseline = df["Median"][i]
        barCodeEvents.append(levelCnt)
        if levelCnt in dictBCevents.keys():
            dictBCevents[levelCnt].append(i-levelCnt)
        levelCnt = 0
    else:
        levelCnt += 1


totalEvents = 0
for i in range(1,4):
    totalEvents = totalEvents + len(dictBCevents[i])

print("Total Barcode Events: ", totalEvents)

dfBCevents = pd.DataFrame(np.zeros( (3,3) ), columns=["#_Levels_In_Event", "Count", "%_of_Total"])
dfBCevents.loc[1] = np.array([1,2,3])

for i in range(1,4):
    dfBCevents.loc[i-1] = np.array([i, len(dictBCevents[i]), len(dictBCevents[i])/totalEvents])

print(dfBCevents)
dfCounts = dfBCevents["Count"]
dfCounts.index = dfBCevents["#_Levels_In_Event"]
dfCounts.plot(kind='bar'); 

```

    Total Barcode Events:  258
       #_Levels_In_Event  Count  %_of_Total
    0                1.0  172.0    0.666667
    1                2.0   18.0    0.069767
    2                3.0   68.0    0.263566



![png](/ipynb/BarcodeCntApp_files/BarcodeCntApp_0_1.png)


#### Accuracy of Barcode Capture Events (For Barcode event with 3 levels)


```python
dictAccBCevents = {3:[], 2:[]}
dictInAccBCevents = {3:[], 2:[]}

for i in dictBCevents[3]:
    levels = df["Median"][i-1:i+3].tolist()
    if levels[1] > levels[3] and levels[2] < levels[3]:
        dictAccBCevents[3].append(i)
    else:
        dictInAccBCevents[3].append(i)

print("=== Accurate Level 3 Calls ===")
dfAccBCevents = pd.DataFrame(columns=range(4), index=range(len(dictAccBCevents[3])))
j = 0
for i in dictAccBCevents[3]:
    dfAccBCevents.loc[j] = np.array(df["Median"][i-1:i+3])
    j = j+1

dfAccBCevents.columns = ["Baseline", "L1","L2","L3"]
print(dfAccBCevents)

dfAccBCevents[dfAccBCevents.columns]=dfAccBCevents[dfAccBCevents.columns].astype("float64")

print(" Box plot of Levels for 3-level Accurate Barcode Events ")
import matplotlib.pyplot as plt
import matplotlib
matplotlib.style.use('ggplot')
fig, ax = plt.subplots(1, 1, figsize=(5, 5))
dfAccBCevents.drop(["Baseline"],axis=1).boxplot(ax=ax)
```

    === Accurate Level 3 Calls ===
       Baseline    L1  L2    L3
    0       140    36  26  31.5
    1       136    35  26    33
    2       136    39  27    33
    3       135    36  27    32
    4       134    36  26    32
    5       136    36  26    32
    6       134    37  26  29.5
    7       132    36  26    32
    8       133  36.5  28    32
    9       124    37  28    32
    10      131    34  26    30
    11      127    35  26    30
    12      133    35  26    31
    13      133    38  26    32
    14      130    36  27    32
    15      131    40  26    31
    16      132    36  28    32
    17      132  34.5  26    32
    18      131    37  26    30
    19      131    36  26  32.5
    20      125    34  26    30
    21      124    34  26    32
    22      125    34  26    30
    23      124    35  25    30
    24      126    36  26    32
    25      126    34  26    30
    26      118    40  24    38
    27      118    34  24    32
    28      125    34  26    32
    29      122    34  26    30
    30      119    34  24    30
    31      120    34  24    30
    32      119    32  26    30
    33      117    35  24    30
    34      113    34  24    28
    35      108    33  24    30
    36      106    32  24    28
    37      105    32  24  28.5
    38      102    32  22    28
    39      100    32  24    29
    40      100    30  22    28
    41       98  29.5  22    28
    42       96    32  22    28
    43      123    33  22    30
    44      116    33  22    30
    45      122    34  22    28
    46      110    38  24  28.5
    47      119    34  22    28
    48      119    34  24    29
    49      117    33  22    30
    50      116    34  22    28
    51      110    32  22    28
    52      112    32  22    28
    53      112    34  22    28
    54      109    32  24    28
    55      108    32  22    28
     Box plot of Levels for 3-level Accurate Barcode Events 





    <matplotlib.axes._subplots.AxesSubplot at 0x11843d550>




![png](/ipynb/BarcodeCntApp_files/BarcodeCntApp_2_2.png)


#### Dwelltime statistics for 3 Level Accurate Barcode Events


```python
dfDwellTime = pd.DataFrame(data=np.zeros( (len(dictAccBCevents[3]),4) ), columns=["L1DT", "L2DT", "L3DT", "Total_Dwell_Time"])
j = 0
for i in dictAccBCevents[3]:
    L1DT = df["Shift_Time"][i] - df["Shift_Time"][i-1]
    L2DT = df["Shift_Time"][i+1] - df["Shift_Time"][i]
    L3DT = df["Shift_Time"][i+2] - df["Shift_Time"][i+1]
    TotalDT = df["Shift_Time"][i+2] - df["Shift_Time"][i-1]
    dfDwellTime.loc[j] = np.array([L1DT, L2DT, L3DT, TotalDT])
    j = j+1

print(dfDwellTime["Total_Dwell_Time"].describe())
print(dfDwellTime)
fig, ax = plt.subplots(1, 1, figsize=(5, 5))
dfDwellTime.boxplot(ax=ax)
```

    count       56.000000
    mean     17138.678571
    std      17017.746806
    min        588.000000
    25%       5360.250000
    50%      11053.000000
    75%      22727.750000
    max      66977.000000
    Name: Total_Dwell_Time, dtype: float64
           L1DT    L2DT     L3DT  Total_Dwell_Time
    0     569.0    66.0  16685.0           17320.0
    1     348.0   106.0   1575.0            2029.0
    2     549.0    36.0  15025.0           15610.0
    3    1755.0   593.0  14644.0           16992.0
    4    2168.0    23.0   5938.0            8129.0
    5     555.0   141.0   4541.0            5237.0
    6    2407.0    85.0    146.0            2638.0
    7   12129.0   211.0   2974.0           15314.0
    8    4540.0   138.0   3724.0            8402.0
    9       3.0    16.0   4950.0            4969.0
    10   3503.0   212.0    288.0            4003.0
    11      5.0   204.0   9144.0            9353.0
    12   1007.0   298.0   7852.0            9157.0
    13    386.0    30.0  25541.0           25957.0
    14      5.0   241.0   1391.0            1637.0
    15   2681.0    19.0   2913.0            5613.0
    16   4103.0   666.0    517.0            5286.0
    17   1027.0   354.0   3150.0            4531.0
    18    320.0   313.0   4759.0            5392.0
    19   9886.0   137.0   2355.0           12378.0
    20   1949.0   169.0    131.0            2249.0
    21   5084.0  2392.0  21963.0           29439.0
    22   1325.0    29.0   2362.0            3716.0
    23   3290.0   222.0   5249.0            8761.0
    24     35.0   579.0  11085.0           11699.0
    25    944.0   330.0   8021.0            9295.0
    26      7.0   552.0     29.0             588.0
    27      5.0   284.0   1733.0            2022.0
    28   1932.0   663.0  26661.0           29256.0
    29    252.0   625.0    278.0            1155.0
    30   4134.0   282.0  15458.0           19874.0
    31    963.0    24.0   4642.0            5629.0
    32    252.0   236.0    378.0             866.0
    33   4360.0   117.0  17936.0           22413.0
    34   4370.0  2186.0    423.0            6979.0
    35   8772.0   220.0  21767.0           30759.0
    36   1618.0   159.0  11707.0           13484.0
    37   4032.0   261.0   6114.0           10407.0
    38   1082.0   917.0  35794.0           37793.0
    39    594.0  1670.0   6315.0            8579.0
    40   1516.0   264.0  10942.0           12722.0
    41    371.0    98.0  47032.0           47501.0
    42  16382.0   463.0  33183.0           50028.0
    43   4491.0   277.0    617.0            5385.0
    44      3.0    12.0  22354.0           22369.0
    45   4242.0   162.0   1681.0            6085.0
    46      5.0  1562.0  23970.0           25537.0
    47   5678.0   430.0  11730.0           17838.0
    48   3964.0    75.0  14233.0           18272.0
    49    430.0   128.0  32253.0           32811.0
    50   6949.0    69.0  52530.0           59548.0
    51      5.0   153.0  66819.0           66977.0
    52  40338.0   335.0  21306.0           61979.0
    53    784.0    16.0  60464.0           61264.0
    54   1382.0    68.0  22222.0           23672.0
    55   3901.0  1529.0   7438.0           12868.0





    <matplotlib.axes._subplots.AxesSubplot at 0x118f0d390>




![png](/ipynb/BarcodeCntApp_files/BarcodeCntApp_4_2.png)



```python
pd.concat([dfAccBCevents, dfDwellTime], axis=1)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Baseline</th>
      <th>L1</th>
      <th>L2</th>
      <th>L3</th>
      <th>L1DT</th>
      <th>L2DT</th>
      <th>L3DT</th>
      <th>Total_Dwell_Time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>140.0</td>
      <td>36.0</td>
      <td>26.0</td>
      <td>31.5</td>
      <td>569.0</td>
      <td>66.0</td>
      <td>16685.0</td>
      <td>17320.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>136.0</td>
      <td>35.0</td>
      <td>26.0</td>
      <td>33.0</td>
      <td>348.0</td>
      <td>106.0</td>
      <td>1575.0</td>
      <td>2029.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>136.0</td>
      <td>39.0</td>
      <td>27.0</td>
      <td>33.0</td>
      <td>549.0</td>
      <td>36.0</td>
      <td>15025.0</td>
      <td>15610.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>135.0</td>
      <td>36.0</td>
      <td>27.0</td>
      <td>32.0</td>
      <td>1755.0</td>
      <td>593.0</td>
      <td>14644.0</td>
      <td>16992.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>134.0</td>
      <td>36.0</td>
      <td>26.0</td>
      <td>32.0</td>
      <td>2168.0</td>
      <td>23.0</td>
      <td>5938.0</td>
      <td>8129.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>136.0</td>
      <td>36.0</td>
      <td>26.0</td>
      <td>32.0</td>
      <td>555.0</td>
      <td>141.0</td>
      <td>4541.0</td>
      <td>5237.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>134.0</td>
      <td>37.0</td>
      <td>26.0</td>
      <td>29.5</td>
      <td>2407.0</td>
      <td>85.0</td>
      <td>146.0</td>
      <td>2638.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>132.0</td>
      <td>36.0</td>
      <td>26.0</td>
      <td>32.0</td>
      <td>12129.0</td>
      <td>211.0</td>
      <td>2974.0</td>
      <td>15314.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>133.0</td>
      <td>36.5</td>
      <td>28.0</td>
      <td>32.0</td>
      <td>4540.0</td>
      <td>138.0</td>
      <td>3724.0</td>
      <td>8402.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>124.0</td>
      <td>37.0</td>
      <td>28.0</td>
      <td>32.0</td>
      <td>3.0</td>
      <td>16.0</td>
      <td>4950.0</td>
      <td>4969.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>131.0</td>
      <td>34.0</td>
      <td>26.0</td>
      <td>30.0</td>
      <td>3503.0</td>
      <td>212.0</td>
      <td>288.0</td>
      <td>4003.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>127.0</td>
      <td>35.0</td>
      <td>26.0</td>
      <td>30.0</td>
      <td>5.0</td>
      <td>204.0</td>
      <td>9144.0</td>
      <td>9353.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>133.0</td>
      <td>35.0</td>
      <td>26.0</td>
      <td>31.0</td>
      <td>1007.0</td>
      <td>298.0</td>
      <td>7852.0</td>
      <td>9157.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>133.0</td>
      <td>38.0</td>
      <td>26.0</td>
      <td>32.0</td>
      <td>386.0</td>
      <td>30.0</td>
      <td>25541.0</td>
      <td>25957.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>130.0</td>
      <td>36.0</td>
      <td>27.0</td>
      <td>32.0</td>
      <td>5.0</td>
      <td>241.0</td>
      <td>1391.0</td>
      <td>1637.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>131.0</td>
      <td>40.0</td>
      <td>26.0</td>
      <td>31.0</td>
      <td>2681.0</td>
      <td>19.0</td>
      <td>2913.0</td>
      <td>5613.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>132.0</td>
      <td>36.0</td>
      <td>28.0</td>
      <td>32.0</td>
      <td>4103.0</td>
      <td>666.0</td>
      <td>517.0</td>
      <td>5286.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>132.0</td>
      <td>34.5</td>
      <td>26.0</td>
      <td>32.0</td>
      <td>1027.0</td>
      <td>354.0</td>
      <td>3150.0</td>
      <td>4531.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>131.0</td>
      <td>37.0</td>
      <td>26.0</td>
      <td>30.0</td>
      <td>320.0</td>
      <td>313.0</td>
      <td>4759.0</td>
      <td>5392.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>131.0</td>
      <td>36.0</td>
      <td>26.0</td>
      <td>32.5</td>
      <td>9886.0</td>
      <td>137.0</td>
      <td>2355.0</td>
      <td>12378.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>125.0</td>
      <td>34.0</td>
      <td>26.0</td>
      <td>30.0</td>
      <td>1949.0</td>
      <td>169.0</td>
      <td>131.0</td>
      <td>2249.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>124.0</td>
      <td>34.0</td>
      <td>26.0</td>
      <td>32.0</td>
      <td>5084.0</td>
      <td>2392.0</td>
      <td>21963.0</td>
      <td>29439.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>125.0</td>
      <td>34.0</td>
      <td>26.0</td>
      <td>30.0</td>
      <td>1325.0</td>
      <td>29.0</td>
      <td>2362.0</td>
      <td>3716.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>124.0</td>
      <td>35.0</td>
      <td>25.0</td>
      <td>30.0</td>
      <td>3290.0</td>
      <td>222.0</td>
      <td>5249.0</td>
      <td>8761.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>126.0</td>
      <td>36.0</td>
      <td>26.0</td>
      <td>32.0</td>
      <td>35.0</td>
      <td>579.0</td>
      <td>11085.0</td>
      <td>11699.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>126.0</td>
      <td>34.0</td>
      <td>26.0</td>
      <td>30.0</td>
      <td>944.0</td>
      <td>330.0</td>
      <td>8021.0</td>
      <td>9295.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>118.0</td>
      <td>40.0</td>
      <td>24.0</td>
      <td>38.0</td>
      <td>7.0</td>
      <td>552.0</td>
      <td>29.0</td>
      <td>588.0</td>
    </tr>
    <tr>
      <th>27</th>
      <td>118.0</td>
      <td>34.0</td>
      <td>24.0</td>
      <td>32.0</td>
      <td>5.0</td>
      <td>284.0</td>
      <td>1733.0</td>
      <td>2022.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>125.0</td>
      <td>34.0</td>
      <td>26.0</td>
      <td>32.0</td>
      <td>1932.0</td>
      <td>663.0</td>
      <td>26661.0</td>
      <td>29256.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>122.0</td>
      <td>34.0</td>
      <td>26.0</td>
      <td>30.0</td>
      <td>252.0</td>
      <td>625.0</td>
      <td>278.0</td>
      <td>1155.0</td>
    </tr>
    <tr>
      <th>30</th>
      <td>119.0</td>
      <td>34.0</td>
      <td>24.0</td>
      <td>30.0</td>
      <td>4134.0</td>
      <td>282.0</td>
      <td>15458.0</td>
      <td>19874.0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>120.0</td>
      <td>34.0</td>
      <td>24.0</td>
      <td>30.0</td>
      <td>963.0</td>
      <td>24.0</td>
      <td>4642.0</td>
      <td>5629.0</td>
    </tr>
    <tr>
      <th>32</th>
      <td>119.0</td>
      <td>32.0</td>
      <td>26.0</td>
      <td>30.0</td>
      <td>252.0</td>
      <td>236.0</td>
      <td>378.0</td>
      <td>866.0</td>
    </tr>
    <tr>
      <th>33</th>
      <td>117.0</td>
      <td>35.0</td>
      <td>24.0</td>
      <td>30.0</td>
      <td>4360.0</td>
      <td>117.0</td>
      <td>17936.0</td>
      <td>22413.0</td>
    </tr>
    <tr>
      <th>34</th>
      <td>113.0</td>
      <td>34.0</td>
      <td>24.0</td>
      <td>28.0</td>
      <td>4370.0</td>
      <td>2186.0</td>
      <td>423.0</td>
      <td>6979.0</td>
    </tr>
    <tr>
      <th>35</th>
      <td>108.0</td>
      <td>33.0</td>
      <td>24.0</td>
      <td>30.0</td>
      <td>8772.0</td>
      <td>220.0</td>
      <td>21767.0</td>
      <td>30759.0</td>
    </tr>
    <tr>
      <th>36</th>
      <td>106.0</td>
      <td>32.0</td>
      <td>24.0</td>
      <td>28.0</td>
      <td>1618.0</td>
      <td>159.0</td>
      <td>11707.0</td>
      <td>13484.0</td>
    </tr>
    <tr>
      <th>37</th>
      <td>105.0</td>
      <td>32.0</td>
      <td>24.0</td>
      <td>28.5</td>
      <td>4032.0</td>
      <td>261.0</td>
      <td>6114.0</td>
      <td>10407.0</td>
    </tr>
    <tr>
      <th>38</th>
      <td>102.0</td>
      <td>32.0</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>1082.0</td>
      <td>917.0</td>
      <td>35794.0</td>
      <td>37793.0</td>
    </tr>
    <tr>
      <th>39</th>
      <td>100.0</td>
      <td>32.0</td>
      <td>24.0</td>
      <td>29.0</td>
      <td>594.0</td>
      <td>1670.0</td>
      <td>6315.0</td>
      <td>8579.0</td>
    </tr>
    <tr>
      <th>40</th>
      <td>100.0</td>
      <td>30.0</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>1516.0</td>
      <td>264.0</td>
      <td>10942.0</td>
      <td>12722.0</td>
    </tr>
    <tr>
      <th>41</th>
      <td>98.0</td>
      <td>29.5</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>371.0</td>
      <td>98.0</td>
      <td>47032.0</td>
      <td>47501.0</td>
    </tr>
    <tr>
      <th>42</th>
      <td>96.0</td>
      <td>32.0</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>16382.0</td>
      <td>463.0</td>
      <td>33183.0</td>
      <td>50028.0</td>
    </tr>
    <tr>
      <th>43</th>
      <td>123.0</td>
      <td>33.0</td>
      <td>22.0</td>
      <td>30.0</td>
      <td>4491.0</td>
      <td>277.0</td>
      <td>617.0</td>
      <td>5385.0</td>
    </tr>
    <tr>
      <th>44</th>
      <td>116.0</td>
      <td>33.0</td>
      <td>22.0</td>
      <td>30.0</td>
      <td>3.0</td>
      <td>12.0</td>
      <td>22354.0</td>
      <td>22369.0</td>
    </tr>
    <tr>
      <th>45</th>
      <td>122.0</td>
      <td>34.0</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>4242.0</td>
      <td>162.0</td>
      <td>1681.0</td>
      <td>6085.0</td>
    </tr>
    <tr>
      <th>46</th>
      <td>110.0</td>
      <td>38.0</td>
      <td>24.0</td>
      <td>28.5</td>
      <td>5.0</td>
      <td>1562.0</td>
      <td>23970.0</td>
      <td>25537.0</td>
    </tr>
    <tr>
      <th>47</th>
      <td>119.0</td>
      <td>34.0</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>5678.0</td>
      <td>430.0</td>
      <td>11730.0</td>
      <td>17838.0</td>
    </tr>
    <tr>
      <th>48</th>
      <td>119.0</td>
      <td>34.0</td>
      <td>24.0</td>
      <td>29.0</td>
      <td>3964.0</td>
      <td>75.0</td>
      <td>14233.0</td>
      <td>18272.0</td>
    </tr>
    <tr>
      <th>49</th>
      <td>117.0</td>
      <td>33.0</td>
      <td>22.0</td>
      <td>30.0</td>
      <td>430.0</td>
      <td>128.0</td>
      <td>32253.0</td>
      <td>32811.0</td>
    </tr>
    <tr>
      <th>50</th>
      <td>116.0</td>
      <td>34.0</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>6949.0</td>
      <td>69.0</td>
      <td>52530.0</td>
      <td>59548.0</td>
    </tr>
    <tr>
      <th>51</th>
      <td>110.0</td>
      <td>32.0</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>5.0</td>
      <td>153.0</td>
      <td>66819.0</td>
      <td>66977.0</td>
    </tr>
    <tr>
      <th>52</th>
      <td>112.0</td>
      <td>32.0</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>40338.0</td>
      <td>335.0</td>
      <td>21306.0</td>
      <td>61979.0</td>
    </tr>
    <tr>
      <th>53</th>
      <td>112.0</td>
      <td>34.0</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>784.0</td>
      <td>16.0</td>
      <td>60464.0</td>
      <td>61264.0</td>
    </tr>
    <tr>
      <th>54</th>
      <td>109.0</td>
      <td>32.0</td>
      <td>24.0</td>
      <td>28.0</td>
      <td>1382.0</td>
      <td>68.0</td>
      <td>22222.0</td>
      <td>23672.0</td>
    </tr>
    <tr>
      <th>55</th>
      <td>108.0</td>
      <td>32.0</td>
      <td>22.0</td>
      <td>28.0</td>
      <td>3901.0</td>
      <td>1529.0</td>
      <td>7438.0</td>
      <td>12868.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
print("=== InAccurate Level 3 Calls ===")
dfInAccBCevents = pd.DataFrame(columns=range(4), index=range(len(dictInAccBCevents[3])))
j = 0
for i in dictInAccBCevents[3]:
    dfInAccBCevents.loc[j] = np.array(df["Median"][i-1:i+3])
    j = j+1

dfInAccBCevents.columns = ["Baseline", "L1", "L2", "L3"]
print(dfInAccBCevents)
```

    === InAccurate Level 3 Calls ===
       Baseline  L1  L2   L3
    0       137  34  27   34
    1       132  82  36   26
    2       126  34  28   34
    3     114.5  34  24   34
    4       128  32  76   54
    5     124.5  31  25   31
    6       117  28  34   26
    7       105  30  24   36
    8       102  30  23   30
    9        96  34  23   34
    10       91  30  23  NaN
    11      118  84  34   22



```python
dfDiffAccBCevents = abs(dfAccBCevents.drop("Baseline", axis=1).diff(axis=1))
dfDiffAccBCevents.drop(["L1"], axis=1, inplace=True)
dfDiffAccBCevents.columns = ["L2~L1", "L3~L2"]
print(dfDiffAccBCevents)

print(" Box plot of Level-Deltas for 3-level Accurate Barcode Events ")

fig, ax = plt.subplots(ncols=1, figsize=(5, 5))
dfDiffAccBCevents.boxplot(ax =ax)
#dfDiffAccBCevents.boxplot(by="L2~L1", ax=ax)
```

        L2~L1  L3~L2
    0    10.0    5.5
    1     9.0    7.0
    2    12.0    6.0
    3     9.0    5.0
    4    10.0    6.0
    5    10.0    6.0
    6    11.0    3.5
    7    10.0    6.0
    8     8.5    4.0
    9     9.0    4.0
    10    8.0    4.0
    11    9.0    4.0
    12    9.0    5.0
    13   12.0    6.0
    14    9.0    5.0
    15   14.0    5.0
    16    8.0    4.0
    17    8.5    6.0
    18   11.0    4.0
    19   10.0    6.5
    20    8.0    4.0
    21    8.0    6.0
    22    8.0    4.0
    23   10.0    5.0
    24   10.0    6.0
    25    8.0    4.0
    26   16.0   14.0
    27   10.0    8.0
    28    8.0    6.0
    29    8.0    4.0
    30   10.0    6.0
    31   10.0    6.0
    32    6.0    4.0
    33   11.0    6.0
    34   10.0    4.0
    35    9.0    6.0
    36    8.0    4.0
    37    8.0    4.5
    38   10.0    6.0
    39    8.0    5.0
    40    8.0    6.0
    41    7.5    6.0
    42   10.0    6.0
    43   11.0    8.0
    44   11.0    8.0
    45   12.0    6.0
    46   14.0    4.5
    47   12.0    6.0
    48   10.0    5.0
    49   11.0    8.0
    50   12.0    6.0
    51   10.0    6.0
    52   10.0    6.0
    53   12.0    6.0
    54    8.0    4.0
    55   10.0    6.0
     Box plot of Level-Deltas for 3-level Accurate Barcode Events 





    <matplotlib.axes._subplots.AxesSubplot at 0x118022278>




![png](/ipynb/BarcodeCntApp_files/BarcodeCntApp_7_2.png)



```python
threeLevelBitCalls = [len(dictBCevents[3])]*3
L1max = max(dfAccBCevents["L1"])
L1min = min(dfAccBCevents["L1"])

L2max = max(dfAccBCevents["L2"])
L2min = min(dfAccBCevents["L2"])

L3max = max(dfAccBCevents["L3"])
L3min = min(dfAccBCevents["L3"])

L2L1max = max(dfDiffAccBCevents["L2~L1"])
L2L1min = min(dfDiffAccBCevents["L2~L1"]) 
L3L2max = max(dfDiffAccBCevents["L3~L2"])
L3L2min = min(dfDiffAccBCevents["L3~L2"])

for i in dfInAccBCevents.index:
    levels = np.array(dfInAccBCevents.loc[i,["L1","L2","L3"]])

    if not (L1min <= levels[0] <= L1max):
        threeLevelBitCalls[0] = threeLevelBitCalls[0] - 1

    if not (L2min <= levels[1] <= L2max):
        threeLevelBitCalls[1] = threeLevelBitCalls[1] - 1

    if not (L3min <= levels[2] <= L3max):
        threeLevelBitCalls[2] = threeLevelBitCalls[2] - 1
    
    if (L1min <= levels[0] <= L1max) and \
       (L2min <= levels[1] <= L2max) and \
       (L3min <= levels[2] <= L3max):
        deltas = np.array(dfDiffAccBCevents.loc[i,["L2~L1", "L3~L2"]])
        if not(L2L1min <= deltas[0] <= L2L1max) and not(L3L2min <= deltas[1] <= L3L2max) :
            threeLevelBitCalls[1] = threeLevelBitCalls[1] - 1
        elif (L2L1min <= deltas[0] <= L2L1max) and not(L3L2min <= deltas[1] <= L3L2max):
            threeLevelBitCalls[2] = threeLevelBitCalls[2] - 1
        else:
            threeLevelBitCalls[0] = threeLevelBitCalls[0] - 1

threeLevelBitAccuracy = [ x/len(dictBCevents[3]) for x in threeLevelBitCalls ]
dfBitAccuracy = pd.DataFrame.from_items([ ("L1_Accuracy", [threeLevelBitAccuracy[0]]), 
                                          ("L2_Accuracy", [threeLevelBitAccuracy[1]]), 
                                          ("L3_Accuracy", [threeLevelBitAccuracy[2]]) ])
print(dfBitAccuracy)
```

       L1_Accuracy  L2_Accuracy  L3_Accuracy
    0     0.852941     0.941176     0.926471


#### Accuracy of 2 Level Events


```python

print("===== All 2 Level Events ======")
for i in dictBCevents[2]:
    print(df["Median"][i-1:i+2].tolist())

print("===== Accurate 2 Level Events ======")
dictAccBCevents[2] = []
dictInAccBCevents[2] = []

for i in dictBCevents[2]:
    levels = df["Median"][i-1:i+2].tolist()
    if (levels[1] > levels[2] and \
        L1min <= levels[1] <= L1max and \
        L2min <= levels[2] <= L2max and \
       L2L1min <= (levels[1] - levels[2]) <= L2L1max) or \
       (levels[2] > levels[1] and \
         L2min <= levels[1] <= L2max and \
         L3min <= levels[2] <= L3max and \
       L3L2min <= (levels[2] - levels[1]) <= L3L2max):
        dictAccBCevents[2].append(i)
    else:
        dictInAccBCevents[2].append(i)

dfAccBCevents = pd.DataFrame(columns=range(3), index=range(len(dictAccBCevents[2])))
j = 0
for i in dictAccBCevents[2]:
    dfAccBCevents.loc[j] = np.array(df["Median"][i-1:i+2])
    j = j+1

dfAccBCevents.columns = ["Baseline", "LA","LB"]
print(dfAccBCevents)
```

    ===== All 2 Level Events ======
    [135.0, 117.0, 70.0]
    [133.0, 35.0, 26.0]
    [127.0, 106.0, 63.0]
    [125.0, 85.0, 88.5]
    [126.0, 24.0, 30.0]
    [123.5, 34.0, 23.0]
    [124.0, 80.0, 102.0]
    [123.0, 34.0, 24.0]
    [120.0, 34.0, 26.0]
    [120.0, 30.0, 34.0]
    [117.0, 53.0, 48.0]
    [110.0, 34.0, 24.0]
    [107.0, 32.0, 24.0]
    [104.0, 39.0, 50.0]
    [98.0, 32.0, 23.0]
    [125.0, 36.0, 24.0]
    [107.5, 32.0, 24.0]
    [96.0, 14.0, nan]
    ===== Accurate 2 Level Events ======
      Baseline  LA  LB
    0      133  35  26
    1      126  24  30
    2    123.5  34  23
    3      123  34  24
    4      120  34  26
    5      110  34  24
    6      107  32  24
    7       98  32  23
    8      125  36  24
    9    107.5  32  24



```python
print("===== Inaccurate 2 Level Events ======")
dfInAccBCevents = pd.DataFrame(columns=range(3), index=range(len(dictInAccBCevents[2])))
j = 0
for i in dictInAccBCevents[2]:
    dfInAccBCevents.loc[j] = np.array(df["Median"][i-1:i+2])
    j = j+1
dfInAccBCevents.columns = ["Baseline", "LA","LB"]
print(dfInAccBCevents)
```

    ===== Inaccurate 2 Level Events ======
      Baseline   LA    LB
    0      135  117    70
    1      127  106    63
    2      125   85  88.5
    3      124   80   102
    4      120   30    34
    5      117   53    48
    6      104   39    50
    7       96   14   NaN



```python
twoLevelBitCalls = [0]*3
for i in dictBCevents[2]:
    levels = df["Median"][i-1:i+2].tolist()
    if (levels[1] > levels[2]):
        if (L1min <= levels[1] <= L1max):
            twoLevelBitCalls[0] = twoLevelBitCalls[0] + 1
        if (L2min <= levels[2] <= L2max):
            twoLevelBitCalls[1] = twoLevelBitCalls[1] + 1
        elif (L3min <= levels[2] <= L3max):
            twoLevelBitCalls[2] = twoLevelBitCalls[2] + 1        
    if (levels[2] > levels[1]):
        if (L2min <= levels[1] <= L2max):
            twoLevelBitCalls[1] = twoLevelBitCalls[1] + 1
        if (L3min <= levels[2] <= L3max):
            twoLevelBitCalls[2] = twoLevelBitCalls[2] + 1
```


```python
print("=== Two Level Bit Calls ===")
print(np.array(twoLevelBitCalls))

print("=== Three Level Bit Calls ===")
print(np.array(threeLevelBitCalls))

print("=== Two and Three Level Bit Calls Combined ===")
print(np.array(twoLevelBitCalls) + np.array(threeLevelBitCalls))

totalBitAccuracy = [ x/(len(dictBCevents[2])+len(dictBCevents[3])) for x in (np.array(twoLevelBitCalls) + np.array(threeLevelBitCalls))]
print(totalBitAccuracy)
```

    === Two Level Bit Calls ===
    [ 9 10  2]
    === Three Level Bit Calls ===
    [58 64 63]
    === Two and Three Level Bit Calls Combined ===
    [67 74 65]
    [0.77906976744186052, 0.86046511627906974, 0.7558139534883721]

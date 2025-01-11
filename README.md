# TradingView-to-analyze-the-upper-and-lower-shadows-wicks-
shadows (wicks)
This Pine Script code is designed for TradingView to analyze the upper and lower shadows (wicks) of candlesticks. It identifies two specific states for candles, logs their data into a table, and visually highlights them on the chart. Here's a detailed explanation:

---

### **1. Variable and Array Initialization**
The code begins by defining arrays to store shadow data and other variables to track the state:
```pinescript
var array<float> upperShadows = array.new_float(500, 0.0)
var array<float> lowerShadows = array.new_float(500, 0.0)
var array<int> barIndices = array.new_int(500, 0)
```
- `upperShadows`: Stores the upper shadows of the candles.
- `lowerShadows`: Stores the lower shadows of the candles.
- `barIndices`: Stores the bar indices (candlestick number).

---

### **2. State Definitions**
Two states (`State 1` and `State 2`) are defined along with counters to track their occurrences:
```pinescript
var bool isState1 = false
var bool isState2 = false
var int state1Count = 0
var int state2Count = 0
```
- `isState1`: True when the candle satisfies the conditions for State 1.
- `isState2`: True when the candle satisfies the conditions for State 2.
- `state1Count`, `state2Count`: Counters to record how many candles fall into each state.

---

### **3. Function to Calculate Shadows**
The function `VIPER_SUN_SHADOW_FUNCTION` calculates the upper and lower shadows for each candle and stores them in arrays:
```pinescript
VIPER_SUN_SHADOW_FUNCTION() =>
    for i = 0 to 499 by 1
        if bar_index - i >= 0 and not na(open[1]) and not na(close[1]) and not na(high[1]) and not na(low[1])
            upper = high[i] - math.max(open[i], close[i])
            lower = math.min(open[i], close[i]) - low[i]
```
- **Upper shadow (`upper`)**: Difference between the high price and the larger value of the open or close.
- **Lower shadow (`lower`)**: Difference between the low price and the smaller value of the open or close.

---

### **4. Table Creation for Display**
A table is created in the bottom-right corner of the chart using `table.new`. The table will display shadow data and state details:
```pinescript
var table dataTable = table.new(position.bottom_right, columns = 5, rows = 501, border_width = 1)
```
The table has the following columns:
1. **Bar Index**: The index of the candlestick.
2. **State**: The state of the candlestick (State 1 or State 2).
3. **Shadow Diff**: The difference between the upper and lower shadows.
4. **Shadow Length**: The length of the shadow.
5. **Condition**: Indicates whether the state is bullish or bearish.

---

### **5. Processing Candlesticks**
The function `processCurrentBar` checks each candlestick for specific conditions to classify them into State 1 or State 2:
```pinescript
processCurrentBar() =>
    state1 = math.abs(upper) == math.abs(lower) and close[1] > close[2]
    state2 = math.abs(upper) == math.abs(lower) and close[1] < close[2]
```
- **State 1**: Upper and lower shadows are equal, and the closing price is higher than the previous candle.
- **State 2**: Upper and lower shadows are equal, and the closing price is lower than the previous candle.

The results are displayed in the table with dynamic coloring:
- **Bullish (State 1)**: Green background.
- **Bearish (State 2)**: Red background.

---

### **6. Visual Elements**
The script highlights candles in different colors and adds dashed lines for better visualization:
- **For State 1**: A green background with a red dashed line.
- **For State 2**: A red background with a green dashed line.
```pinescript
bgcolor(isState1 ? color.rgb(76, 175, 79)  : na)
bgcolor(isState2 ? color.rgb(255, 0, 0)  : na)
```

Dashed lines are drawn using the `line.new` function:
```pinescript
line.new(bar_index[100], (open[1]+close[1]/2), bar_index+50, (open[1]+close[1]/2), extend=extend.both, style=line.style_dashed)
```

---

### **7. Displaying Counters**
At the end of the chart, the total counts for each state are displayed using a label:
```pinescript
label.new(bar_index, high, 
          text="State 1: " + str.tostring(state1Count) + "\nState 2: " + str.tostring(state2Count),
          style=label.style_label_down,
          color=color.rgb(33, 149, 243, 100),
          textcolor=color.white)
```
- The label shows the number of candles in State 1 and State 2.

---

### **Output**
1. **Shadows Analysis**: Upper and lower shadows are calculated and logged in the table.
2. **State Identification**: Each candlestick is classified as State 1 or State 2, with its details shown in the table.
3. **Visual Enhancements**: States are highlighted with colored backgrounds and dashed lines for better interpretation.
4. **Summary**: A label displays the total counts of each state on the chart.

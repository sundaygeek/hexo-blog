---
title: pandas使用经验-拆分dataframe中的字典
date: 2018-04-10 16:16:18
tags:
  - pandas
  - 数据导入
categories:
  - pandas
  - 数据处理
---

如果导入的dataframe中包含有字典的，使用**data.join(data['A10'].apply(json.loads).apply(pd.Series))**来拆分字典，组合成不同的列。

```
import pandas as pd
import json

filename = 'top5.txt'
data = pd.read_csv(filename, sep="\t", header=None)

# test    model.8.10  modelname   810 8101    2018-03-28 04:21:13 2018-03-28 04:21:13 
# 1   0   2018-04-02 14:50:54 {"cell_info":"LTE  PLMN:46000 EARFCN:38400(B39) Cell Identity
# :197539969 PCI:141 TAC:37884 RSSI:-65 RSRP:-95  RSRQ:-11 SINR*10: 133 EMM state:REGISTERED 
# service state:NORMAL reg domain:CS_PS lte_tx_power tx = 9 lte_rx_chain0 rssi=-64 rsrp=-94 
# sinr=133 lte_rx_chain1 rssi=-69 rsrp=-99 sinr=118  ","log_from":"com.android.phone",
# "reg_at_time":"31112","rat":"14","reg_during_time":"3554","hplmn":"46002"}    2018-04-02

columns = []
for i in range(data.shape[1]):
    columns.append('A' + str(i))
data.columns = columns
print(data.columns)
# Index(['A0', 'A1', 'A2', 'A3', 'A4', 'A5', 'A6', 'A7', 'A8', 'A9', 'A10','A11'],
#       dtype='object')

print(data['A10'])
# 0    {"cell_info":"LTE  PLMN:46000 EARFCN:38400(B39...
data = data.join(data['A10'].apply(json.loads).apply(pd.Series))

print(data.columns)
# Index(['A0', 'A1', 'A2', 'A3', 'A4', 'A5', 'A6', 'A7', 'A8', 'A9', 'A10',
#        'A11', 'cell_info', 'hplmn', 'log_from', 'rat', 'reg_at_time',
#        'reg_during_time'],
#       dtype='object')
```

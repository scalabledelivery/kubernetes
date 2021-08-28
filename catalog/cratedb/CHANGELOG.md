# Change Log
Newest changes should be entered at the top. The datetime format of log entries are generated with `date -u +"%Y/%m/%d %H:%M"`.

# 2021/08/28 02:54 - Liveness check added
Added an check against localhost:4200/, which when done without a browser returns the node status

# 2021/08/26 22:51 - Node Affinity Added
Added node affinity to push instances to separate nodes when possible.

# 2021/08/26 22:04 - CrateDB StatefulSet Created
Added CrateDB to catalog.
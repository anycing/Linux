# 非交互式添加分区

### disk\_partition.sh

```
#!/bin/bash

fdisk -l

fdisk /dev/sdb << EOF
n
p


+1G
w
EOF
```

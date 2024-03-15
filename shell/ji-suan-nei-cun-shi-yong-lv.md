# 计算内存使用率

### calc\_mem.sh

```bash
#!/bin/bash

mem_used=$(free -m | awk 'NR==2{print $3}')
mem_total=$(free -m | awk 'NR==2{print $2}')

echo "$(echo "scale=2;$mem_used*100/$mem_total" | bc)%"
```


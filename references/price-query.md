# 价格查询集成 - flight-compare

## 概述
本技能可以与 `flight-compare` 技能配合使用，实现在旅行攻略中嵌入实时价格信息。

## 集成方式

### 1. 机票价格查询
当需要查询机票价格时，使用以下命令：

```bash
# 基础查询（往返）
node /home/ubuntu/flight-compare/scripts/compare.js --from "出发城市" --to "目的城市" --depart "YYYY-MM-DD" --return "YYYY-MM-DD"

# 基础查询（单程）
node /home/ubuntu/flight-compare/scripts/compare.js --from "出发城市" --to "目的城市" --depart "YYYY-MM-DD"

# 高级筛选示例
node /home/ubuntu/flight-compare/scripts/compare.js --from "福州" --to "东京" --depart "2026-06-19" --journey-type 1 --max-price 3000 --sort-type 3
```

### 2. 酒店价格查询
```bash
node /home/ubuntu/flight-compare/scripts/flyai_hotel.js --dest "城市名" --check-in "YYYY-MM-DD" --check-out "YYYY-MM-DD"

# 高级筛选
node /home/ubuntu/flight-compare/scripts/flyai_hotel.js --dest "杭州" --stars "4,5" --sort rate_desc --max-price 800
```

### 3. 火车票查询
```bash
node /home/ubuntu/flight-compare/scripts/flyai_train.js --from "出发城市" --to "目的城市" --depart "YYYY-MM-DD"

# 高级筛选
node /home/ubuntu/flight-compare/scripts/flyai_train.js --from "北京" --to "上海" --depart "2026-06-19" --seat "二等座" --sort-type 3
```

### 4. 景点搜索
```bash
node /home/ubuntu/flight-compare/scripts/flyai_poi.js --city "城市名" --keyword "景点关键词"

# 按级别搜索
node /home/ubuntu/flight-compare/scripts/flyai_poi.js --city "北京" --level 5
```

## 在攻略中嵌入价格信息

### 格式建议
在攻略的时间线中，可以添加价格信息：

```markdown
- **HH:MM - HH:MM**：【交通】福州→东京（航班查询）
  - 📍 导航："福州长乐国际机场"
  - 💰 **机票价格**：约 ¥1850-¥2200（携程+飞猪对比）
  - ⏰ 航班时间：建议选择上午航班
  - ⚠️ **注意**：提前2小时到达机场
```

### 费用汇总表增强
在文档末尾的费用汇总表中，可以添加实时价格：

```markdown
## 💰 费用汇总表

| 项目 | 预估费用 | 实时价格 | 备注 |
|------|----------|----------|------|
| 机票（往返） | ¥4000 | ¥3650 | 携程+飞猪对比最低价 |
| 住宿（5晚） | ¥2500 | ¥2800 | 4星级酒店 |
| 餐饮 | ¥1000 | ¥950 | 每天约¥200 |
| 门票 | ¥500 | ¥480 | 景点门票 |
| 交通 | ¥300 | ¥280 | 当地交通 |
| **总计** | **¥8300** | **¥8160** | |

> 价格更新时间：2026-05-16 23:30
```

## 工作流程集成

### 在攻略生成中加入价格查询步骤

1. **信息收集阶段**：确认出发地、目的地、日期
2. **价格查询阶段**：
   ```bash
   # 查询机票价格
   node /home/ubuntu/flight-compare/scripts/compare.js --from "福州" --to "东京" --depart "2026-06-19" --return "2026-06-21"
   
   # 查询酒店价格
   node /home/ubuntu/flight-compare/scripts/flyai_hotel.js --dest "东京" --check-in "2026-06-19" --check-out "2026-06-21"
   ```
3. **攻略生成阶段**：将价格信息嵌入到攻略文档中
4. **HTML地图阶段**：在地图的费用部分显示实时价格

## 注意事项

1. **数据源**：flight-compare使用携程问道和飞猪两个数据源
2. **价格时效性**：机票和酒店价格实时变动，查询结果仅供参考
3. **API限制**：注意查询频率，避免触发API限制
4. **错误处理**：如果查询失败，使用历史数据或默认价格范围

## 故障排除

### 1. 脚本不存在
```bash
# 检查flight-compare是否已安装
ls -la /home/ubuntu/flight-compare/scripts/

# 如果不存在，克隆仓库
git clone https://github.com/chenkunqing/flight-compare.git /home/ubuntu/flight-compare
cd /home/ubuntu/flight-compare && npm install
```

### 2. 权限问题
```bash
# 确保脚本有执行权限
chmod +x /home/ubuntu/flight-compare/scripts/*.js
```

### 3. 依赖问题
```bash
# 安装依赖
cd /home/ubuntu/flight-compare && npm install

# 安装flyai CLI（如果需要飞猪数据源）
npm install -g flyai
```

## 示例：完整的工作流程

假设用户说："帮我规划去东京5天的旅行，顺便查一下机票和酒店价格"

1. **信息收集**：确认出发地、日期、人数、预算
2. **价格查询**：
   ```bash
   # 查询机票
   node /home/ubuntu/flight-compare/scripts/compare.js --from "福州" --to "东京" --depart "2026-06-19" --return "2026-06-23"
   
   # 查询酒店
   node /home/ubuntu/flight-compare/scripts/flyai_hotel.js --dest "东京" --check-in "2026-06-19" --check-out "2026-06-23" --stars "4,5"
   ```
3. **攻略生成**：基于查询结果生成详细的5天行程攻略
4. **费用汇总**：在攻略末尾添加基于实时价格的费用汇总表
5. **HTML地图**：创建包含价格信息的HTML交互式地图

## 相关技能

- **flight-compare**：独立的旅行数据查询技能
- **travel-itinerary**：旅行攻略生成技能（本技能）
- **super-flight**：飞猪机票查询技能
- **wendao-partner-qclaw-skill**：携程问道技能
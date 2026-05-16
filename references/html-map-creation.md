# HTML交互式地图创建指南

## 技术选型
- **地图库**：Leaflet.js（轻量、开源、易用）
- **地图瓦片**：**必须使用国内可访问的地图服务**，OpenStreetMap在国内被墙
- **推荐瓦片源**：
  - **高德地图**（首选）：需要API key
  - 腾讯地图（备选）：需要申请key
  - 天地图（备选）：需要申请key（tk参数）

## 高德地图API key获取方法
1. **从现有项目中查找**：检查用户的其他项目（如旅行项目），在`src/components/`或配置文件中搜索`AMAP_KEY`或`amap`关键字
2. **代码中常见位置**：
   - `const AMAP_KEY = '...'`（如`FootprintMap.tsx`）
   - 环境变量文件（`.env.local`、`.env.production`）
   - 配置文件（`config.js`、`config.ts`）
3. **搜索命令**：
   ```bash
   grep -r "amap\|AMAP\|11f75d76b215d1706a5309de5eac85c1" /home/ubuntu/
   ```

## 高德地图Leaflet配置（带API key）
```javascript
// 高德地图瓦片（使用API key）
const AMAP_KEY = '从项目获取的key';
L.tileLayer(`https://webrd0{s}.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=8&x={x}&y={y}&z={z}&key=${AMAP_KEY}`, {
    subdomains: '1234',
    attribution: '© 高德地图',
    maxZoom: 18
}).addTo(map);
```

## 高德地图Leaflet配置（备选：天地图，不需要key）
```javascript
// 天地图瓦片（不需要key，但需要tk参数）
L.tileLayer('http://t{s}.tianditu.gov.cn/DataServer?T=vec_w&x={x}&y={y}&l={z}&tk=1234567890', {
    subdomains: ['0', '1', '2', '3', '4', '5', '6', '7'],
    attribution: '© 天地图',
    maxZoom: 18
}).addTo(map);
// 添加注记层（显示地名）
L.tileLayer('http://t{s}.tianditu.gov.cn/DataServer?T=cva_w&x={x}&y={y}&l={z}&tk=1234567890', {
    subdomains: ['0', '1', '2', '3', '4', '5', '6', '7'],
    attribution: '',
    maxZoom: 18,
    opacity: 0.7
}).addTo(map);
```

## HTML地图功能要求
1. **地图显示**：显示完整路线（不同颜色区分不同路段）
2. **城市标记**：用圆形标记，点击显示城市名称
3. **路线绘制**：用Polyline连接各点，不同颜色区分
4. **Tab切换**：每天一个Tab，点击显示当天详细路线信息
5. **响应式设计**：支持手机和电脑访问
6. **图例说明**：显示路线颜色含义

## HTML地图结构
```html
<div class="header">标题和副标题</div>
<div id="map"></div>
<div class="legend">路线说明图例</div>
<div class="tabs">每日Tab按钮</div>
<div id="day1" class="day-content">Day1详细信息</div>
<!-- 其他天... -->
<div class="footer">底部说明</div>
```

## HTML地图内容结构
每个Day的内容区块应包含完整的时间线：
```html
<div id="day1" class="day-content">
    <h2>Day 1 (6/2 周二)：标题</h2>
    <div class="timeline">
        <div class="timeline-item">
            <div class="timeline-time">16:10-16:30</div>
            <div class="timeline-content">📍 具体安排</div>
        </div>
        <!-- 更多时间段 -->
    </div>
    <div class="tip">💡 提示信息</div>
    <div class="warning">⚠️ 住宿/注意事项</div>
</div>
```

## 为HTML地图优化的格式
当攻略需要用于HTML移动端展示时，内容应该：
- 用`<strong>`加粗景点名
- 用`<span style="font-size:12px;color:#666;">`包裹详细信息
- 用`·`分隔不同信息点
- 避免长段落，多用箭头`→`连接步骤

```html
<div class="timeline-content">
    ⭐ <strong>景点名</strong>描述<br>
    <span style="font-size:12px;color:#666;">门票信息 · 它是什么描述 · <strong>玩法：</strong>步骤1→步骤2→步骤3 · <strong>注意：</strong>注意事项</span>
</div>
```

## 地图导航链接处理

### 高德地图导航链接格式
```javascript
// 正确格式：使用真实地名，不是显示名
const amapUrl = `https://uri.amap.com/marker?position={经度},{纬度}&name={真实地名}&coordinate=gaode&callnative=1`;
```

### Marker数据结构设计
每个marker需要两个名称：
- `name`：显示名（用于地图标签和弹窗标题，可带emoji和day标记）
- `navName`：导航名（用于高德地图搜索，必须是真实地名）

```javascript
// 示例
{name: 'Day5 🇨🇳 霍尔果斯', coord: [44.12, 80.42], type: 'day5', navName: '霍尔果斯国门景区'}
```

### 弹窗内容设计
```javascript
const popupContent = `
    <div style="text-align:center;">
        <b>${marker.name}</b><br>           <!-- 显示名 -->
        <span style="color:#999;font-size:11px;">${navName}</span><br>  <!-- 真实地名 -->
        <a href="${amapUrl}">📍 高德地图导航</a>
    </div>
`;
```

### 关键原则
1. **navName必须是高德地图能搜到的真实地名**，如"霍尔果斯国门景区"而非"Day5 🇨🇳 霍尔果斯"
2. **弹窗同时显示显示名和真实地名**，方便用户在其他设备上手动搜索
3. **经度在前，纬度在后**：高德URI格式是 `position={lng},{lat}`

## 多日行程彩色路线图

当用户要求在总览地图上区分每天路线时，使用**不同颜色的线段**表示每一天：

### 颜色方案
| 日期 | 颜色代码 | 场景 |
|------|----------|------|
| Day1 | #FF6B6B | 抵达日 |
| Day2 | #4ECDC4 | 高原日 |
| Day3 | #45B7D1 | 返程日 |
| Day4 | #96CEB4 | 长途日 |
| Day5 | #FFEAA7 | 多景点日 |
| Day6 | #DDA0DD | 草原日 |
| Day7 | #FFB347 | 公路旅行日 |
| Day8 | #87CEEB | 风景日 |
| Day9 | #98D8C8 | 返程日 |

### 实现要点
1. **总览与详情分离**：总览用彩色区分日期，详情用统一颜色显示当天路线
2. **标记颜色一致**：标记点使用与当天路线相同的颜色
3. **浅色背景文字**：Day4/Day5颜色较浅，标签文字用深色(#333)
4. **图例分层**：上方每日颜色，下方地点类型（酒店/景点/餐饮）

## 坐标校验

### 常见坐标错误
地图坐标可能来自网络，存在偏差。校验方法：
1. 以已知准确坐标为参考点（如城市中心），计算相对位置
2. 特克斯县城 [43.21, 81.84] 是准确参考点
3. 喀拉峻景区应在特克斯东南20-30km，坐标约 [43.12-43.18, 81.88-81.95]
4. 如果景区坐标经度比城市大0.3度以上，很可能是错的（0.3度≈25km）

### 坐标系注意
- 高德地图使用GCJ-02坐标系（火星坐标）
- 如果使用GPS坐标（WGS-84），需要转换后才能准确定位
- 新疆地区坐标偏差影响相对较小，但仍需注意

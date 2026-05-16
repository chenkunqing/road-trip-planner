# Hermes技能管理命令参考

## 添加Tap源
```bash
hermes skills tap add chenkunqing/road-trip-planner
```

## 安装技能
```bash
# 从URL安装
hermes skills install https://raw.githubusercontent.com/chenkunqing/road-trip-planner/main/SKILL.md

# 安装到指定目录
hermes skills install https://raw.githubusercontent.com/chenkunqing/road-trip-planner/main/SKILL.md --category productivity --yes
```

## 更新技能
```bash
hermes skills update travel-itinerary
```

## 查看已安装技能
```bash
hermes skills list
```

## 查看可用技能
```bash
hermes skills browse
hermes skills search "关键词"
```

## 常见问题

### 技能更新不可用
如果`hermes skills update`显示"No updates available"，可能是因为技能是从本地或URL安装的，而不是从hub安装的。解决方法：
1. 从原始URL重新安装
2. 或者手动更新SKILL.md文件

### Tap源管理
```bash
# 查看已添加的tap
hermes skills tap list

# 移除tap
hermes skills tap remove chenkunqing/road-trip-planner
```

## 技能管理最佳实践

1. **检查技能版本**：安装前先检查GitHub仓库中的版本
2. **比较差异**：如果新版本与旧版本差异很大，考虑是否需要合并
3. **备份重要技能**：更新前备份旧版本的SKILL.md
4. **测试新技能**：安装后立即测试新技能的功能
5. **维护references目录**：将详细内容放在references目录中，保持SKILL.md简洁

## 从GitHub安装自定义技能

### 方法1：从URL直接安装
```bash
hermes skills install https://raw.githubusercontent.com/{owner}/{repo}/main/SKILL.md --yes
```

### 方法2：通过Tap安装
```bash
# 1. 添加tap源
hermes skills tap add {owner}/{repo}

# 2. 从tap安装
hermes skills install {skill-name}
```

## 技能文件结构
```
~/.hermes/skills/{category}/{skill-name}/
├── SKILL.md          # 主技能文件
├── references/       # 详细参考文档
│   ├── html-map-creation.md
│   ├── route-planning.md
│   └── advanced-features.md
├── templates/        # 模板文件
└── scripts/          # 脚本文件
```

## 注意事项

1. **版本兼容性**：新版本可能与旧版本不兼容，需要手动合并
2. **功能差异**：精简版可能缺少高级功能，需要评估是否需要保留
3. **用户偏好**：更新技能时要考虑用户的历史偏好和需求
4. **测试验证**：更新后应测试所有功能是否正常工作

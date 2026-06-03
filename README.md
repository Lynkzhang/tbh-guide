# TBH攻略站 - 更新与部署指南

## 项目结构

```
D:\opencode\TBH攻略站\
├── site\                    # 网站文件（部署这个目录）
│   ├── index.html           # 主页面
│   ├── rune.html            # 符文树页面
│   ├── data_items.js        # 物品数据（JS）
│   └── data_search.js       # 搜索数据（JS）
├── extracted\               # 解包数据
│   ├── tables\              # CSV数据表
│   ├── item_names.json      # 物品名映射
│   ├── item_group_names.json # 物品组名映射
│   ├── monster_names.json   # 怪物名映射
│   └── rune_tree.json       # 符文树数据
├── template.html            # HTML模板（保留占位符）
├── rebuild.py               # 主重建脚本
├── generate_rune_final.py   # 符文树生成脚本
└── tbh-guide\               # Git仓库（GitHub Pages）
```

## 游戏文件位置

- 游戏路径：`E:\SteamLibrary\steamapps\common\TaskbarHero\`
- 数据文件：`TaskBarHero_Data\`
- AssetBundle：`StreamingAssets\aa\StandaloneWindows64\`

## 更新流程

### 1. 游戏更新后重新解包

```bash
# 运行重建脚本（提取数据+生成网站）
python "D:\opencode\TBH攻略站\rebuild.py"
```

rebuild.py 做了什么：
1. 提取本地化数据（UnityPy解析bundle）
2. 提取CSV数据表（43个表）
3. 建立物品名映射（ItemKey → 中文名）
4. 翻译物品组名（韩文→中文）
5. 建立怪物名映射
6. 构建搜索数据（物品→掉落来源+概率）
7. 生成JS数据文件
8. 生成HTML内容（制作配方、宝箱掉落、关卡怪物）
9. 注入到template.html生成index.html

### 2. 更新符文树（如需要）

```bash
python "D:\opencode\TBH攻略站\generate_rune_final.py"
```

### 3. 部署到GitHub

```bash
# 复制site目录到Git仓库
Copy-Item "D:\opencode\TBH攻略站\site\*" "D:\opencode\TBH攻略站\tbh-guide\" -Recurse -Force

# 提交并推送
cd "D:\opencode\TBH攻略站\tbh-guide"
git add .
git commit -m "更新说明"
git push
```

GitHub Pages会自动从main分支部署，几分钟后生效。

## 网站地址

https://lynkzhang.github.io/tbh-guide/

## Git仓库

https://github.com/Lynkzhang/tbh-guide

## 注意事项

1. **模板文件**：`template.html` 保留了 `<!--craft-->`、`<!--chest-->`、`<!--stage-->` 占位符，rebuild.py会读取模板并替换
2. **翻译顺序**：物品组名翻译时，`UNCOMMON` 必须在 `COMMON` 之前替换，否则会变成"UN普通"
3. **稀有度颜色**：从游戏纹理提取的实际颜色，不要随意修改
4. **怪物显示**：关卡怪物显示全部名称，不截断

## 数据统计（2026-06-03更新）

- 物品名：5935条
- 物品组名：2440条
- 怪物名：61条
- 关卡：120个
- 掉落表：491个
- 搜索物品：5829个
- 符文节点：174个

## 依赖

- Python 3.x
- UnityPy（解析Unity AssetBundle）
- GitHub CLI（gh命令，用于部署）

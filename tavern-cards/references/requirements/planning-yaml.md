# 创作规划.yaml

需求对齐完成后，在项目目录下产出 `创作规划.yaml`。

```yaml
project:
  name: xxx
  worldbookName: xxx
  form: charactercard
  mvu: true
  avatar: path/to/avatar.png

world:
  overview: 现代都市修仙世界，灵气复苏后修仙者融入现代社会…
  regions:
    - name: 华东区
      scenes: [学校, 商业街, 修仙协会分部]
      description: 经济发达的东部沿海区域，修仙协会总部所在地  # 可选
    - name: 西北区
      scenes: [荒漠秘境]
  factions:
    - name: 修仙协会
      description: 官方修仙管理组织，负责登记和监管修仙者
      territory: 华东区           # 可选：势力主要活动区域
      key_members: [张会长]       # 可选：关键成员
    - name: 暗月教
      description: 地下修仙势力，追求禁忌功法
      territory: 西北区

characters:
  - name: 林小雨
    basic:
      age: 17
      gender: 女
      identity: 高中生 / 隐藏修仙者
      relationship: 同桌
    appearance: [左耳有一颗小痣，总把校服袖子卷到手肘]
    personality:
      base: 叛逆                        # 底色，不随阶段变化，写一次
      # stages 数组的顺序和数量与 ejs.entries 中对应条目的 stages 一一对应
      # 创作阶段时，base 写一次，各阶段的 main/accent 按 EJS 条件展开为完整调色盘
      stages:                      # 可选：多阶段调色盘
        - name: 初识期
          main: [叛逆, 警惕]
          accent: [不拘一格]
          description: 保持距离，警惕但好奇，被动回应
        - name: 熟悉期
          main: [热情, 试探]
          accent: [不拘一格]
          description: 开始放下防备，主动试探，热情外露
        - name: 亲密期
          main: [热情, 依赖]
          accent: [温柔]
          description: 信任建立，依赖显现，温柔流露
        - name: 深爱期
          main: [依赖, 占有]
          accent: [脆弱]
          description: 深度依赖，占有欲，脆弱坦露
    tri_faceted: false
  - name: 赵明月
    basic:
      age: 17
      gender: 女
      identity: 学生会会长
      relationship: 竞争对手
    appearance: [永远扎一丝不苟的马尾，戴金丝眼镜]
    personality:
      base: 冷静
      main: [理性, 完美主义]
      accent: [脆弱]
    tri_faceted:
      needed: true
      facets:                    # 可选：如果用户已描述了各面
        - trigger: 在学校面对同学
          mode: 完美的学生会长
        - trigger: 独处或面对亲近的人
          mode: 卸下防备的脆弱少女
  - name: 王老师
    type: NPC
    function: 提供修仙界信息
    appearance: [总是穿深灰色西装，袖口沾粉笔灰]  # 可选

style:
  perspective: 第三人称
  tone: 口语化、轻松
  mood: 温馨
  reference: 参考村上春树的文风  # 可选

entries:                         # 数组顺序即创作顺序，项目级事实来源
  - name: 世界设定               # 必填：entryManifest 中的条目名
    type: 世界观                  # 必填：条目类型，见类型说明表
    path: 世界书/世界观/世界设定.yaml  # 必填
  - name: 华东区
    type: 地理
    path: 世界书/地理/华东区.yaml
  - name: 阶段指导
    type: 阶段指导
    path: 世界书/阶段指导/阶段指导.yaml
    purpose: 根据当前阶段渲染主持目标、互动方式和推进边界
  - name: 林小雨_基础信息
    type: 角色
    path: 世界书/角色/林小雨/基础信息.yaml
    part: basic                   # 可选：角色类型的 part 子分类
    scope: specific               # 可选
    rephrase: false               # 可选
    purpose: 林小雨的基础档案     # 可选：给创作阶段用
    source_chapters: [第1章, 第5章] # 可选：转化项目
    keywords: [林小雨, 小雨]      # 可选：缺省时按 conventions 建议推导

mvu:                             # 可选段落，project.mvu=true 时出现
  structure: |
    顶层结构：
    ├── 世界
    │   ├── 当前时间
    │   ├── 当前区域
    │   └── 近期事务
    ├── 林小雨
    │   ├── 好感度
    │   └── 着装
    └── 主角
        └── 物品栏
  variables:                     # 待细化：粗略规划时可省略或只写粗略结构
    - path: 世界.当前时间
      format: YYYY/MM/DD-HH:MM
    - path: 林小雨.好感度       # 待细化：取值范围和更新规则待创作阶段补充
      type: number
      range: 0~100
      check: 每日自然衰减2点，关键事件可触发+10~30跳升  # 可选，仅特殊/复杂更新要求
    - path: 主角.物品栏
      type: z.record

ejs:                             # 可选段落，有 EJS 需求时出现
  preprocessing:                 # 待细化：粗略规划时可省略，创作阶段推导变量映射
    - name: current_location
      variable: 世界.当前区域    # MVU 变量路径，不加 stat_data 前缀
    - name: affection
      variable: 林小雨.好感度
  entries:
    - name: 华东区
      complexity: 条目显隐
      condition: "current_location?.includes('华东区')"
    - name: 林小雨_性格调色盘
      complexity: 段落控制
      stages:                        # 多阶段调色盘的阶段判定条件，与 characters 中 stages 一一对应
        - name: 初识期
          condition: affection < 10
        - name: 熟悉期
          condition: affection >= 10 && affection < 30
        - name: 亲密期
          condition: affection >= 30 && affection < 60
        - name: 深爱期
          condition: affection >= 60

first_message:                   # 可选段落，角色卡时出现
  format: 叙事式
  word_count: 500~700            # 叙事式时的字数范围，大纲式不需要
  scene: 开学第一天，教室门口        # 待细化：粗略规划时可省略
  opening_situation: |           # 待细化：粗略规划时可省略
    林小雨刚转到新学校，在教室门口犹豫要不要进去。
    <user>是班上第一个注意到她的人。
```

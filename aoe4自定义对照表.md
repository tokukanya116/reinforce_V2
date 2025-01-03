```
AI_CreateEncounter 514 好像是给ai创建一个目标
```

##### 控制台命令

```

FOW_ForceRevealAllUnblockedAreas() 开全图
localPlayer = World_GetPlayerAt(index) 找玩家
AI_SetDifficulty(localPlayer, 3) 重设难度
localPlayer.raceName
print(localPlayer.raceName)
```

##### bluePrints映射

###### entity 实体

```
outpost:哨塔
ovoo:敖包
pasture:牧场
barracks:兵营
stable:马厩
archery_range:靶场
blacksmith:铁匠铺
scar_dock:码头
siege_workshop:攻城武器库
monastery:寺，教堂
university:大学
landmark_age1~3_defense:防御地标
landmark_age1~3_economy:经济地标
town_center_capital:首都tc
town_center:小tc
mining_camp:矿场
econ_food:磨坊
econ_wood:伐木场
scar_market:市场
house:房屋
wonder:奇观
farm:田
```

```
如需要生成新ui，参考 ags_diplomacy_ui.scar
```

##### scar  脚本

**超级有用的脚本**

```
返回a向b的方向 1935
计算两个实体的距离 857
根据 字符串 直接返回一个 技能包蓝图 560 (注意事项看 Ability)
根据 技能包原型的蓝图id 返回 一个技能蓝图 566 (注意事项看 Ability)
向SGroup下发攻击命令 653
命令一个小队攻击移动到一个位置（任何位置都可以查询）。可以排队，可以按照计划，可以在半径内搜索掩护。sgroup可以选择在到达标记附近时删除，如果没有标记，则在半径10内删除 705
命令攻击者sgroup攻击移动到战略点目标；当它是可捕获的，sgroup将捕获它 1556
命令班组捕获班组武器实体组 1611
命令小队在指定位置建造建筑物，或继续在现有建筑物上建造。该命令还检查该位置是否已经存在建筑物，如果建筑物类型正确，小队将继续建造它。1816

```

##### **Ability**

注：技能包组需要在 Attributes 里 先打开 open Attributes 读取才能查看(!没有编辑权限，只能调用)

```
LocalCommand_SquadEntityAbility(player_id, official_gold, eg_single, bp_supervise, true, false)
```

##### 其他

###### 异常
实体生成错误会导致资源变成不可选(但游戏能正常运行且不报错，需要逐行注释查错)

##### 思路
1.如果你需要寻找安全的定位，最好基于一个建筑物作为起始目标
例如：

    ```
    AGS_LAYOUT_TABLE_A = {
    OFFSET_FRONT, --0
    OFFSET_BACK, --4
    OFFSET_LEFT, --6
    OFFSET_RIGHT, --2
    OFFSET_FRONT_LEFT,  --7
    OFFSET_BACK_LEFT, --5
    OFFSET_FRONT_RIGHT, --1
    OFFSET_BACK_RIGHT, --3
    }
    -- 值得注意的是：他们都是int值 所以你引用这个方向的时候 写 0 或者 OFFSET_FRONT 都是可以的
    如： 此处就是计算建筑边距
    -- 建筑一般都是6
    local building_radius = 6
    --只有城镇中心是8
    if Entity_IsOfType(building, "town_center") then
        building_radius = 8
    end
    local offset = OFFSET_FRONT --指的是在这个建筑的前方，你想要斜角就 找对角方向 OFFSET_FRONT_LEFT 之类的
    if offset % 2 == 1 then
        building_radius = building_radius * 1.4142 --sqrt(2) --建筑都是方形的，相当于补充边距
    end
    ```

2.关于位移格子
    并不完全是按照真实距离去进行位移的
    例：如果你打开地图模式，你就会发现建筑物可以遵循 栅格 布置
    栅格 指的是 地编的白色格子 或者 游戏里建造时显示的那些格子（1个 栅格 相当于 5）
    但是 建筑实体 本身是有 模型占位格 的（实体中心点在这个模型格中心 模型占位格一般 4个 占 1个 栅格）
    模型占位格 不对齐 栅格！ 一般比 自身 占用 栅格 要小
    简单来说 
    如果你想要在scar脚本模式下 将一个对齐另一个实体（不重叠） ，你就要考虑 实际距离(比如 小金矿和普通矿场 就是 3个 栅格，实际距离就是 15)
    同时他有个特性 （因为 1个地图栅格 有 4 模型占位格距离 的 容许机制，即取值在在这个容许范围之内，实体都不会在地图上位移超过1格）
    例如 日本矿场 无论是 大金 或 小金 ，计算偏移量都是 12.5；其他矿场 都是 10


##### 作者&鸣谢
雾隐kagari
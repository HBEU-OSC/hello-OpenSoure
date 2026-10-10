# 三蛇争霸 —— 完整说明文档

## 一、项目简介

**三蛇争霸** 是一个基于 Python + Pygame 的自动对战贪吃蛇游戏。屏幕上同时存在三条不同颜色的蛇，它们各自使用不同的 AI 策略自主移动、觅食和躲避危险，直到场上只剩下最后一条蛇时判定胜负。

- **红蛇**：贪心策略，直冲最近食物，不躲不避，鲁莽型
- **绿蛇**：BFS 寻路 + 空间评估，会思考的安全型
- **蓝蛇**：惯性随机游走，偶尔朝食物靠拢，随性型

游戏过程中，任何一条蛇出局（撞墙、撞自己、撞别的蛇），底部面板会立刻显示「X色蛇出局」并说明出局原因。当场上只剩最后一条蛇时，显示「X色蛇获胜！」并结束本局。

---

## 二、运行环境

| 项目 | 要求 |
|------|------|
| Python | 3.8 及以上（推荐 3.10+） |
| 操作系统 | Windows / macOS / Linux |
| 依赖库 | pygame |
| 显示 | 需要图形界面（不支持纯命令行） |

### 安装依赖

```bash
pip install pygame
```

---

## 三、文件结构

项目只有一个文件：

```
snake.py        主程序（约 400 行）
```

直接运行：

```bash
python snake.py
```

---

## 四、游戏规则

### 4.1 基本规则

1. 三条蛇在 28 × 20 的网格上自由移动，食物同时存在 3 个。
2. 每条蛇吃到食物后身体长度 +1，蛇头始终在身体列表的最前面。
3. 蛇每走一步，尾部会自然收缩一格（吃到食物时除外）。

### 4.2 出局条件

| 编号 | 出局原因 | 触发条件 |
|------|----------|----------|
| 1 | 撞到墙壁 | 蛇头超出网格边界 |
| 2 | 撞到自己身体 | 蛇头进入自己身体（尾部一格除外，因为下一步会收缩） |
| 3 | 撞到 X 色蛇的身体 | 蛇头进入别的蛇身体（尾部一格除外） |
| 4 | 与 X 色蛇迎面相撞 | 两条蛇的新蛇头落在同一格，双方同时出局 |

### 4.3 胜负判定

- **获胜**：场上只剩 1 条蛇存活 → 显示「X色蛇获胜！」
- **平局**：所有蛇同时出局（理论上可能出现）→ 显示「所有蛇同时出局，平局！」

### 4.4 出局信息显示

- 底部面板保留最近 **3 条** 出局信息。
- 文字颜色与出局蛇的颜色一致。
- 出局信息示例：
  - `红色蛇出局：撞到墙壁`
  - `绿色蛇出局：撞到蓝色蛇的身体`
  - `蓝色蛇出局：与红色蛇迎面相撞`

---

## 五、三种策略详解

### 5.1 红蛇 —— 贪心直冲型

```python
def strategy_red(game, snake):
    # 找到距离蛇头曼哈顿距离最近的食物
    # 在所有非反向方向中，选一步后离食物更近的方向
```

- **特点**：计算简单、速度快、直冲目标。
- **缺点**：完全无视危险，经常主动撞墙、撞别的蛇、绕进死胡同。
- **典型结局**：通常是最早出局的蛇。

### 5.2 绿蛇 —— BFS 寻路 + 空间评估

```python
def strategy_green(game, snake):
    # 用 BFS 搜索到最近食物的最短路径
    # 找到路径 → 沿第一步前进
    # 找不到路径 → 选相邻空格中"活动空间"最大的方向
```

- **特点**：会绕开障碍，走最短路径，无路可走时优先选空间大的方向保命。
- **空间评估**：用洪水填充（flood fill）计算从某格出发能走到的空地数量，上限 600。
- **缺点**：BFS 目标只是"最近食物"，不考虑"安全换空间"策略，长局可能把自己堵死。
- **典型表现**：存活时间通常最长，是最大热门。

### 5.3 蓝蛇 —— 惯性随机游走型

```python
def strategy_blue(game, snake):
    # 85% 概率保持原方向
    # 15% 概率改变方向
    #   其中 50% 概率朝最近食物靠一靠
    #   另外 50% 概率完全随机
```

- **特点**：行为不可预测，会绕圈、会突然调头，偶尔凑巧吃到食物。
- **缺点**：经常走着走着撞墙或钻进别的蛇身体。
- **典型表现**：存活时间随机性极大，偶尔能捡漏获胜。

---

## 六、操作说明

| 按键 | 功能 |
|------|------|
| `R` | 重新开始一局 |
| `ESC` | 退出游戏 |
| 关闭窗口 | 退出游戏 |

游戏**全自动运行**，玩家无需操作，只需观赏三条蛇的竞技表演。

---

## 七、界面布局

```
┌──────────────────────────────────┐
│                                  │
│                                  │
│        游戏主区域（28 × 20）      │
│        三条蛇 + 3 个食物          │
│                                  │
│                                  │
├──────────────────────────────────┤
│ 红色蛇出局：撞到墙壁              │  ← 底部信息面板（高度 100 px）
│ 绿色蛇出局：与蓝色蛇迎面相撞       │
│ 蓝色蛇获胜！                      │
└──────────────────────────────────┘
```

### 配色方案

| 元素 | 颜色值 | 说明 |
|------|--------|------|
| 背景 | (26, 28, 38) | 深色底 |
| 网格线 | (40, 42, 56) | 淡灰线 |
| 红蛇 | (235, 72, 72) | 珊瑚红 |
| 绿蛇 | (72, 200, 96) | 草绿 |
| 蓝蛇 | (82, 132, 240) | 天蓝 |
| 食物 | (255, 214, 64) | 金黄色圆点 |
| 信息面板 | (16, 17, 24) | 更深的黑 |

蛇头颜色会自动提亮 70 个色阶，让蛇头更醒目。

---

## 八、可调参数

所有配置都在代码开头的常量区，修改即可调整游戏节奏：

```python
CELL     = 22        # 每格像素大小
COLS     = 28        # 网格列数
ROWS     = 20        # 网格行数
PANEL_H  = 100       # 底部信息面板高度
WIDTH    = COLS * CELL
HEIGHT   = ROWS * CELL + PANEL_H

FPS      = 60        # 帧率
STEP_MS  = 110       # 每步间隔（毫秒），数字越小蛇越快
FOOD_NUM = 3         # 场上同时存在的食物数量
```

### 常见调整建议

| 想要的效果 | 调整方式 |
|------------|----------|
| 蛇跑得更快 | 减小 `STEP_MS`（如 80） |
| 蛇跑得更慢 | 增大 `STEP_MS`（如 150） |
| 更拥挤刺激 | 减小 `COLS`、`ROWS` |
| 更空旷 | 增大 `COLS`、`ROWS` |
| 食物更多 | 增大 `FOOD_NUM` |

---

## 九、完整源代码

保存为 `snake.py`，直接运行即可。

```python
# -*- coding: utf-8 -*-
"""
三蛇争霸 —— 三条不同颜色、不同 AI 策略的贪吃蛇自由竞技
  红蛇：贪心策略（直冲最近食物，不躲不避）
  绿蛇：BFS 寻路 + 最大空间规避（会思考的安全型）
  蓝蛇：惯性随机游走（爱直行，偶尔朝食物靠）

规则：
  · 蛇撞墙 / 撞自己 / 撞别的蛇 => 出局，底部面板显示"X色蛇出局：原因"
  · 场上只剩最后一条蛇 => 显示"X色蛇获胜！"，游戏结束
"""

import sys
import os
import random
from collections import deque
import pygame

# ==================== 基础配置 ====================
CELL     = 22
COLS     = 28
ROWS     = 20
PANEL_H  = 100
WIDTH    = COLS * CELL
HEIGHT   = ROWS * CELL + PANEL_H

FPS      = 60
STEP_MS  = 110
FOOD_NUM = 3

DIRS = {
    'up':    (0, -1),
    'down':  (0, 1),
    'left':  (-1, 0),
    'right': (1, 0),
}
OPPOSITE = {'up': 'down', 'down': 'up', 'left': 'right', 'right': 'left'}

SNAKE_COLORS = {
    '红': (235, 72, 72),
    '绿': (72, 200, 96),
    '蓝': (82, 132, 240),
}

BG_COLOR   = (26, 28, 38)
GRID_COLOR = (40, 42, 56)
PANEL_BG   = (16, 17, 24)
FOOD_COLOR = (255, 214, 64)


# ==================== 小工具 ====================
def in_bounds(p):
    return 0 <= p[0] < COLS and 0 <= p[1] < ROWS


def dir_from_delta(dx, dy):
    for name, (a, b) in DIRS.items():
        if a == dx and b == dy:
            return name
    return None


def build_blocked(game):
    blocked = set()
    for s in game.snakes:
        if not s.alive:
            continue
        for p in s.body[:-1]:
            blocked.add(p)
    return blocked


def flood_fill(start, blocked, limit=600):
    seen = {start}
    q = deque([start])
    while q and len(seen) < limit:
        cur = q.popleft()
        for dx, dy in DIRS.values():
            np_ = (cur[0] + dx, cur[1] + dy)
            if not in_bounds(np_) or np_ in seen or np_ in blocked:
                continue
            seen.add(np_)
            q.append(np_)
    return len(seen)


# ==================== 三种移动策略 ====================
def strategy_red(game, snake):
    """红蛇：纯贪心，朝最近的食物直线前进，从不考虑安全。"""
    head = snake.body[0]
    if not game.foods:
        return snake.direction

    target = min(game.foods, key=lambda f: abs(f[0] - head[0]) + abs(f[1] - head[1]))
    back = OPPOSITE[snake.direction]

    best_dir, best_dist = None, 10 ** 9
    for name, (dx, dy) in DIRS.items():
        if name == back:
            continue
        nx, ny = head[0] + dx, head[1] + dy
        d = abs(nx - target[0]) + abs(ny - target[1])
        if d < best_dist:
            best_dist, best_dir = d, name
    return best_dir or snake.direction


def strategy_green(game, snake):
    """绿蛇：BFS 找最近食物的路径；走不通时选活动空间最大的方向。"""
    head = snake.body[0]
    blocked = build_blocked(game)

    prev = {head: None}
    q = deque([head])
    target = None
    while q:
        cur = q.popleft()
        if cur in game.foods:
            target = cur
            break
        for dx, dy in DIRS.values():
            np_ = (cur[0] + dx, cur[1] + dy)
            if not in_bounds(np_) or np_ in prev or np_ in blocked:
                continue
            prev[np_] = cur
            q.append(np_)

    if target is not None:
        cur = target
        while prev[cur] != head:
            cur = prev[cur]
        return dir_from_delta(cur[0] - head[0], cur[1] - head[1])

    best, best_space = None, -1
    for name, (dx, dy) in DIRS.items():
        np_ = (head[0] + dx, head[1] + dy)
        if not in_bounds(np_) or np_ in blocked:
            continue
        space = flood_fill(np_, blocked)
        if space > best_space:
            best_space, best = space, name
    return best or snake.direction


def strategy_blue(game, snake):
    """蓝蛇：惯性随机游走，喜欢保持直行，偶尔朝食物凑一凑。"""
    head = snake.body[0]
    blocked = build_blocked(game)

    options = []
    for name, (dx, dy) in DIRS.items():
        np_ = (head[0] + dx, head[1] + dy)
        if in_bounds(np_) and np_ not in blocked:
            options.append(name)

    if not options:
        return snake.direction

    if snake.direction in options and random.random() < 0.85:
        return snake.direction

    if game.foods and random.random() < 0.5:
        target = min(game.foods,
                     key=lambda f: abs(f[0] - head[0]) + abs(f[1] - head[1]))
        options.sort(key=lambda n: abs(head[0] + DIRS[n][0] - target[0]) +
                                 abs(head[1] + DIRS[n][1] - target[1]))
        return options[0]

    return random.choice(options)


# ==================== 蛇 ====================
class Snake:
    def __init__(self, name, body, direction, strategy):
        self.name = name
        self.color = SNAKE_COLORS[name]
        self.body = [tuple(p) for p in body]
        self.direction = direction
        self.strategy = strategy
        self.alive = True
        self.death_reason = None


# ==================== 游戏主体 ====================
class Game:
    def __init__(self):
        self.snakes = [
            Snake('红', [(6, 6), (5, 6), (4, 6)], 'right', strategy_red),
            Snake('绿', [(COLS - 7, ROWS - 7), (COLS - 6, ROWS - 7), (COLS - 5, ROWS - 7)],
                  'left', strategy_green),
            Snake('蓝', [(COLS // 2, 3), (COLS // 2, 2), (COLS // 2, 1)], 'down', strategy_blue),
        ]
        self.foods = []
        self.messages = []
        self.over = False
        self.winner = None

        for _ in range(FOOD_NUM):
            self.spawn_food()

    def spawn_food(self):
        occupied = set()
        for s in self.snakes:
            if s.alive:
                occupied.update(s.body)
        occupied.update(self.foods)

        free = [(x, y) for x in range(COLS) for y in range(ROWS)
                if (x, y) not in occupied]
        if not free:
            return False
        self.foods.append(random.choice(free))
        return True

    def step(self):
        alive = [s for s in self.snakes if s.alive]
        if len(alive) <= 1:
            return

        new_dirs = {}
        for s in alive:
            d = s.strategy(self, s)
            new_dirs[s] = d if d in DIRS else s.direction

        heads = {}
        for s in alive:
            dx, dy = DIRS[new_dirs[s]]
            heads[s] = (s.body[0][0] + dx, s.body[0][1] + dy)

        deaths = {}

        for s in alive:
            h = heads[s]
            if not in_bounds(h):
                deaths[s] = "撞到墙壁"
                continue

            ate = h in self.foods
            own_body = s.body if ate else s.body[:-1]
            if h in own_body:
                deaths[s] = "撞到自己身体"
                continue

            for o in alive:
                if o is s:
                    continue
                if h in o.body[:-1]:
                    deaths[s] = f"撞到{o.name}色蛇的身体"
                    break

        for i in range(len(alive)):
            for j in range(i + 1, len(alive)):
                a, b = alive[i], alive[j]
                if heads[a] == heads[b]:
                    deaths.setdefault(a, f"与{b.name}色蛇迎面相撞")
                    deaths.setdefault(b, f"与{a.name}色蛇迎面相撞")

        for s in alive:
            if s in deaths:
                continue
            s.direction = new_dirs[s]
            h = heads[s]
            ate = h in self.foods
            if ate:
                self.foods.remove(h)
            s.body.insert(0, h)
            if not ate:
                s.body.pop()

        for s in alive:
            if s in deaths:
                s.alive = False
                s.death_reason = deaths[s]
                self.messages.append((f"{s.name}色蛇出局：{deaths[s]}", s.color))

        while len(self.foods) < FOOD_NUM:
            if not self.spawn_food():
                break

        remaining = [s for s in self.snakes if s.alive]
        if len(remaining) == 1:
            self.over = True
            self.winner = remaining[0]
            self.messages.append((f"{self.winner.name}色蛇获胜！", self.winner.color))
        elif len(remaining) == 0:
            self.over = True
            self.messages.append(("所有蛇同时出局，平局！", (255, 220, 120)))

    def draw(self, screen, font):
        board_h = ROWS * CELL
        screen.fill(BG_COLOR)

        for x in range(0, WIDTH + 1, CELL):
            pygame.draw.line(screen, GRID_COLOR, (x, 0), (x, board_h))
        for y in range(0, board_h + 1, CELL):
            pygame.draw.line(screen, GRID_COLOR, (0, y), (WIDTH, y))

        for s in self.snakes:
            if not s.alive:
                continue
            for i, p in enumerate(s.body):
                rect = pygame.Rect(p[0] * CELL + 1, p[1] * CELL + 1, CELL - 2, CELL - 2)
                if i == 0:
                    color = tuple(min(255, c + 70) for c in s.color)
                else:
                    color = s.color
                pygame.draw.rect(screen, color, rect, border_radius=5)

        for f in self.foods:
            cx = f[0] * CELL + CELL // 2
            cy = f[1] * CELL + CELL // 2
            pygame.draw.circle(screen, FOOD_COLOR, (cx, cy), CELL // 2 - 2)
            pygame.draw.circle(screen, (255, 250, 210), (cx - 2, cy - 2), 2)

        pygame.draw.rect(screen, PANEL_BG, (0, board_h, WIDTH, PANEL_H))
        pygame.draw.line(screen, (90, 92, 120), (0, board_h), (WIDTH, board_h), 2)

        y = board_h + 10
        for text, color in self.messages[-3:]:
            surf = font.render(text, True, color)
            screen.blit(surf, (14, y))
            y += 27

        if not self.messages:
            surf = font.render("三条蛇正在自由竞技……", True, (150, 150, 170))
            screen.blit(surf, (14, board_h + 10))


# ==================== 主循环 ====================
def main():
    pygame.init()
    screen = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("三蛇争霸 —— 三种策略的贪吃蛇对决")
    clock = pygame.time.Clock()

    # ---- 直接按文件路径加载中文字体，绕过 pygame 在 Windows 上的 SysFont bug ----
    def make_font(size=19):
        candidates = [
            r"C:\Windows\Fonts\msyh.ttc",      # 微软雅黑
            r"C:\Windows\Fonts\msyhbd.ttc",
            r"C:\Windows\Fonts\msyhl.ttc",
            r"C:\Windows\Fonts\simhei.ttf",    # 黑体
            r"C:\Windows\Fonts\simsun.ttc",    # 宋体
            r"C:\Windows\Fonts\simkai.ttf",    # 楷体
            r"C:\Windows\Fonts\simfang.ttf",   # 仿宋
            r"C:\Windows\Fonts\Deng.ttf",      # 等线
            r"C:\Windows\Fonts\msjh.ttc",      # 微软正黑体
            "/System/Library/Fonts/PingFang.ttc",           # macOS
            "/usr/share/fonts/opentype/noto/NotoSansCJK-Regular.ttc",  # Linux
        ]
        for path in candidates:
            if os.path.exists(path):
                try:
                    return pygame.font.Font(path, size)
                except Exception:
                    continue
        return pygame.font.Font(None, size)

    font = make_font(19)

    game = Game()
    last_step = pygame.time.get_ticks()

    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:
                    running = False
                elif event.key == pygame.K_r:
                    game = Game()
                    last_step = pygame.time.get_ticks()

        now = pygame.time.get_ticks()
        if not game.over and now - last_step >= STEP_MS:
            last_step = now
            game.step()

        game.draw(screen, font)
        pygame.display.flip()
        clock.tick(FPS)

    pygame.quit()
    sys.exit()


if __name__ == "__main__":
    main()
```

---

## 十、代码结构说明

### 10.1 模块划分

| 模块 | 作用 |
|------|------|
| 基础配置 | 网格尺寸、颜色、方向常量 |
| 小工具函数 | 边界判断、方向计算、洪水填充 |
| 三种策略函数 | `strategy_red` / `strategy_green` / `strategy_blue` |
| `Snake` 类 | 存储单条蛇的身体、方向、存活状态 |
| `Game` 类 | 游戏整体状态、每一步推进、绘制 |
| `main()` | 初始化 pygame、字体、主循环、事件处理 |

### 10.2 关键函数

| 函数 | 说明 |
|------|------|
| `build_blocked(game)` | 收集所有蛇身体占据的格子（尾巴不算），供策略判断障碍 |
| `flood_fill(start, blocked, limit)` | 从某格出发计算可到达的空地数量，评估"活动空间" |
| `Game.step()` | 单步推进：策略决策 → 碰撞检测 → 移动 → 结算 → 判定胜负 |
| `Game.spawn_food()` | 在空闲格随机生成一个食物 |

### 10.3 单步推进流程

```
① 收集存活蛇
② 每条蛇调用自己的策略函数，得到新方向
③ 计算每条蛇的新蛇头位置
④ 碰撞检测（撞墙 / 撞自己 / 撞别人 / 迎面撞）
⑤ 存活的蛇移动，吃到食物则身体 +1
⑥ 记录出局蛇的信息到 messages
⑦ 补充食物到 FOOD_NUM
⑧ 判定胜负：剩 1 条获胜，剩 0 条平局
```

---

## 十一、常见问题排查

### Q1：报错 `TypeError: expected str, bytes or os.PathLike object, not int`

这是 pygame 在 Windows 上读取系统字体注册表时的已知 bug。本版代码已经**完全绕过** `pygame.font.SysFont` 和 `match_font`，改用**直接按文件路径加载字体**，不会再触发此错误。

如果仍报错，请确认代码使用的是本文档「九、完整源代码」中的版本。

### Q2：中文显示为方块

说明 `make_font()` 中没有找到任何可用中文字体。解决办法：

1. 打开 `C:\Windows\Fonts` 目录。
2. 找一个中文字体文件（如 `msyh.ttc`）。
3. 把它的完整路径加到 `candidates` 列表的最前面。

### Q3：游戏速度太快 / 太慢

修改开头的常量 `STEP_MS`：
- 想要更快 → 改小，如 80。
- 想要更慢 → 改大，如 150。

### Q4：三条蛇总是一模一样的结果

红蛇、蓝蛇涉及随机策略，多次运行结果会不同。若你希望结果更稳定，可以降低蓝蛇的随机概率（`0.85` 和 `0.5` 这两个参数）。

### Q5：想换成四条蛇 / 五条蛇

在 `Game.__init__` 的 `self.snakes` 列表里追加 `Snake(...)` 对象，并给新蛇准备：
1. 初始位置（不与其他蛇重叠）。
2. 一个策略函数（可参考现有的三种，或自己写一个）。
3. 在 `SNAKE_COLORS` 中加一个颜色（否则会 KeyError）。

### Q6：想自己写新策略

写一个函数，签名如下：

```python
def strategy_xxx(game, snake):
    # game.foods      : list[(x, y)]  当前食物位置
    # snake.body      : list[(x, y)]  蛇身（头在 index 0）
    # snake.direction : 'up'/'down'/'left'/'right'
    # 返回值：'up'/'down'/'left'/'right'
    ...
    return 'right'
```

然后在创建 `Snake` 时把它作为 `strategy` 参数传入即可。

---

## 十二、扩展方向

如果想进一步提升这个项目，可以尝试：

- **增加蛇的数量**：支持 4 ~ 6 条蛇同场竞技。
- **多种食物**：加入"加速食物""缩短食物"等特殊道具。
- **策略进化**：用强化学习或遗传算法训练出更强的 AI 蛇。
- **回放系统**：记录每一步状态，支持比赛回放。
- **胜率统计**：自动运行 N 局，统计三种策略的获胜率。
- **玩家对战**：把红蛇改为键盘控制，人机同场。

---

## 十三、许可与致谢

本程序为教学 / 娱乐示例，可自由修改和分发。

祝玩得愉快 🐍🐍🐍

---
title: C语言课程设计
date: 2023-06-08 15:00:42
tags:
---

## 1. 设计目的与意义

推箱子游戏是一种益智游戏，玩家需要通过移动箱子和人物来完成任务。本项目的设计目的是让玩家通过实现推箱子游戏，学习和提高对于C语言的编程技能，并锻炼自己的逻辑思维能力。同时通过编写这个游戏，可以帮助玩家深入理解游戏设计中的各个环节，包括游戏界面设计，游戏逻辑实现，游戏音效和动画设计等。

## 2. 模块组成及各模块的功能



![module_structure](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202306081512412.png)

在这个结构图中，主程序 `main()` 调用各个模块的函数来完成游戏的逻辑。其中，`init_map()` 用于初始化地图，`draw_map()` 用于绘制地图，`draw_element()` 用于绘制单个元素，`move_player()` 用于移动玩家角色，`move_box()` 用于移动箱子，`check_box()` 用于检查箱子是否可以被推动，`check_win()` 用于检查游戏是否胜利，`game_over()` 用于处理游戏结束的逻辑。可以看到，这些模块的功能相对独立，彼此之间没有太多的耦合，使得代码结构清晰、易于维护。

本项目主要由以下几个模块组成：

### 2.1. 主函数模块

主函数模块是整个项目的入口，它主要负责初始化游戏界面，加载游戏数据，以及调用其他模块实现游戏的运行和结束。其中，该模块包括以下函数：

- `main()`：主函数，负责初始化游戏界面和游戏数据，并调用其他模块实现游戏的运行和结束。

### 2.2. 游戏界面模块

游戏界面模块主要负责实现游戏的显示和交互功能，包括游戏地图的绘制，玩家角色和箱子的移动，以及游戏结束时的提示和分数统计等。其中，该模块包括以下函数：

- `init_map()`：初始化游戏地图，包括地图大小和地图中各元素的位置信息。
- `draw_map()`：绘制游戏地图，包括地图中各元素的图形和颜色。
- `draw_element()`：绘制游戏中的单个元素，包括玩家角色，箱子和墙等。
- `move_player()`：移动玩家角色，包括上下左右四个方向的移动和越过箱子的操作。
- `move_box()`：移动箱子，包括将箱子推向上下左右四个方向的操作。
- `game_over()`：游戏结束时的提示和分数统计。

### 2.3. 游戏逻辑模块

游戏逻辑模块主要负责实现游戏的逻辑功能，包括玩家角色和箱子的移动规则，以及游戏结束条件的判断等。其中，该模块包括以下函数：

- `check_box()`：检查箱子是否可以被推动，包括箱子与墙和其他箱子的碰撞检测。
- `check_win()`：检查游戏是否胜利，包括所有箱子是否被推到正确的位置。

## 3. 运行界面

游戏运行时的界面如下所示：

**开始界面**

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202306081513073.png)

**游戏规则查看界面**

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202306081513633.png)

**初始界面**

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202306081514140.png)

人和箱子的显示优先级高于星号

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202306081514779.png)

成功后

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202306081514645.png)



## 4. 代码解释

代码部分的详细解释如下所示：

```c
#include <stdio.h>
#include <stdlib.h>
#include <conio.h>
#include <windows.h>
#include <stdbool.h>

#define ROW 10
#define COL 10

char map[ROW][COL] = {0}; //这部分代码定义了游戏地图的大小，使用了一个二维字符数组 `map` 来存储地图的元素。其中，`ROW` 和 `COL` 分别表示地图的行数和列数，初始值为 10。该数组的初始值为 0，即表示所有位置都是可行走区域。

```c
void init_map() {
    // 初始化地图，包括地图大小和地图上各个元素的位置
    // ...

    // 将地图上的元素存储到 map 数组中
    // ...
}
```

这部分代码定义了初始化游戏地图的函数 `init_map()`，该函数主要负责初始化游戏地图的大小和位置信息，并将地图上的元素存储到 `map` 数组中。具体实现细节在注释中给出。

```c
void draw_map() {
    // 绘制游戏地图，包括地图上各个元素的图形和颜色
    // ...

    // 绘制地图上的元素
    // ...
}
```

这部分代码定义了绘制游戏地图的函数 `draw_map()`，该函数主要负责绘制游戏地图和地图上的元素。具体实现细节在注释中给出。

```c
void draw_element(int x, int y, char c) {
    // 绘制游戏中的单个元素，包括玩家角色、箱子和墙等
    // ...

    // 绘制元素的图形和颜色
    // ...
}
```

这部分代码定义了绘制游戏中的单个元素的函数 `draw_element()`，该函数主要负责绘制游戏中的单个元素，包括玩家角色、箱子和墙等。具体实现细节在注释中给出。

```c
void move_player(int x, int y, int dx, int dy) {
    // 移动玩家角色，包括上下左右四个方向的移动和越过箱子的操作
    // ...

    // 根据玩家输入的方向移动角色
    // ...

    // 判断是否越过箱子
    // ...

    // 更新玩家角色的位置
    // ...
}
```

这部分代码定义了移动玩家角色的函数 `move_player()`，该函数主要负责根据玩家输入的方向移动角色，并判断是否越过箱子。具体实现细节在注释中给出。

```c
void move_box(int x, int y, int dx, int dy) {
    // 移动箱子，包括将箱子推向上下左右四个方向的操作
    // ...

    // 判断箱子是否可以被推动
    // ...

    // 更新箱子的位置
    // ...
}
```

这部分代码定义了移动箱子的函数 `move_box()`，该函数主要负责将箱子推向上下左右四个方向，并判断箱子是否可以被推动。具体实现细节在注释中给出。

```c
bool check_box(int x, int y, int dx, int dy) {
    // 检查箱子是否可以被推动，包括箱子与墙和其他箱子的碰撞检测
    // ...

    // 判断箱子是否与墙或其他箱子碰撞
    // ...

    // 判断箱子是否越过边界
    // ...

    // 更新箱子的位置
    // ...
}
```

这部分代码定义了检查箱子是否可以被推动的函数 `check_box()`，该函数主要负责检查箱子是否与墙或其他箱子碰撞，并判断箱子是否越过边界。具体实现细节在注释中给出。

```c
bool check_win() {
    // 检查游戏是否胜利，包括所有箱子是否被推到正确的位置
    // ...

    // 判断所有
```

## 5.源码

```c
#include <stdio.h>

// 函数声明
void init_map();
void draw_map();
void draw_element(char element);
void move_player(char direction);
int move_box(int x, int y);
int check_box(int x, int y);
int check_win();
void game_over();

// 全局变量
char map[5][5] = {
        {'#', '#', '#', '#', '#'},
        {'#', ' ', ' ', 'B', '#'},
        {'#', ' ', 'P', ' ', '#'},
        {'#', ' ', ' ', 'T', '#'},
        {'#', '#', '#', '#', '#'}
};
int player_x = 2;
int player_y = 2;

int main() {
    init_map();
    char input;
    while (1) {
        draw_map();
        scanf(" %c", &input);
        move_player(input);
        if (check_win()) {
            game_over();
            break;
        }
    }
    return 0;
}

void init_map() {
    // 初始化地图，这里已经在全局变量中定义了一个简单的地图
}

void draw_map() {
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            draw_element(map[i][j]);
        }
        printf("\n");
    }
}

void draw_element(char element) {
    printf("%c ", element);
}

void move_player(char direction) {
    int new_x = player_x;
    int new_y = player_y;
    switch (direction) {
        case 'w': new_x--; break;
        case 's': new_x++; break;
        case 'a': new_y--; break;
        case 'd': new_y++; break;
        default: return;
    }
    if (map[new_x][new_y] == ' ') {
        map[player_x][player_y] = ' ';
        player_x = new_x;
        player_y = new_y;
        map[player_x][player_y] = 'P';
    } else if (map[new_x][new_y] == 'B') {
        if (move_box(new_x, new_y)) {
            map[player_x][player_y] = ' ';
            player_x = new_x;
            player_y = new_y;
            map[player_x][player_y] = 'P';
        }
    }
}

int move_box(int x, int y) {
    int new_x = x * 2 - player_x;
    int new_y = y * 2 - player_y;
    if (check_box(new_x, new_y)) {
        map[x][y] = ' ';
        map[new_x][new_y] = 'B';
        return 1;
    }
    return 0;
}

int check_box(int x, int y) {
    if (map[x][y] == ' ' || map[x][y] == 'T') {
        return 1;
    }
    return 0;
}

int check_win() {
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            if (map[i][j] == 'T') {
                return 0;
            }
        }
    }
    return 1;
}

void game_over() {
    printf("恭喜你赢得了游戏！\n");
}

```


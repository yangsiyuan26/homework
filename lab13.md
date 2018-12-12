/*13-贪吃蛇实验报告*/
#include <stdio.h>

#include <windows.h>

#include <stdlib.h>

#include <math.h>

#include <conio.h>

#include <string.h>

#include <time.h>

void food();

void show();

void move();

void turn();

void check();

void ini();

int  dy[4] = { 0, 1, 0, -1 };

int  dx[4] = { -1, 0, 1, 0 };

int sum = 5;//蛇总长度

int over = 0;

int speed;

char map[17][17];

struct A[100]{
    int x, y;  //身体坐标 

    int dir;  //方向（只有蛇头的方向是有用的） 
}

void ini() {  //初始化游戏界面

    speed = 500;
    over = 0;
    sum = 5;
    //movable[0] = 0; movable[1] = 1; movable[2] = 1; movable[3] = 0;
    //distance[0] = 9999; distance[1] = 9999; distance[2] = 9999; distance[3] = 9999;
    int i, j;
    for (i = 0; i < 100; i++) {  //初始化蛇头和蛇身的数据
        A[i].dir = 0;
        A[i].x = 0;
        A[i].y = 0;
    }
    A[0].x = 1; A[0].y = 1;  //地图左上角设置一条长度为5的蛇
    A[1].x = 1; A[1].y = 2;
    A[2].x = 1; A[2].y = 3;
    A[3].x = 1; A[3].y = 4;
    A[4].x = 1; A[4].y = 5; A[4].dir = 1;
    srand(time(0));
    for (i = 0; i < 17; i++) {  //设置地图
        for (j = 0; j < 17; j++) {
            map[i][j] = '*';
        }
    }
    for (i = 1; i < 16; i++) {
        for (j = 1; j < 16; j++) {
            map[i][j] = ' ';
        }
    }
    //map[6][5] = '*'; map[6][6] = '*'; map[6][7] = '*';
    //map[7][5] = '*'; map[7][7] = '*';
    map[A[4].x][A[4].y] = 'H';  //设置蛇头
    for (i = 0; i < sum - 1; i++) {  //设置蛇身
        map[A[i].x][A[i].y] = 'X';
    }
    food();
    //calculate();
}

void show() {//显示界面

int i, j, x, y;
    
    for (i = 0; i < 17; i++) {  //显示界面
        for (j = 0; j < 17; j++) {
            printf("%c", map[i][j]);
        }
        printf("\n");
    }
    while (1) {
        Sleep(speed);  //界面刷新速度
        turn();
        move();
        if (over) {  //设置蛇死掉后可以进行的操作 
            while (1) {
                char ch = _getch();
                if (ch == 113) {  //输入‘q’结束 
                    return;
                }
                else if (ch == 114) {  //输入‘r’重新开始 
                    ini();
                    break;
                }
            }
        }
        system("cls");  //清屏 
        for (i = 0; i < 17; i++) {  //重新显示界面
            for (j = 0; j < 17; j++) {
                printf("%c", map[i][j]);
            }
            printf("\n");
        }
        //calculate(); //计算并记录蛇头与食物距离
    }
}

void food() {  //生成食物（任务二第一步）

    int x, y;
    while (1) {
        x = (int)(15 * rand() / (RAND_MAX + 1.0));  //随机产生一组食物坐标
        y = (int)(15 * rand() / (RAND_MAX + 1.0));
        if (map[x][y] == ' ') {  //如果是空格则在该处生成食物
            map[x][y] = 'O';
            //foodx = x;  //记录食物坐标 
            //foody = y;
            break;
        }
    }
}

void move() {  //蛇移动

    int i, x, y;
    int t = sum;  //t记录当前蛇总长度 
    check();  //移动前检查按当前方向移动一步后的情况
    if (t == sum) {  //没有吃到苹果
        for (i = 0; i < sum - 1; i++) {
            if (i == 0) {  //蛇尾坐标处变成空格，把蛇尾坐标变成前一个蛇身的坐标 
                map[A[i].x][A[i].y] = ' ';
                A[i].x = A[i + 1].x;
                A[i].y = A[i + 1].y;
            }
            else {  //每个蛇身坐标都变为它前一个蛇身的坐标
                A[i].x = A[i + 1].x;
                A[i].y = A[i + 1].y;
            }
            map[A[i].x][A[i].y] = 'X';  //把地图上蛇身坐标处的字符设置成‘X’
        }
        A[sum - 1].x = A[sum - 1].x + dx[A[sum - 1].dir];  //蛇头按当前方向移动一格 
        A[sum - 1].y = A[sum - 1].y + dy[A[sum - 1].dir];
        map[A[sum - 1].x][A[sum - 1].y] = 'H';  //把地图上蛇头坐标处的字符设置成‘H’
    }
    else {  //吃到苹果（sum会加1）
        map[A[sum - 2].x][A[sum - 2].y] = 'X';  //把地图上原蛇头坐标处的字符设置成‘X’
        A[sum - 1].x = A[sum - 2].x + dx[A[sum - 2].dir];  //新蛇头的坐标是原蛇头沿当前方向移动一格后的坐标 
        A[sum - 1].y = A[sum - 2].y + dy[A[sum - 2].dir];
        A[sum - 1].dir = A[sum - 2].dir;  //新蛇头方向为原蛇头的方向
        map[A[sum - 1].x][A[sum - 1].y] = 'H';  //把地图上蛇头坐标处的字符设置成‘H’
        food();
    }

void check() {  //检查是否死亡或者吃到食物（任务二第二步

    int x, y, i, j;
    x = A[sum - 1].x + dx[A[sum - 1].dir];  //记录按当前方向移动一格后蛇头的坐标 
    y = A[sum - 1].y + dy[A[sum - 1].dir];
    if (map[x][y] == '*' || map[x][y] == 'X') {  //如果地图上该坐标处字符为‘*’或‘X’就死亡 
        if (x != A[0].x || y != A[0].y) {  //蛇尾除外 
            map[8][4] = 'G'; map[8][5] = 'A'; map[8][6] = 'M'; map[8][7] = 'E';  //输出“GAME OVER” 
            map[8][9] = 'O'; map[8][10] = 'V'; map[8][11] = 'E'; map[8][12] = 'R';
            map[8][8] = ' ';
            system("cls");
            for (i = 0; i < 17; i++) {
                for (j = 0; j < 17; j++) {
                    printf("%c", map[i][j]);
                }
                printf("\n");
            }
            printf("Input 'r' to restart\nInput 'q' to quit\n");
            over = 1;
        }
    }
    else if (map[x][y] == 'O') {  //吃到苹果 
        sum++;  //蛇身总长加1 
        speed = ((600 - sum * 20)>100) ? (600 - sum * 20) : 100; //速度加快 
    }
}

void turn() {  //转弯

    if (_kbhit()) {
        char dir = _getch();  //读取输入的键 
        switch (dir) {  //改变方向 
        case 119: A[sum - 1].dir = (A[sum - 1].dir == 2)?2:0; break;
        case 100: A[sum - 1].dir = (A[sum - 1].dir == 3)?3:1; break;
        case 115: A[sum - 1].dir = (A[sum - 1].dir == 0)?0:2; break;
        case 97: A[sum - 1].dir = (A[sum - 1].dir == 1)?1:3; break;
        }
    }
}

int main() {

    char ch = _getch();
    system("cls");
    ini();
    show();
    return 0;
}
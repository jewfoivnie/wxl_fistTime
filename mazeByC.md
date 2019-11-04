```c
#include<stdio.h>
#include<stdlib.h>
#include<time.h>
int m = 7,n = 7;
int maze[7][7];
typedef struct Stack{
	int top;
	int *x;
	int *y;
	int *direction;
}Stack;
void createMaze(){//随机构造迷宫
	srand((int)time(0));
	int i,j;
	for(i= 0;i<m;i++){
		for(j = 0;j<n;j++){
			maze[i][j] = rand()%5+1;
			if(maze[i][j] == 1||maze[i][j] == 2||maze[i][j] == 3) maze[i][j] = 0;
			else maze[i][j] = 1;
			if(i==0||i==m-1||j==0||j==n-1) maze[i][j] = 1;//给周围围一堵墙
			if((i == 1&&j==1)||(i==m-2&&j==n-2)) maze[i][j]  = 0;//把迷宫第一个和最后一个设为通路
		}
	}
	printf("输出迷宫：\n");
	for(i = 0;i<m;i++){//输出迷宫
		for(j = 0;j<n;j++) printf("%d ",maze[i][j]);
		putchar(10);
	}
}
Stack *Initialize(Stack *s){//初始化栈
	s->x = (int *)malloc(sizeof(Stack)*m*n);
	s->y = (int *)malloc(sizeof(Stack)*m*n);
	s->direction = (int *)malloc(sizeof(Stack)*m*n);
	if(s->x ==NULL||s->y==NULL||s->direction==NULL){
		printf("创建栈失败！");
		return NULL;
	}
	s->top = -1;
	return s;
}

void pushstack(int x,int y,Stack *s){//入栈，将坐标入栈
	s->top++;
	s->x[s->top] = x;s->y[s->top] = y;
}
void popstack(Stack *s){//出栈
	s->top--;
}
void walk(Stack *s){//开始走迷宫
	int x,y;
	int count;//来计数探索的周围的边数
	pushstack(1,1,s);//入口入栈
	maze[1][1] = 2;
	while(s->top!=-1){
		count = 1;
		if(s->top!=0){//如果当前的点不是迷宫入口
			s->direction[s->top] = s->direction[s->top-1];
		}
		if(s->top==0) s->direction[s->top] = 3;//如果是迷宫入口,则从方向为3开始
		while(count<=8){
			x = directionx(s->direction[s->top],s->x[s->top]);//将当前点的当前方向的点的坐标记录下来
			y = directiony(s->direction[s->top],s->y[s->top]);
			if(maze[x][y]==0){//如果可走通
				pushstack(x,y,s);//入栈
				maze[x][y] = 2;//走过的路赋值2
				break;
			}
			count++;
			s->direction[s->top]++;
			if(s->direction[s->top]>7) s->direction[s->top] = 0;
		}
		if(s->x[s->top]==m-2&&s->y[s->top]==n-2) return;
		if(count==9)//如果8个方向都走完了还没有走动就出栈
			popstack(s);
	}
}
int directionx(int n,int x){//传入方向和当前点的横坐标，得到该方向的下一个点的横坐标（方向从0开始顺时针旋转，0为左上角）
	static int x1;
	switch(n){
		case 0:x1 = --x;break;
		case 1:x1 = --x;break;
		case 2:x1 = --x;break;
		case 3:x1 = x;break;
		case 4:x1 = ++x;break;
		case 5:x1 = ++x;break;
		case 6:x1 = ++x;break;
		case 7:x1 = x;break;
	}
	return x1;
}
int directiony(int n,int y){//传入方向和当前点的纵坐标，得到该方向的下一个点的纵坐标（方向从0开始顺时针旋转，0为左上角）
	static int y1;
	switch(n){
		case 0:y1 = --y;break;
		case 1:y1 = y;break;
		case 2:y1 = ++y;break;
		case 3:y1 = ++y;break;
		case 4:y1 = ++y;break;
		case 5:y1 = y;break;
		case 6:y1 = --y;break;
		case 7:y1 = --y;break;
	}
	return y1;
}
void printRoute(Stack *s){//输出走的路线
	int i = 0;
	for(;i<s->top;i++) printf("(%d,%d)->",s->x[i],s->y[i]);
	if(s->top>=0) printf("(%d,%d)",m-2,n-2);
}
void printWalkLine(Stack *s){//结合迷宫将路线输出
	int i,j;
	int temp,flag = 1;
	putchar(10);
	printf("输出在迷宫中走的路线：\n");
	for(i = 0;i<m;i++){
		for(j = 0;j<n;j++) {
			temp = s->top;
			flag = 1;
			while(temp>-1){
				if(temp==-1)break;
				if(i==s->x[temp]&&j == s->y[temp]){//对于每一个数组的坐标，如果能在栈中找到，就输出“X”
					flag = 0;
					printf("X ");
				}
				temp--;
			}
			if(flag) printf("%d ",maze[i][j]);//否则输出值
		}
        putchar(10);
	}
}
void turnTwoToOne(){//将迷宫中的2变为0
	int i,j;
	for(i = 0;i<m;i++){
		for(j = 0;j<n;j++) {
			if(maze[i][j]==2) maze[i][j]=0;
		}
	}
}
int main(){
	Stack stack;
	Stack *s = &stack;
	createMaze();
	s = Initialize(s);
	walk(s);
	if(s->top==-1)printf("该迷宫没有出路！");
	turnTwoToOne();
	printWalkLine(s);
	printf("输出路径：\n");
	printRoute(s);
	printf("\n路径长度为：%d",s->top+1);
	return 0;
}
```
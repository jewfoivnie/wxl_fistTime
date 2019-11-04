```c
//农夫过河
#include<stdio.h>
#include<stdlib.h>
typedef int Datatype;
typedef struct Queue{
	Datatype location[17];
	int f;
	int r;
}SeqQueue;
typedef SeqQueue *pSeqQueue;

void initialize(pSeqQueue p){//初始化队列
	p->f = 0;p->r = 0;
}
int isEmptyQueue(pSeqQueue p){//判断队列是否为空
	if(p->f==p->r)return 1;
	return 0;
}
int isFullQueue(pSeqQueue p){//判断队列是否为满
	if(p->r==p->f-1)return 1;
	return 0;
}
void inQueue(pSeqQueue p,int location){//进队列
	if(isFullQueue(p))return;
	p->location[p->r] = location;
	p->r++;
}
int outQueue(pSeqQueue p){//出队列
	if(isEmptyQueue(p))return -1;
	int loc = p->location[p->f];
	p->f++;
	return loc;
}

int farmer(int location){//如果是北岸就返回1，南岸返回0
	return (0!=(location&0x08));
}
int wolf(int location){//如果是北岸就返回1，南岸返回0；
	return (0!=(location&0x04));
}
int cabbage(int location){
	return (0!=(location&0x02));
}
int goat(int location){
	return (0!=(location&0x01));
}
int safe(int location){//判断安全状态
	if((goat(location)==cabbage(location))&&(goat(location)!=farmer(location))) return 0;//羊吃白菜
	if((goat(location)==wolf(location))&&(goat(location)!=farmer(location))) return 0;//狼吃羊
	return 1;
}
void character(int a[16],int i){//用文字形式表示出来
	if((farmer(a[i])==wolf(a[i]))&&(wolf(a[i])!=wolf(a[i-1]))){//如果当前状态下狼和农夫在同一岸且狼当前位置与前一状态下的位置不同
		if(farmer(a[i]))printf("农夫带着狼到了北岸\n");//在上述前提下，如果农夫在北岸，则农夫一定带着狼去的
		else printf("农夫带着狼到了南岸\n");//上一个if语句不满足，则农夫带着狼到了南岸
	}
	else if((farmer(a[i])==cabbage(a[i]))&&(cabbage(a[i])!=cabbage(a[i-1]))){
		if(farmer(a[i]))printf("农夫带着白菜到了北岸\n");
		else printf("农夫带着白菜到了南岸\n");
	}
	else if((farmer(a[i])==goat(a[i]))&&(goat(a[i])!=goat(a[i-1]))){
		if(farmer(a[i]))printf("农夫带着羊到了北岸\n");
		else printf("农夫带着羊到了南岸\n");
	}
	else {//以上条件都不满足，则农夫单独行动
		if(farmer(a[i]))printf("农夫独自到达北岸\n");
		else printf("农夫独自到达南岸\n");
	}
}
void tenToTwo(int n){//将十进制转化为二进制输出
	int mo[4];
	int temp;
	int i = 0;
	int j;
	for(;i<4;i++){
		mo[i] = n%2;
		n = (n-mo[i])/2;
		if(n==0) break;
	}
	temp = 3-i;
	if(i!=3){
		while(temp!=0){
			printf("0");
			temp--;
		}
	}
	for(j = i;j>=0;j--){
		printf("%d",mo[j]);
	}
	printf("\t\t");
}

void translate(int a[]){//将队列里的内容翻译成人能懂的语言方式
	printf("二进制形式：\t文字形式：\n");
	int i,j;
	int seq[16];
	for(i = 15,j = 15;i>=0;i = a[i],j--){
		seq[j] = i;
		if(i==0)break;
	}
	for(i = j;i<16;i++){
		tenToTwo(seq[i]);
		if(i>j) character(seq,i);
		else printf("开始时农夫和所有物品都在南岸\n");
	}
	return;
}
void crossRiver(pSeqQueue p){//农夫过河
	int i,mover;
	int location = 0,newlocation = 0;
	int route[16];
	inQueue(p,0x00);//初始状态入队
	for(i = 0;i<16;i++)route[i] = -1;
	route[0] = 0;
	while(!isEmptyQueue(p)&&(route[15]==-1)){
		location = outQueue(p);//取队头作为当前位置
		for(mover = 1;mover<=8;mover<<=1){
			if((0!=(location&0x08))==(0!=(location&mover))){//农夫和移动的物体在同一岸边
			newlocation = location^(0x08|mover);//计算新的状态
			if(safe(newlocation)&&route[newlocation]==-1){
				route[newlocation] = location;
				inQueue(p,newlocation);
			}
			}
		}
	}
	if(route[15] != -1){
		translate(route);
	}
	else printf("问题无解");
}
int main(){
	SeqQueue queue;
	pSeqQueue p = &queue;
	initialize(p);
	crossRiver(p);
	return 0 ;
}
```
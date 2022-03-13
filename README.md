# ZOHO
[brick_game.txt](https://github.com/kedarpotadar/ZOHO/files/8239543/brick_game.txt)
#include <stdio.h>
#include <conio.h>
#include <process.h>
#include <dos.h>
#include <stdlib.h>
#include <graphics.h>
#include <ctype.h>
 
#define NULL 0
#define YES 1
#define NO 0
 
int maxx, maxy, midx, midy;
int bri[5][20];
 
void music(int);
char mainscreen();
void drawbrick(int, int);
void erasebrick(int, int);
void bricks(void);
 
 
int main() {
	union REGS ii, oo;
	int ballx, bally, paddlex;
	int paddley, dx = 1, dy = -1, oldx, oldy;
	int gm = CGAHI, gd = CGA, playerlevel;
	int i, flag = 0, speed = 25;
	int welldone = NO, score = 0, chance = 4, area;
	int layer[5] = {
	   10,
	   20,
	   30,
	   40,
	   50
	}, limit = 50, currentlayer = 4;
	char* p1, *p2;
 
 
	initgraph(&gd, &gm, "C:\\TURBOC3\\BGI");
  
	maxx = getmaxx();
	maxy = getmaxy();
 
	 
	midx = maxx / 2;
	midy = maxy / 2;
 
	
	playerlevel = mainscreen();
 
	
	switch (playerlevel) {
	case 'A':
	case 'a':
		speed = 15;
		break;
 
	case 'E':
	case 'e':
		speed = 10;
	}
 
	 
	rectangle(0, 0, maxx, maxy - 12);
	bricks();
	rectangle(midx - 25, maxy - 7 - 12, midx + 25, maxy - 12);
	floodfill(midx, maxy - 1 - 12, 1);
	circle(midx, maxy - 13 - 12, 12);
	floodfill(midx, maxy - 10 - 12, 1);
 
	 
	area = imagesize(midx - 12, maxy - 18, midx + 12, maxy - 8);
	p1 = (char*)malloc(area);
 
	 
	area = imagesize(midx - 25, maxy - 7, midx + 25, maxy - 1);
	p2 = (char*)malloc(area);
 
	 
	if (p1 == NULL || p2 == NULL) {
		puts("Insufficient memory!!");
		exit(1);
	}
  
	
	getimage(midx - 12, maxy - 7 - 12 - 12 + 1, midx + 12, maxy - 8 - 12, p1);
	getimage(midx - 25, maxy - 7 - 12, midx + 25, maxy - 1 - 12, p2);
 
	 
	paddlex = midx - 25;
	paddley = maxy - 7 - 12;
	ballx = midx - 12;
	bally = maxy - 7 - 12 + 1 - 12;
 
	 
	gotoxy(45, 25);
	printf("Balls Remaining: ");
	for (i = 0; i < 3; i++) {
		circle(515 + i * 35, maxy - 5, 12);
		floodfill(515 + i * 35, maxy - 5, 1);
	}  
	gotoxy(1, 25);
	
	printf("Your Score: %4d", score);
	settextjustify(CENTER_TEXT, CENTER_TEXT);
	settextstyle(SANS_SERIF_FONT, HORIZ_DIR, 4);
	while (1) {
		
		flag = 0;
		oldx = ballx;
		 
		oldy = bally;
		ballx = ballx + dx; 
		bally = bally + dy;
		if (bally > 40) {
		limit = 50;
		currentlayer = 4;
		}
		else {
		if (bally > 30) {
		limit = 40;
		currentlayer = 3;
		}
		else {
		if (bally > 20) {
		limit = 30;
		currentlayer = 2;
		}
		else {
		if (bally > 10) {
		limit = 20;
		currentlayer = 1;
		}
		else {
		limit = 10;
		currentlayer = 0;
		}
		}
		}
		}
 
	 
		if (ballx < 1) {
		music(5);
		ballx = 1;
		dx = -dx;
		}  
		if (ballx > (maxx - 24 - 1)) {
		music(5);
		ballx = maxx - 24 - 1;
		dx = -dx;
		}
 

		if (bally < 1) {
		music(5);
		bally = 1;
		dy = -dy;
		} 
		if (bally < limit) {
		
		if (bri[currentlayer][(ballx + 10) / 32] == 1) {
		 
		for (i = 1; i <= 6; i++) {
		 
		if (bri[currentlayer][(ballx + i + 10) / 32] == 0) {
		 
		ballx = ballx + i;
		flag = 1;
		break;
		} 
		if (bri[currentlayer][(ballx - i + 10) / 32] == 0) {
		ballx = ballx - i;
		flag = 1;
		break;
		}
		} 
		if (!flag) {
		
		if (bally < layer[currentlayer - 1]) {
		
		currentlayer--;
		limit = layer[currentlayer];
		}
		putimage(oldx, oldy, p1, OR_PUT);
		
		putimage(oldx, oldy, p1, XOR_PUT);
		
		putimage(ballx, bally, p1, XOR_PUT);
		delay(speed); 
		continue;
		}
		}  
		music(4); 
		
		erasebrick((ballx + 10) / 32, currentlayer);
		
		if ((ballx + 10) / 32 == 19) line(maxx, 0, maxx, 50);
		
		if ((ballx + 10) / 32 == 0) line(0, 0, 0, 50);
		
		element to 1 to indicate absence of brick 
		if (currentlayer == 0) line(0, 0, maxx, 0);
		bri[currentlayer][(ballx + 10) / 32] = 1;
		bally = bally + 1; 
		dy = -dy;  
		score += 5; 
		gotoxy(16, 25);
		
		
		printf("%4d", score);
 
		if (welldone == NO) welldone = YES;
		else {
		
		outtextxy(midx, midy, "Well done!");
		music(1);
		}
		} 
		if (bally > 50 && welldone == YES) {
	setviewport(midx - 32 * 2.5, midy - 32 / 2, midx + 32 * 2.5, midy + 32 / 2, 1);
		clearviewport();
		setviewport(0, 0, maxx, maxy, 1);
		welldone = NO;
		}
 
		
		if (bally > 180 - 12) {
		welldone = NO;
 
		if (ballx < paddlex - 20 || ballx > paddlex + 50) {
		
		while (bally < 177) {
		
		putimage(oldx, oldy, p1, XOR_PUT);
		putimage(ballx, bally, p1, XOR_PUT);
		delay(speed); 
		oldx = ballx;
		
		oldy = bally;
		ballx = ballx + dx;
		bally = bally + dy;
		}
		chance--; 
		score -= 20;
		gotoxy(16, 25);
		printf("%4d", score);
		music(2); 
		
		if (chance) putimage(515 + (chance - 1) * 35 - 12, maxy - 10, p1, XOR_PUT);
		if (chance == 1) {
		gotoxy(45, 25);
		printf("Your last ball... Be careful!");
		}
		if (!chance) {
		gotoxy(45, 25);
		printf("Press any key... ");
		outtextxy(midx, midy, "I warned you! Try again");
		music(3);
		closegraph();
		restorecrtmode();
		exit(0);
		}
		} 
		music(5);
		bally = 180 - 12; 
		dy = -dy;
		} 
		
		putimage(oldx, oldy, p1, OR_PUT); 
		
		putimage(oldx, oldy, p1, XOR_PUT); 
		
		putimage(ballx, bally, p1, XOR_PUT); 
		if (score == 500 - ((4 - chance) * 20)) {
		outtextxy(midx, midy, "You win !!!");
		if (score < 500) outtextxy(midx, midy + 30, "Try scoring 500");
		else outtextxy(midx, midy + 30, "You are simply GREAT!");
		music(3);
		closegraph();
		restorecrtmode();
		exit(0);
		} 
		delay(speed); 
		if (kbhit()) {
		
		ii.h.ah = 0;
		
		int86(22, &ii, &oo); 
		
		putimage(paddlex, paddley, p2, OR_PUT);
		
		putimage(paddlex, paddley, p2, XOR_PUT); 
		if (oo.h.ah == 1) exit(0); // right arrow key 
		
		if (oo.h.ah == 75) paddlex = paddlex - 20; 
		
		if (oo.h.ah == 77) paddlex = paddlex + 20;
		
		if (paddlex < 0) paddlex = 0; 
		if (paddlex > 589)
		paddlex = 589;
 
		
		putimage(paddlex, paddley, p2, XOR_PUT);
		}
	}
}
 

char mainscreen() {
	
	int ff[12][40] = {
	1,1,1,1,0,0,0,1,1,1,1,0,0,0,1,1,1,1,1,0,
	0,0,1,1,1,0,0,1,0,0,0,0,1,0,0,0,1,1,1,0,
	1,0,0,0,1,0,0,1,0,0,0,1,0,0,0,0,1,0,0,0,
	0,1,0,0,0,1,0,1,0,0,0,1,0,0,0,1,0,0,0,1,
	1,0,0,0,0,1,0,1,0,0,0,0,1,0,0,0,1,0,0,0,
	1,0,0,0,0,0,0,1,0,0,1,0,0,0,1,0,0,0,0,0,
	1,0,0,0,0,1,0,1,0,0,0,0,1,0,0,0,1,0,0,0,
	1,0,0,0,0,0,0,1,0,1,0,0,0,0,1,0,0,0,0,0,
	1,0,0,0,1,0,0,1,0,0,0,1,0,0,0,0,1,0,0,0,
	1,0,0,0,0,0,0,1,1,0,0,0,0,0,1,0,0,0,0,0,
	1,1,1,1,0,0,0,1,1,1,1,0,0,0,0,0,1,0,0,0,
	1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,1,1,1,0,0,
	1,0,0,0,1,0,0,1,0,0,0,1,0,0,0,0,1,0,0,0,
	1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,0,0,0,1,0,
	1,0,0,0,0,1,0,1,0,0,0,0,1,0,0,0,1,0,0,0,
	1,0,0,0,0,0,0,1,0,1,0,0,0,0,0,0,0,0,0,1,
	1,0,0,0,0,1,0,1,0,0,0,0,1,0,0,0,1,0,0,0,
	1,0,0,0,0,0,0,1,0,0,1,0,0,0,0,0,0,0,0,1,
	1,0,0,0,1,0,0,1,0,0,0,0,1,0,0,0,1,0,0,0,
	0,1,0,0,0,1,0,1,0,0,0,1,0,0,1,0,0,0,0,1,
	1,1,1,1,0,0,0,1,0,0,0,0,1,0,1,1,1,1,1,0,
	0,0,1,1,1,0,0,1,0,0,0,0,1,0,0,1,1,1,1,0,
	0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
	0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
	};
	int i, j, lx = 0, ly = 0, ch;
 
	
	rectangle(0, 0, maxx, maxy);
 
	
	for (i = 0; i < 12; i++) {
		for (j = 0; j < 40; j++) {
		if (ff[i][j]) rectangle(lx, ly, lx + 15, ly + 9);
		lx = lx + 16;
		}
		lx = 0;
		ly = ly + 10;
	} 
	line(0, maxy - 12, maxx, maxy - 12);
	setfillstyle(XHATCH_FILL, WHITE);
	
	floodfill(2, maxy - 2, WHITE);
	setfillstyle(SOLID_FILL, WHITE);
	rectangle(midx - 25, maxy - 7 - 12, midx + 25, maxy - 12);
	floodfill(midx, maxy - 1 - 12, 1);
	circle(midx, maxy - 13 - 12, 12);
	floodfill(midx, maxy - 10 - 12, 1);
	music(3); 
	
	while (1) {
		
		setviewport(1, 125 - 12, maxx - 1, maxy - 1, 1);
		clearviewport();
		setviewport(0, 0, maxx, maxy, 1);
		outtextxy(20, 135, "Select any of the following:");
		outtextxy(20, 155, "Play ( P )");
		outtextxy(20, 165, "Instructions ( I )");
		outtextxy(20, 175, "Exit ( E )");
		ch = 0; // continue till the correct choice is made 
		while (!(ch == 'E' || ch == 'I' || ch == 'P')) {
		
		fflush(stdin);
		if ((ch = getch()) == 0) getch();
		else ch = toupper(ch);
		}
		if (ch == 'P') break;
		switch (ch) {
		case 'I':
		setviewport(1, 125 - 12, maxx - 1, maxy - 1, 1);
		clearviewport();
		setviewport(0, 0, maxx, maxy, 1);
		settextstyle(DEFAULT_FONT, HORIZ_DIR, 1);
		outtextxy(20, 125, " Instructions ");
		settextstyle(DEFAULT_FONT, HORIZ_DIR, 0);
		outtextxy(20, 140, "Use left and right arrow keys to move paddle.");
		outtextxy(20, 150, "If you don't collect the ball on the paddle, you lose the ball.");
		outtextxy(20, 160, "On loosing a ball you loose 20 points.");
		outtextxy(20, 170, "On taking a brick you gain 5 points.");
		outtextxy(20, 185, "Press any key...");
		fflush(stdin);
		if (getch() == 0) getch();
		break;
		case 'E':
		closegraph();
		restorecrtmode();
		exit(0);
		}
	}
	setviewport(1, 125 - 12, maxx - 1, maxy - 1, 1);
	clearviewport(); 
	setviewport(0, 0, maxx, maxy, 1);
	outtextxy(20, 135, "Select any of the following levels:");
	outtextxy(20, 155, "Novice ( N )");
	outtextxy(20, 165, "Advanced ( A )");
	outtextxy(20, 175, "Expert ( E )"); 
	fflush(stdin);
	if ((ch = getch()) == 0) getch();
	clearviewport(); 
	return (ch);
}
 

void bricks() {
	int i, j, lx = 0, ly = 0;
	for (i = 0; i < 5; i++) // 5 rows  {
		for (j = 0; j < 20; j++) // 20 columns  {
		
		drawbrick(lx, ly);
		lx = lx + 32;
		}
		lx = 0;
		ly = ly + 10;
	}
}
 

void drawbrick(int lx, int ly) {
	rectangle(lx, ly, lx + 31, ly + 9);
	rectangle(lx + 2, ly - 2, lx + 31 - 2, ly + 9 - 2);
	floodfill(lx + 1, ly + 1, 2);
}
 
 
void erasebrick(int b, int l) {
	
	setcolor(BLACK);
	rectangle(b * 32, l * 10, (b * 32) + 31, (l * 10) + 9);
	rectangle(b * 32 + 1, l * 10, (b * 32) + 31 - 1, (l * 10) + 9 - 1);
	rectangle(b * 32 + 2, l * 10, (b * 32) + 31 - 2, (l * 10) + 9 - 2);
	setcolor(WHITE);
}
 

void music(int type) {
	
	float octave[7] = {
	   130.81,
	   146.83,
	   164.81,
	   174.61,
	   196,
	   220,
	   246.94
	};
	int n, i;
	switch (type) {
	case 1:
		for (i = 0; i < 7; i++) {
		sound(octave[i] * 8);
		delay(30);
		}
		nosound();
		break;
	case 2:
		for (i = 0; i < 15; i++) {
		n = random(7);
		sound(octave[n] * 4);
		delay(100);
		}
		nosound();
		break;
	case 3:
		while (!kbhit()) {
		n = random(7);
		sound(octave[n] * 4);
		delay(100);
		}
		nosound(); // flush the keyboard buffer 
		if (getch() == 0) getch();
		break;
	case 4:
		for (i = 4; i >= 0; i--) {
		sound(octave[i] * 4);
		delay(15);
		}
		nosound();
		break;
 
	case 5:
		sound(octave[6] * 2);
		delay(50);
		nosound();
	}
}

#include <stdlib.h>
#include <gl/glut.h>
#include <math.h>
#include "glaux.h"
#include <iostream>
#include <ctime>
#pragma comment (lib, "glaux.lib")


struct PLAYER2;

int  w = 600, h = 400;
int counter = 0, f_counter = 0, d_counter=0 ;
AUX_RGBImageRec *MyImage;

float gravity = 1.45;
float drag = 0.8;

struct SHTAB
{
	AUX_RGBImageRec * image;

	int x = 300, y = 200;

	bool hitpoint = true;

	void Draw()
	{
		glRasterPos2d(x, y);
		glDrawPixels(
			image->sizeX,
			image->sizeY,
			GL_RGB,
			GL_UNSIGNED_BYTE,
			image->data);
	}

}Shtab;

struct Wall
{
	AUX_RGBImageRec * image2;
	int x = 200;
	int y = 150;
	void Draw()
	{
		glRasterPos2d(x, y);
		glDrawPixels(
			image2->sizeX,
			image2->sizeY,
			GL_RGB,
			GL_UNSIGNED_BYTE,
			image2->data);
	}


}wall;

struct BALL
{
	int x = 300, y = 400;
	float dx = -5, dy = 0;
	bool Up, Down, Left, Right, visible;
	AUX_RGBImageRec * image;

	void move(SHTAB & hp, Wall &wall)
	{

		if (Up && visible)
		{
			y += 25;
			if (y >= h - 40)
			{
				visible = false;
			}
			if (y>wall.y - 40 && y<wall.y && x>wall.x - 40 && x<wall.x + 40)
			{
				visible = false;
			}
			if (x + 39 >= hp.x && x <= hp.x && y > hp.y)
			{
				
				hp.hitpoint = false;
				visible = false;
			}

			
		}
		if (Down && visible)
		{
			y -= 25;
			if (y <= 0)
			{
				visible = false;
			}
			if (y < wall.y + 40 && y>wall.y && x > wall.x - 40 && x<wall.x + 40)
			{
				visible = false;
			}
			if (x+40>=hp.x && x<hp.x+40 && y<=hp.y )
			{

				hp.hitpoint = false;
				visible = false;
			}
		}

		if (Right && visible)
		{
			x += 25;
			if (x >= w - 40)
			{
				visible = false;
			}
			if (x>wall.x - 40 && x<wall.x && y>wall.y - 40 && y<wall.y + 40)
			{
				visible = false;
			}
			if (y + 39 >= hp.y && y <= hp.y && x > hp.x)
			{

				hp.hitpoint = false;
				visible = false;
			}
		
		}
		if (Left && visible)
		{
			x -= 25;
			if (x <= 0)
			{
				visible = false;
			}
			if (x < wall.x + 40 && x>wall.x && y > wall.y - 40 && y<wall.y + 40)
			{
				visible = false;
			}
			if (y + 40 >= hp.y && y<hp.y + 40 && x <= hp.x)
			{

				hp.hitpoint = false;
				visible = false;
			}
		}

		if (x < 0)
		{
			visible = false;
		}
		if (x > w - 40)
		{
			visible = false;
		}
		if (y < 0)
		{
			visible = false;
		}
		if (y > h - 40)
		{
			visible = false;
		}

	}

	void Draw()
	{
		if (visible)
		{
			glRasterPos2d(x, y);
			glDrawPixels(
				image->sizeX,
				image->sizeY,
				GL_RGB,
				GL_UNSIGNED_BYTE,
				image->data);
		}
	}
} ball;


struct PLAYER
{
	int x = 300, y = 90;
	int cx = x + 20;
	int cy = y + 20;
	float dx = -10, dy = 0;
	BALL ball;
	bool Right, Left, Up, Down, run;

	bool player=true;

	AUX_RGBImageRec * image;


	void  setDirection()
	{
		int i =  rand() % 4;
		Up = false;
		Down = false;
		Right = false;
		Left = false;
		run = true;

		if(x<=0)
		{
			i = 2;
			while(i==3)
			i = rand() % 4;
		}

		if (x >= w-40 )
		{
			i = 3;
			while (i == 2)
				i = rand() % 4;
		}
		if (y <= 0)
		{
			i = 0;
			while (i == 1)
				i = rand() % 4;
		}
		if (y >= h-40 )
		{
			i = 1;
			while (i == 0)
				i = rand() % 4;
		}
		switch (i)
		{
		case 0: Up = true; break;
		case 1: Down = true; break;
		case 2: Right = true; break;
		case 3: Left = true; break;
		}
	
	}

	void move(PLAYER &p1, PLAYER2 &p2);
	/*{
	
		if (Right && run)
		{
			
			x += 25;
			if (x >= w - 40)
			{
				x = w - 40;
				if (player == false)
				setDirection();
			}
			if (x+40 > wall.x && x  < wall.x + 40 && y + 40 > wall.y  && y<wall.y + 40)
			{
				x = wall.x-40 ;
			}

			if (p1.x + 40 > p2.x && p1.x  < p2.x + 40 && p1.y + 40 > p2.y  && p1.y<p2.y + 40)
			{
				p1.x = p2.x - 40;
				p2.x = p1.x + 40;


			}

			

		}

		if (Left && run)
		{
			if (x + 40 == wall.x)
			{
				x = wall.x - 40;
			}
		
			x -= 25;
			if (x <= 0 )
			{
				x = 0;
				if (player == false)
				setDirection();
			}
			if (x < wall.x+40  && x+40 > wall.x+40 && y+40 > wall.y  && y<wall.y + 40)
			{
				x = wall.x+40 ;
			}
			
			if (p1.x < p2.x + 40 && p1.x + 40 > p2.x + 40 && p1.y + 40 > p2.y  && p1.y<p2.y + 40)
			{
				p1.x = p2.x + 40;
				p2.x = p1.x - 40;
			}
		}


		if (Up && run)
		{
			
			y += 25;
			if (y >= h - 40)
			{
				y = h - 40;
				if (player == false)
				setDirection();
				
			}
			
			if (y>wall.y-40 && y<wall.y && x>wall.x-40 && x<wall.x+40)
			{
				y = wall.y - 40;
			}

			if (p1.y>p2.y - 40 && p1.y<p2.y && p1.x>p2.x - 40 &&p1.x<p2.x + 40)
			{
				p1.y = p2.y - 40;
				p2.y = p1.y + 40;
			}
		}
	

		if (Down && run)
		{
	
			y -=25;
			if (y <= 0 )
			{
				y = 0;
				if(player == false)
				setDirection();
			}
			if (y < wall.y + 40 && y>wall.y && x > wall.x - 40 && x<wall.x + 40)
			{
				y = wall.y + 40;
			}

			if (p1.y < p2.y + 40 && p1.y>p2.y && p1.x > p2.x - 40 && p1.x<p2.x + 40)
			{
				p1.y = p2.y + 40;
				p2.y = p1.y - 40;
			}
		}


	}*/

	void Draw()
	{
		glRasterPos2d(x, y);
		glDrawPixels(
			image->sizeX,
			image->sizeY,
			GL_RGB,
			GL_UNSIGNED_BYTE,
			image->data);
	}
} pl1,pl2; 

struct PLAYER2
{
	int x = 100, y = 90;
	int cx = x + 20;
	int cy = y + 20;
	float dx = -10, dy = 0;
	BALL ball;
	bool Right, Left, Up, Down, run;

	bool player=false;

	AUX_RGBImageRec * image;


	

	void move2(PLAYER2 &p1, PLAYER &p2)
	{

		if (Right && run)
		{

			x += 25;
			if (x >= w - 40)
			{
				x = w - 40;
			
			}
			if (x + 40 > wall.x && x  < wall.x + 40 && y + 40 > wall.y  && y<wall.y + 40)
			{
				x = wall.x - 40;
			}

			if (p1.x + 40 > p2.x && p1.x  < p2.x + 40 && p1.y + 40 > p2.y  && p1.y<p2.y + 40)
			{
				p1.x = p2.x - 40;
				p2.x = p1.x + 40;


			}



		}

		if (Left && run)
		{
			if (x + 40 == wall.x)
			{
				x = wall.x - 40;
			}

			x -= 25;
			if (x <= 0)
			{
				x = 0;
			
			}
			if (x < wall.x + 40 && x + 40 > wall.x + 40 && y + 40 > wall.y  && y<wall.y + 40)
			{
				x = wall.x + 40;
			}

			if (p1.x < p2.x + 40 && p1.x + 40 > p2.x + 40 && p1.y + 40 > p2.y  && p1.y<p2.y + 40)
			{
				p1.x = p2.x + 40;
				p2.x = p1.x - 40;
			}
		}


		if (Up && run)
		{

			y += 25;
			if (y >= h - 40)
			{
				y = h - 40;
			

			}

			if (y>wall.y - 40 && y<wall.y && x>wall.x - 40 && x<wall.x + 40)
			{
				y = wall.y - 40;
			}

			if (p1.y>p2.y - 40 && p1.y<p2.y && p1.x>p2.x - 40 && p1.x<p2.x + 40)
			{
				p1.y = p2.y - 40;
				p2.y = p1.y + 40;
			}
		}


		if (Down && run)
		{

			y -= 25;
			if (y <= 0)
			{
				y = 0;
		
			}
			if (y < wall.y + 40 && y>wall.y && x > wall.x - 40 && x<wall.x + 40)
			{
				y = wall.y + 40;
			}

			if (p1.y < p2.y + 40 && p1.y>p2.y && p1.x > p2.x - 40 && p1.x<p2.x + 40)
			{
				p1.y = p2.y + 40;
				p2.y = p1.y - 40;
			}
		}


	}

	void Draw()
	{
		glRasterPos2d(x, y);
		glDrawPixels(
			image->sizeX,
			image->sizeY,
			GL_RGB,
			GL_UNSIGNED_BYTE,
			image->data);
	}
} pl;

void PLAYER:: move(PLAYER &p1, PLAYER2 &p2)
{

	if (Right && run)
	{

		x += 25;
		if (x >= w - 40)
		{
			x = w - 40;
			if (player == false)
				setDirection();
		}
		if (x + 40 > wall.x && x  < wall.x + 40 && y + 40 > wall.y  && y<wall.y + 40)
		{
			x = wall.x - 40;
		}

		if (p1.x + 40 > p2.x && p1.x  < p2.x + 40 && p1.y + 40 > p2.y  && p1.y<p2.y + 40)
		{
			p1.x = p2.x - 40;
			p2.x = p1.x + 40;


		}



	}

	if (Left && run)
	{
		if (x + 40 == wall.x)
		{
			x = wall.x - 40;
		}

		x -= 25;
		if (x <= 0)
		{
			x = 0;
			if (player == false)
				setDirection();
		}
		if (x < wall.x + 40 && x + 40 > wall.x + 40 && y + 40 > wall.y  && y<wall.y + 40)
		{
			x = wall.x + 40;
		}

		if (p1.x < p2.x + 40 && p1.x + 40 > p2.x + 40 && p1.y + 40 > p2.y  && p1.y<p2.y + 40)
		{
			p1.x = p2.x + 40;
			p2.x = p1.x - 40;
		}
	}


	if (Up && run)
	{

		y += 25;
		if (y >= h - 40)
		{
			y = h - 40;
			if (player == false)
				setDirection();

		}

		if (y>wall.y - 40 && y<wall.y && x>wall.x - 40 && x<wall.x + 40)
		{
			y = wall.y - 40;
		}

		if (p1.y>p2.y - 40 && p1.y<p2.y && p1.x>p2.x - 40 && p1.x<p2.x + 40)
		{
			p1.y = p2.y - 40;
			p2.y = p1.y + 40;
		}
	}


	if (Down && run)
	{

		y -= 25;
		if (y <= 0)
		{
			y = 0;
			if (player == false)
				setDirection();
		}
		if (y < wall.y + 40 && y>wall.y && x > wall.x - 40 && x<wall.x + 40)
		{
			y = wall.y + 40;
		}

		if (p1.y < p2.y + 40 && p1.y>p2.y && p1.x > p2.x - 40 && p1.x<p2.x + 40)
		{
			p1.y = p2.y + 40;
			p2.y = p1.y - 40;
		}
	}


}

void Fire(BALL &ball, PLAYER &pl2)
{
	ball.x = pl2.x;
	ball.y = pl2.y;
	ball.visible = true;
	ball.Up = pl2.Up;
	ball.Down = pl2.Down;
	ball.Right = pl2.Right;
	ball.Left = pl2.Left;
}


void DrawField()
{

	glRasterPos2d(0, 0);
	glDrawPixels(
		MyImage->sizeX,
		MyImage->sizeY,
		GL_RGB,
		GL_UNSIGNED_BYTE,
		MyImage->data);

	glColor3f(0.0, 0.0, 0.0);
}

void Tick()
{
	pl1.ball.move(Shtab, wall);
	pl2.move( pl2, pl);
	pl.move2(pl, pl2);
	pl2.ball.move(Shtab, wall);
	pl.ball.move(Shtab, wall);
	double Dx = pl1.ball.x - pl1.x;
	double Dy = pl1.ball.y - pl1.y;

	double d = sqrt(Dx*Dx + Dy * Dy);

	int r1 = 30;
	int r2 = 30;

	if (d < r1 + r2)
	{
		double ax = Dx / d;
		double ay = Dy / d;

		double Vn1 = pl1.dx*ax + pl1.dy*ay;
		double Vt1 = -pl1.dx*ay + pl1.dy*ax;

		double Vn2 = ball.dx*ax + ball.dy*ay;
		double Vt2 = -ball.dx*ay + ball.dy*ax;

		Vn2 = Vn1 - Vn2;

		ball.dx = Vn2 * ax - Vt2 * ay;
		ball.dy = Vn2 * ay + Vt2 * ax;

	}

}



void MyKeyboard(unsigned char key, int a, int b)
{
	
	switch (key)
	{
	case 'w':
		
		pl2.Up = true;
		pl2.Down = false;
		pl2.Left = false;
		pl2.Right = false;
		pl2.run = true;
		break;

	case 'd':
	
		pl2.Up = false;
		pl2.Down = false;
		pl2.Left = false;
		pl2.Right = true;
		pl2.run = true;
		break;

	case 'a':
	
		pl2.Up = false;
		pl2.Down = false;
		pl2.Left = true;
		pl2.Right = false;
		pl2.run = true;
		break;

	case 's':
	
		pl2.Up = false;
		pl2.Down = true;
		pl2.Left = false;
		pl2.Right = false;
		pl2.run = true;
		break;

	case '8':
		pl.Up = true;
		pl.Down = false;
		pl.Left = false;
		pl.Right = false;
		pl.run = true;
		break;

	case '6':

		pl.Up = false;
		pl.Down = false;
		pl.Left = false;
		pl.Right = true;
		pl.run = true;
		break;

	case '4':

		pl.Up = false;
		pl.Down = false;
		pl.Left = true;
		pl.Right = false;
		pl.run = true;
		break;

	case '5':

		pl.Up = false;
		pl.Down = true;
		pl.Left = false;
		pl.Right = false;
		pl.run = true;
		break;
	case ' ':
		ball.visible = false; std::cout << Shtab.hitpoint << " ";
		break;

	

	}
}

void MyKeyboardUp(unsigned char key, int a, int b)
{

	switch (key)
	{

	case 'w':
	
		pl2.Up = true;
		pl2.Down = false;
		pl2.Left = false;
		pl2.Right = false;
		pl2.run = false;
		break;

	case 'd':
	
		pl2.Up = false;
		pl2.Down = false;
		pl2.Left = false;
		pl2.Right = true;
		pl2.run = false;
		break;

	case 'a':
	
		pl2.Up = false;
		pl2.Down = false;
		pl2.Left = true;
		pl2.Right = false;
		pl2.run = false;
		break;

	case 's':
	
		pl2.Up = false;
		pl2.Down = true;
		pl2.Left = false;
		pl2.Right = false;
		pl2.run = false;
		break;

	case '8':

		pl.Up = true;
		pl.Down = false;
		pl.Left = false;
		pl.Right = false;
		pl.run = false;
		break;

	case '6':

		pl.Up = false;
		pl.Down = false;
		pl.Left = false;
		pl.Right = true;
		pl.run = false;
		break;

	case '4':

		pl.Up = false;
		pl.Down = false;
		pl.Left = true;
		pl.Right = false;
		pl.run = false;
		break;

	case '5':

		pl.Up = false;
		pl.Down = true;
		pl.Left = false;
		pl.Right = false;
		pl.run = false;
		break;
	case' ':
		pl2.ball.x = pl2.x;
		pl2.ball.y = pl2.y;
		pl2.ball.Up = pl2.Up;
		pl2.ball.Down = pl2.Down;
		pl2.ball.Right = pl2.Right;
		pl2.ball.Left = pl2.Left;
		pl2.ball.visible = true;
		break;

	case'0':
		pl.ball.x = pl.x;
		pl.ball.y = pl.y;
		pl.ball.Up = pl.Up;
		pl.ball.Down = pl.Down;
		pl.ball.Right = pl.Right;
		pl.ball.Left = pl.Left;
		pl.ball.visible = true;
		break;
	}

}




void display()
{
	glClear(GL_COLOR_BUFFER_BIT);
	DrawField();
	if (Shtab.hitpoint == true)
	{
		Shtab.Draw();
	}
	else
	{
	
		system("pause");
	}
	
	wall.Draw();
	ball.Draw();
	//pl1.ball.Draw();
	pl.Draw();
	pl.ball.Draw();
	pl2.ball.Draw();
	//pl1.Draw();
	pl2.Draw();
	glFlush();
}

void timer(int t = 0)
{
	counter++;
	f_counter++;
	d_counter ++;
	/*if (f_counter >= 40)
	{
		Fire(pl1.ball, pl1);
		f_counter = 0;
	}*/

	if (d_counter >= 30)
	{
		pl1.setDirection();
		d_counter = 0;
	}
	
	/*if (counter >= 5)
	{
	
		pl1.move( pl1, pl2);
		counter = 0;
	}*/
	display();
	Tick();
	glutTimerFunc(50, timer, 0);
}


void main(int argc, char *argv[])
{
	srand(time(NULL));
	int x = 300, y = 400;
	pl2.player = true;
	pl1.player = false;
	pl2.x = 200;
	pl2.y = 150;
	std::cout << "Simple game" << std::endl;

	MyImage = auxDIBImageLoad("background.bmp");
	ball.image = auxDIBImageLoad("shell.bmp");
	pl.image = auxDIBImageLoad("tank.bmp");
	pl2.image = auxDIBImageLoad("tank.bmp");
	pl2.ball.image= auxDIBImageLoad("shell.bmp");
	pl.ball.image = auxDIBImageLoad("shell.bmp");
	wall.image2 = auxDIBImageLoad("wall.bmp");
	Shtab.image = auxDIBImageLoad("box.bmp");
	glutInit(&argc, argv);
	if (Shtab.hitpoint == false)
	{
		std::cout << "NOOB" << std::endl;
		system("pause");
	}
	glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
	glutInitWindowSize(w, h);
	glutCreateWindow("SimpleGame");

	glMatrixMode(GL_PROJECTION);
	glClearColor(0.0, 0.0, 1.0, 1.0);
	glLoadIdentity();
	gluOrtho2D(0, w, 0, h);

	glutDisplayFunc(display);
	glutTimerFunc(50, timer, 0);

	glutKeyboardFunc(MyKeyboard);
	glutKeyboardUpFunc(MyKeyboardUp);
	
	glutMainLoop();
}






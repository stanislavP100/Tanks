//To vin the game you should hit the shtab first
//to move the tank use keys 'w' 's' 'a' 'd'
//to fire press 'spase'

#include <ctime>
#pragma comment (lib, "glaux.lib")


struct PLAYER2;

int  w = 600, h = 400;//width and higth of window
int counter = 0, f_counter = 0, d_counter=0 ;
AUX_RGBImageRec *MyImage;


struct SHTAB
{
	AUX_RGBImageRec * image;

	int x = 100, y = 350;//coordinates

	bool hitpoint = true;//shtab destroyed or not

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
	int x = 200;int y = 150;//coordinates
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
	int x, y;
//	float dx = -5, dy = 0;
	bool Up, Down, Left, Right, visible;
	AUX_RGBImageRec * image;

	void move(SHTAB & hp, Wall &wall)
	{

		if (Up && visible)
		{
			y += 15;
			if (y >= h - 40)
			{
				visible = false;
			}
			if (y>wall.y - 40 && y<wall.y && x>wall.x - 40 && x<wall.x + 40)
			{
				visible = false;
			}
			if (x + 39 >= hp.x && x <= hp.x && y > hp.y && y<hp.y+40)
			{
				
				hp.hitpoint = false;
				visible = false;
			}

			
		}
		if (Down && visible)
		{
			y -= 15;
			if (y <= 0)
			{
				visible = false;
			}
			if (y < wall.y + 40 && y>wall.y && x > wall.x - 40 && x<wall.x + 40)
			{
				visible = false;
			}
			if (x + 39 >= hp.x && x <= hp.x && y > hp.y && y<hp.y + 40)
			{

				hp.hitpoint = false;
				visible = false;
			}
		}

		if (Right && visible)
		{
			x += 15;
			if (x >= w - 40)
			{
				visible = false;
			}
			if (x>wall.x - 40 && x<wall.x && y>wall.y - 40 && y<wall.y + 40)
			{
				visible = false;
			}
			if (x>hp.x - 40 && x<hp.x && y>hp.y - 40 && y<hp.y + 40)
			{

				hp.hitpoint = false;
				visible = false;
			}
		
		}
		if (Left && visible)
		{
			x -= 15;
			if (x <= 0)
			{
				visible = false;
			}
			if (x<wall.x - 40 && x>wall.x && y>wall.y - 40 && y<wall.y + 40)
			{
				visible = false;
			}
			if (y + 39 >= hp.y && y <= hp.y && x > hp.x)
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

	void move(PLAYER &p1, PLAYER &p2)
	{
	
	
		if (Right && run)
		{
			
			x += 10;
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
		
			x -= 10;
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
			
			y += 10;
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
	
			y -=10;
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
} pl1,pl2; 





void Fire(BALL &ball, PLAYER &pl2)//tank fire
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
	pl2.move(pl2, pl1);
	pl1.move(pl1, pl2);
	pl2.ball.move(Shtab, wall);
	pl1.ball.move(Shtab, wall);
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

	case' ':
		pl2.ball.x = pl2.x;
		pl2.ball.y = pl2.y;
		pl2.ball.Up = pl2.Up;
		pl2.ball.Down = pl2.Down;
		pl2.ball.Right = pl2.Right;
		pl2.ball.Left = pl2.Left;
		pl2.ball.visible = true;
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
	pl1.ball.Draw();
	pl1.Draw();
	pl2.ball.Draw();
	pl2.Draw();
	glFlush();
}

void timer(int t = 0)
{
	counter++;
	f_counter++;
	d_counter ++;
	if (f_counter >= 40)
	{
		Fire(pl1.ball, pl1);
		f_counter = 0;
	}

	if (d_counter >= 20)
	{
		pl1.setDirection();
		d_counter = 0;
	}
	
	if (counter >= 25)
	{
	
		pl1.move( pl1, pl2);
		counter = 0;
	}
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
	pl1.image = auxDIBImageLoad("tank.bmp");
	pl2.image = auxDIBImageLoad("tank.bmp");
	pl2.ball.image= auxDIBImageLoad("shell.bmp");
	pl1.ball.image = auxDIBImageLoad("shell.bmp");
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
	
	glutMainLoop() ;
}






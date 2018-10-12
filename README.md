# breakout
my version control code for breakout
import acm.graphics.*;

import acm.program.*;
import acm.util.*;
import acmx.export.javax.swing.JButton;
import java.applet.*;
import java.awt.*;
import java.awt.event.*;

import javax.swing.SwingUtilities;


public class breakout extends GraphicsProgram {
public static final int APPLICATION_WIDTH = 1900;
public static final int APPLICATION_HEIGHT = 1000;
private static final int WIDTH = APPLICATION_WIDTH;
private static final int HEIGHT = APPLICATION_HEIGHT;
private static final int PADDLE_WIDTH = 60;
private static final int PADDLE_HEIGHT = 10;
private static final int PADDLE_Y_OFFSET = 0;
private static final int NBRICKS_PER_ROW = 20;
private static final int NBRICK_ROWS = 15;
private static final int BRICK_SEP = 3;
private static final int BRICK_WIDTH =(WIDTH - (NBRICKS_PER_ROW - 1) * BRICK_SEP) / NBRICKS_PER_ROW;
private static final int BRICK_HEIGHT = 20;
private static final int BALL_RADIUS = 15;
private static final int BRICK_Y_OFFSET = 70;
private static final int NTURNS = 3;

private double vx, vy;
private RandomGenerator rgen = RandomGenerator.getInstance();
private static final int DELAY = 10;
//private static final Color RED = null;

	
	public void run() {
		for(int i=0; i < NTURNS; i++) {
			setUpGame();
			playGame();
			if(brickCounter == 0) {
				ball.setVisible(false);
				printWinner();
				break;
			}
			if(brickCounter > 0) {
				removeAll();
			}
		}
		if(brickCounter > 0) {
			printGameOver();
		}
	}
	
	private void setUpGame() {
		drawBricks( getWidth()/2, BRICK_Y_OFFSET);
		drawPaddle();
		drawBall();
		drawBall02();
		

	}
	
	private GRect brick;
	
	
	private void drawBricks(double cx, double cy) {				
		
	
		
		
		for( int row = 0; row < NBRICK_ROWS; row++ ) {
			
			for (int column = 0; column < NBRICKS_PER_ROW; column++) {
				
				
				 
				
				double	x = cx - (NBRICKS_PER_ROW*BRICK_WIDTH)/2 - ((NBRICKS_PER_ROW-1)*BRICK_SEP)/2 + column*BRICK_WIDTH + column*BRICK_SEP;
			
				
				double	y = cy + row*BRICK_HEIGHT + row*BRICK_SEP;

				brick = new GRect( x , y , BRICK_WIDTH , BRICK_HEIGHT );
				add (brick);
				brick.setFilled(true);
				
	
				
				if (row < 2) {
					brick.setColor(Color.RED);
				}
				if (row == 2 || row == 3) {
					brick.setColor(Color.ORANGE);
				}
				if (row == 4 || row == 5) {
					brick.setColor(Color.YELLOW);
				}
				if (row == 6 || row == 7) {
					brick.setColor(Color.GREEN);
				}
				if (row == 8 || row == 9) {
					brick.setColor(Color.CYAN);
				}
				if (row == 10 || row == 11) {
					brick.setColor(Color.BLACK);
					
				}
				if (row == 12 || row == 13) {
					brick.setColor(Color.MAGENTA);
				}
			}
		}
	}
	
	private GRect paddle;
	
	
	private void drawPaddle() {
		
		double x = getWidth()/2 - PADDLE_WIDTH/2; 
		double y = getHeight() - PADDLE_Y_OFFSET - PADDLE_HEIGHT;
		paddle = new GRect (x, y, PADDLE_WIDTH, PADDLE_HEIGHT);
		paddle.setFilled(true);
		add (paddle);
		addMouseListeners();
	}
	

	public void mouseMoved(MouseEvent e) {
		
		if ((e.getX() < getWidth() - PADDLE_WIDTH/2) && (e.getX() > PADDLE_WIDTH/2)) {
			paddle.setLocation(e.getX() - PADDLE_WIDTH/2, getHeight() - PADDLE_Y_OFFSET - PADDLE_HEIGHT);
		}
		
	}
	
	
	private GOval ball;
	private GOval ball02;
	
	public void drawBall02(){
			 
			double x = getWidth()/4 - BALL_RADIUS;
			 double y = getHeight()/2 - BALL_RADIUS;
			 ball02 = new GOval(x,y,BALL_RADIUS,BALL_RADIUS);
			 ball02.setFilled(true);
			 ball02.setColor(Color.RED);
			 add(ball02);
	}


	
	
	private void drawBall() {
		double x = getWidth()/2 - BALL_RADIUS;
		double y = getHeight()/2 - BALL_RADIUS;
		ball = new GOval(x, y, BALL_RADIUS, BALL_RADIUS);
		ball.setFilled(true);
		ball.setColor(Color.LIGHT_GRAY);
		add(ball);
	}
	
	public void playGame() {
		waitForClick();
		getBallVelocity();
		while (true) {
			moveBall();
			moveBall02();
			if (ball.getY() >= getHeight()) {
				break;
			}
			if (ball02.getY() >= getHeight()) {
				break;
			}
			if(brickCounter == 0) {
				break;
			
			}
		}
			/*pause(1200);
			drawBall02();
			getBallVelocity();
			while (true) {
				moveBall02();
				if (ball02.getY() >= getHeight()) {
					break;
				}
				if(brickCounter == 0) {
					break;
				
				}*/
		}
	
		
	
	
	
	
	private void getBallVelocity() {
		vy = 4.0;
		vx = rgen.nextDouble(1.0, 3.0);
		if (rgen.nextBoolean(0.5)) {
			vx = -vx; 
		}
	}
	
	
			
		
	
	private void moveBall() {
		ball.move(vx, vy);
		
		if ((ball.getX() - vx <= 0 && vx < 0 )|| (ball.getX() + vx >= (getWidth() - BALL_RADIUS*2) && vx>0)) {
			vx = -vx;
		}
	
		if ((ball.getY() - vy <= 0 && vy < 0 )) {
			vy = -vy;
		}
		
		GObject collider = getCollidingObject();
		if (collider == paddle) {
			
			
			if(ball.getY() >= getHeight() - PADDLE_Y_OFFSET - PADDLE_HEIGHT - BALL_RADIUS*2 && ball.getY() < getHeight() - PADDLE_Y_OFFSET - PADDLE_HEIGHT - BALL_RADIUS*2 + 4) {
				vy = -vy;	
			}
		}
		
		else if (collider != null) {
			remove(collider); 
			brickCounter--;
			vy = -vy;
		}
		pause (DELAY);
	}
	private void moveBall02() {
		ball02.move(vx, vy);
		
		if ((ball02.getX() - vx <= 0 && vx < 0 )|| (ball02.getX() + vx >= (getWidth() - BALL_RADIUS*2) && vx>0)) {
			vx = -vx;
		}
	
		if ((ball02.getY() - vy <= 0 && vy < 0 )) {
			vy = -vy;
		}
		
		GObject collider = getCollidingObject2();
		if (collider == paddle) {
			
			
			if(ball02.getY() >= getHeight() - PADDLE_Y_OFFSET - PADDLE_HEIGHT - BALL_RADIUS*2 && ball02.getY() < getHeight() - PADDLE_Y_OFFSET - PADDLE_HEIGHT - BALL_RADIUS*2 + 4) {
				vy = -vy;	
			}
		}
		
		else if (collider != null) {
			remove(collider); 
			brickCounter--;
			vy = -vy;
		}
		pause (DELAY);
	}
	
	private GObject getCollidingObject() {
		
		if((getElementAt(ball.getX(), ball.getY())) != null) {
	         return getElementAt(ball.getX(), ball.getY());
	      }
		else if (getElementAt( (ball.getX() + BALL_RADIUS*2), ball.getY()) != null ){
	         return getElementAt(ball.getX() + BALL_RADIUS*2, ball.getY());
	      }
		else if(getElementAt(ball.getX(), (ball.getY() + BALL_RADIUS*2)) != null ){
	         return getElementAt(ball.getX(), ball.getY() + BALL_RADIUS*2);
	      }
		else if(getElementAt((ball.getX() + BALL_RADIUS*2), (ball.getY() + BALL_RADIUS*2)) != null ){
	         return getElementAt(ball.getX() + BALL_RADIUS*2, ball.getY() + BALL_RADIUS*2);
	      }
		
		else{
	         return null;
	      }
		
	}
	private GObject getCollidingObject2() {
		
		if((getElementAt(ball02.getX(), ball02.getY())) != null) {
	         return getElementAt(ball02.getX(), ball02.getY());
	      }
		else if (getElementAt( (ball02.getX() + BALL_RADIUS*2), ball02.getY()) != null ){
	         return getElementAt(ball02.getX() + BALL_RADIUS*2, ball02.getY());
	      }
		else if(getElementAt(ball02.getX(), (ball02.getY() + BALL_RADIUS*2)) != null ){
	         return getElementAt(ball02.getX(), ball02.getY() + BALL_RADIUS*2);
	      }
		else if(getElementAt((ball02.getX() + BALL_RADIUS*2), (ball02.getY() + BALL_RADIUS*2)) != null ){
	         return getElementAt(ball02.getX() + BALL_RADIUS*2, ball02.getY() + BALL_RADIUS*2);
	      }
		
		else{
	         return null;
	      }
		
	}
	
	private void printGameOver() {
		GLabel gameOver = new GLabel ("Your Done Son", getWidth()/2, getHeight()/2);
		gameOver.move(-gameOver.getWidth()/2, -gameOver.getHeight());
		gameOver.setColor(Color.RED);
		add (gameOver);
		
	}
	
	private int brickCounter = 1000;
	
	private void printWinner() {
		GLabel Winner = new GLabel ("Winner Winner Chicken Dinner!!", getWidth()/2, getHeight()/2);
		Winner.move(-Winner.getWidth()/2, -Winner.getHeight());
		Winner.setColor(Color.RED);
		add (Winner);
	}
}	

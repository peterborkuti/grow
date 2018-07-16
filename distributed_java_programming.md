<<TableOfContents>>

=== Prologue ===

Hi,

Szilvi chose Head First Java, so let's try this book. I will read the some first chapters from it by Monday and I will start writing the tasks. If you know tasks, do not hesitate to write. Everybody can be a Task Creator, it does not need deep understanding of the particular language. When you read the book, concentrate on **What will be a good task for this** paragraph~/chapter~/question~/topic~/etc. (This "reading method" is also good for learning any programming language). Write the task here and try to solve.

=== The tasks consist of ===
*the initials of the creator (to blame him)
*short name of task (to identify it in the pull request)
*the description of the task

=== Repo name ===

The name of the git repository is DJPC. Please send pull request against this repo to one of the reviewers:

#[[https://github.com/djavorszky/DJPC]]
#[[https://github.com/peterborkuti/DJPC]]
#[[https://github.com/zsigmondrab/DJPC]]

=== Chapter 1 ===

The tasks with the same name are harder and harder as the number grows. They usually built on top of ech other. So if you can not solve one, try another with the same name but a lower number.

#BP:name1:Write your name to the console
#BP:name2:Write your name 10 times to the console in a row
#BP:name3:Write your name 10 times to the console, every name into a new row
#BP:echo1:You have to write the string into the console which comes as a parameter 
#BP:echo2:You have to write 10 times the string into the console which comes as a parameter
#BP:num1:Write all the integer numbers between 1 and 1000
#BP:num2:Write all the even numbers between 1 and 1000
#BP:num3:Write all the numbers between 1 and 100 that divisible by 5
#BP:num4:Write all the numbers between 1 and 100 that divisible by 5 but not divisible by 10 nor 4 and divisible by 3
#BP:num5:Write all the numbers between 1 and 20, and in the same line with the number, write the number multiplied by 5 and multiplied by 7
#BP:arr1: Create a String array with names. Write 100 random names based on the array.
#BP:arr2: Create a String array with names. Write the names after each other 100 times in the order of its place in the array.\\Let's say the array is A,B,X,C, than you have to write A,B,X,C,A,B,X,C,A,B... (but write every name into a new row)
#BP:arr3: Like arr2, but you have to write "Hello" instead of every third row

=== Chapter 2 ===

Here is a sample code for the second chapter. Please use this. You should not understand everything from this code, take a look at the ~*paint~* method. Try to create a program according to the first part of chapter II.

{{{
/**
 * Boilerplate code for DJPC (Distributed Java Programming Course)
 * 
 * Written by Péter Borkuti, 12/2/2016
 */
import java.awt.Dimension;
import java.awt.Graphics;
import java.awt.Graphics2D;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;
import java.awt.geom.Rectangle2D;
import java.util.Random;

import javax.swing.JApplet;
import javax.swing.JFrame;
import javax.swing.Timer;

class MyShape {
    private int width, height;

    MyShape() {
	Random r = new Random();

	width = r.nextInt(100);
	height = r.nextInt(100);
    }

    public void draw(Graphics g, Dimension d) {
	Graphics2D g2 = (Graphics2D) g;

	Random r = new Random();

	g2.draw(new Rectangle2D.Double(r.nextInt(d.width), r.nextInt(d.height), width, height));
    }
}

public class MyDemo extends JApplet implements ActionListener {
    private static final JApplet applet = new MyDemo();
    private static final Timer timer = new Timer(200, (ActionListener) applet);

    private static final MyShape myShape = new MyShape();

    public void paint(Graphics g) {
	myShape.draw(g, getSize());
    }

    public static void main(String s[]) {
	JFrame f = new JFrame("Demo");

	f.addWindowListener(new WindowAdapter() {
	    public void windowClosing(WindowEvent e) {
		timer.stop();

		System.exit(0);
	    }
	});

	applet.init();

	f.getContentPane().add("Center", applet);

	f.pack();
	f.setSize(new Dimension(550, 550));
	f.setVisible(true);

	timer.setRepeats(true);
	timer.start();
    }

    @Override
    public void actionPerformed(ActionEvent e) {
	this.repaint();
    }
}


}}}

Tasks

#BP:shape01:Create a Shape class with x,y coordinates, a draw empty method and move method which moves x,y into a random direction
#BP:shape02:Plan Rectangle, Triangle, Circle, etc classes and their inheritance tree
#BP:shape03:Createthe classes
#BP:shape04:ModifyMyDemo to draw an array of Shapes
#BP:shape05:Create a getShape method which creates Rectangle~/Triangle,Circle~/stc objects based on a random value
#BP:shape06:Fill the array of Shapes with getShape method
#BP:shape07:Check if you see moving objects on the screen
#BP:shape08:Now rotate all the shapes randomlyand apply it to "move" method
#BP:shape09:Create some more shape


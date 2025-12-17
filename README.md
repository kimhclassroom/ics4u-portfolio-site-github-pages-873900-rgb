# Java Game Assignment

## Project introduction / overview


## What the project does


## How to run the program
Copy and paste the code into CodeHS and create image files and upload the images. 
When "run" is pressed, the game will immediately run and stop when all lives have run out. To play again, press the "start" button at the bottom of the game.

## Project goals or purpose
The goal for this project was to create a simple Java Swing game, with event handling, layout managers, file I/O, collision detection, and classes and inheritance. The purpose of this assignment was to apply GUI knowledge from unit 4. Another goal was to create an enjoyable UI (user interface) experience and get feedback exhchange. The requirements for this project was to 

## Installation or setup instructions
Copy and paste the code into CodeHS and create image files and upload the images. This is already done for you.

## Any additional notes or documentation links


# Java Game Code

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.awt.image.BufferedImage; 
import java.io.*;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.Scanner;

// images
class Images {
    static Image background = load("background.jpg");
    static Image ufo = load("ufo.png");
    static Image asteroid = load("asteroid.png");
    static Image star = load("star.png");
    static Image bluestar = load("bluestar.PNG");
    static Image heart = load("heart.png");
    
    // image loader backup for if the image is not found (image turns pink)
    private static Image load(String name) {
        File f = new File(name);
        if (!f.exists()) {
            BufferedImage img = new BufferedImage(40, 40, BufferedImage.TYPE_INT_ARGB);
            Graphics g = img.getGraphics();
            g.setColor(Color.PINK);
            g.fillRect(0, 0, 40, 40);
            g.dispose();
            return img;
        }
        return new ImageIcon(name).getImage();
    }
}

// game object (superclass for all falling objects)
abstract class GameObject {
    int x;
    int y;
    int size;
    int speed; // falling speed

    GameObject(int x, int y, int size, int speed) {
        this.x = x;
        this.y = y;
        this.size = size;
        this.speed = speed;
    }

    void move() { // sets a default movement so the objects fall downwards
        y += speed;
    }

    Rectangle getBounds() { //rectangle used for collision detection
        return new Rectangle(x, y, size, size);
    }

    abstract void draw(Graphics g); // draw each object
}

// stars
class Star extends GameObject {
    Star(int x, int y) {
        super(x, y, 35, 4);
    }
    void draw(Graphics g) {
        g.drawImage(Images.star, x, y, size, size, null);
    }
}
 // blue star
class BlueStar extends GameObject {
    BlueStar(int x, int y) {
        super(x, y, 30, 4);
    }
    void draw(Graphics g) {
        g.drawImage(Images.bluestar, x, y, size, size, null);
    }
}

// asteroid
class Asteroid extends GameObject {
    Asteroid(int x, int y) {
        super(x, y, 42, 5);
    }
    void draw(Graphics g) {
        g.drawImage(Images.asteroid, x, y, size, size, null);
    }
}

// player
class UFO {
    int x = 250;
    int y = 380;
    int size = 70;

    Rectangle getBounds() {
        return new Rectangle(x, y, size, size);
    }

    void draw(Graphics g) {
        g.drawImage(Images.ufo, x, y, size, size, null);
    }
}

// heart
class Heart extends GameObject {
    Heart(int x, int y) {
        super(x, y, 30, 3);
    }
    void draw(Graphics g) {
        g.drawImage(Images.heart, x, y, size, size, null);
    }
}

// game panel (runs the game loop)
class GamePanel extends JPanel implements ActionListener {
    
// this timer calls actionPerformed() every 20 milliseconds; creates the loop fro the game
    javax.swing.Timer timer = new javax.swing.Timer(20, this);
    UFO player = new UFO();
    ArrayList<GameObject> objects = new ArrayList<>();

    int score = 0;
    int lives = 3;
    int highScore;

    boolean running = false;
    boolean left = false, right = false;
    double asteroidRate = 0.02; // controls chance of asteroid spawn rate

    GamePanel(int highScore) {
        this.highScore = highScore;
        setPreferredSize(new Dimension(550, 450));
        setFocusable(true); // nopte: edit here
        setupKeys();
    }

// move ufo with left and right key bindings
    void setupKeys() {
        InputMap im = getInputMap(WHEN_IN_FOCUSED_WINDOW);
        ActionMap am = getActionMap();

        im.put(KeyStroke.getKeyStroke("pressed LEFT"), "L1");
        im.put(KeyStroke.getKeyStroke("released LEFT"), "L0");
        im.put(KeyStroke.getKeyStroke("pressed RIGHT"), "R1");
        im.put(KeyStroke.getKeyStroke("released RIGHT"), "R0");

        am.put("L1", new AbstractAction(){ 
            public void actionPerformed(ActionEvent e){ left = true;}});
        am.put("L0", new AbstractAction(){ public void actionPerformed(ActionEvent e){ left=false; }});
        am.put("R1", new AbstractAction(){ public void actionPerformed(ActionEvent e){ right=true; }});
        am.put("R0", new AbstractAction(){ public void actionPerformed(ActionEvent e){ right=false; }});
    }

    void startGame() {
        objects.clear();
        score = 0;
        lives = 3;
        running = true;
        timer.start();
    }

    void easy() { 
        asteroidRate = 0.008; 
    }
    
    void medium() { 
        asteroidRate = 0.025; 
        
    }
    
    void hard() { 
        asteroidRate = 0.04; 
    }

    public void actionPerformed(ActionEvent e) {
        if (!running) return;
        
        if (left) {
            player.x -= 5; // move player left continuously if key is held
        }
        
        if (right) {
            player.x += 5; // move player right
        }
        
        //keep the player within the bounds of the screen
        player.x = Math.max(0, Math.min(player.x, getWidth() - player.size));
        
        //spawns the objects at at random with rate
        if (Math.random() < 0.017) {
            objects.add(new Star(randX(), 0));
        }
        
        if (Math.random() < 0.003) {
            objects.add(new BlueStar(randX(), 0));
        }
        
        if (Math.random() < asteroidRate) {
            objects.add(new Asteroid(randX(), 0));
        }
        
        if (Math.random() < 0.001) {
            objects.add(new Heart(randX(), 0));
        }

//iterate through objects while moving them. the iterator allows for safe removal of objects from the its list
           Iterator<GameObject> it = objects.iterator();
        while (it.hasNext()) {
            GameObject obj = it.next();
            obj.move();

            if (player.getBounds().intersects(obj.getBounds())) { // detects colliion if the objects overlap

                if (obj instanceof Star) {
                    score += 5;
                }
                if (obj instanceof BlueStar) {
                    score += 10;
                }

                if (obj instanceof Asteroid) {
                    lives--;
                    if (lives <= 0) {
                        lives = 0;
                        running = false;
                        timer.stop();
                    }
                }

                if (obj instanceof Heart && lives < 3) {
                lives++;
                }
                it.remove(); //if objects collide with the ufo, remove the collided object from the screen
            }

            if (obj.y > getHeight()) it.remove(); //remove fallen objects off the screen (not collided)
        }

        highScore = Math.max(highScore, score);
        repaint();
    }

    int randX() {
        return (int)(Math.random() * (getWidth() - 40));
    }
    
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        g.drawImage(Images.background, 0, 0, getWidth(), getHeight(), null);
        player.draw(g);
        for (GameObject o : objects) o.draw(g);
        g.setColor(Color.WHITE);
        
        g.drawString("Score: " + score, 10, 20);
        g.drawString("Lives: " + lives, 10, 40);
        g.drawString("High Score: " + highScore, 420, 20);
    }
}

// login frame
class LoginFrame extends JFrame {
    JTextField user = new JTextField();
    JPasswordField pass = new JPasswordField();

    LoginFrame() {
        setTitle("Login");
        setSize(300, 200);
        setLocationRelativeTo(null);
        setDefaultCloseOperation(EXIT_ON_CLOSE);

        JPanel p = new JPanel(new GridLayout(5, 1, 5, 5));
        p.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        JButton login = new JButton("Login");
        JButton register = new JButton("Register");

        login.addActionListener(e -> login());
        register.addActionListener(e -> register());
        
        JPanel b = new JPanel();
        b.add(login); b.add(register);
        p.add(b);
        add(p);
        p.add(new JLabel("Username"));
        p.add(user);
        p.add(new JLabel("Password"));
        p.add(pass);
        setVisible(true);
    }

    void login() { // saves the username and password to savedUsers.txt file
        try (Scanner sc = new Scanner(new File("savedUsers.txt"))) {
            while (sc.hasNextLine()) {
                String[] d = sc.nextLine().split(",");
                if (d[0].equals(user.getText()) &&
                    d[1].equals(new String(pass.getPassword()))) {
                    new GalaxyGlideGame(d[0]);
                    dispose();
                    return;
                }
            }
        JOptionPane.showMessageDialog(this,"invalid login");
    } catch(Exception e) {
        JOptionPane.showMessageDialog(this,"user not found");
    }
}

    void register() {
        try (FileWriter fw = new FileWriter("savedUsers.txt", true)) {
            fw.write(user.getText()+","+new String(pass.getPassword())+"\n");
            JOptionPane.showMessageDialog(this,"registered!");
        } catch(Exception e) {}
    }
}

// main game frame
public class GalaxyGlideGame extends JFrame {

    public GalaxyGlideGame(String user) {
        setTitle("Galaxy Glide - " + user);
        setDefaultCloseOperation(EXIT_ON_CLOSE);

        GamePanel panel = new GamePanel(loadHighScore(user));
        add(panel);

        JPanel buttons = new JPanel();
        JButton start = new JButton("Start");
        JButton easy = new JButton("Easy");
        JButton med = new JButton("Medium");
        JButton hard = new JButton("Hard");

        start.addActionListener(e -> panel.startGame());
        easy.addActionListener(e -> panel.easy());
        med.addActionListener(e -> panel.medium());
        hard.addActionListener(e -> panel.hard());

        buttons.add(easy); 
        buttons.add(med); 
        buttons.add(hard); 
        buttons.add(start);
        add(buttons, BorderLayout.SOUTH);

        pack(); // size all the buttons to the buttom of the screen
        setLocationRelativeTo(null); // centers the window on the screen
        setVisible(true);
    }

// highscore - loads previous best score
    static int loadHighScore(String user) { 
        int max = 0;
        try (Scanner sc = new Scanner(new File("scores.txt"))) { //acesses the score.txt 
            while (sc.hasNextLine()) {
                String[] d = sc.nextLine().split(","); //splits each score with a comma and looks for the highest integer
                if (d[0].equals(user)) max = Math.max(max, Integer.parseInt(d[1]));
            }
        } catch(Exception e){ }
            return max;
    }
    
    // starts the swing game on the event dispatch thread (EDT). all components run based off this thread
    public static void main(String[] args) { 
        //runs the main thread timer and loads the login page
        SwingUtilities.invokeLater(LoginFrame::new);
    }
}

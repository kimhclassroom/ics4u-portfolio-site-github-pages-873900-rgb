# Hi, I'm Erica
Welcome to my ICS4U portfolio. I’ll use this site to post my work and reflections.

## Highlights
- 🔧 Project 1: *(title)* – *(1–2 lines about what it does)*
- 🧠 Concept I learned: *(short note)*
- 📝 Blog/Reflection: [Reflection #1](./posts/first_reflection.md) [Reflection #2](./posts/second_reflection.md)
- 

## About me
A short intro (interests, goals, tools I’m learning).

---
## Java swing Game - Galaxy Glide

import javax.swing.*;
import java.awt.*;
import java.util.*;
import java.awt.event.*;
import java.io.*;

class Images {
    static Image background = new ImageIcon("background.jpg").getImage();
    static Image ufo        = new ImageIcon("ufo.jpg").getImage();
    static Image asteroid   = new ImageIcon("asteroid.png").getImage();
    static Image star       = new ImageIcon("star.png").getImage();
    static Image bluestar   = new ImageIcon("bluestar.png").getImage();
    static Image heart      = new ImageIcon("heart.png").getImage();
}

// base class
abstract class GameObject {
    int x, y, size, speed;
    Color color;

    GameObject(int x, int y, int size, int speed, Color color) {
        this.x = x;
        this.y = y;
        this.size = size;
        this.speed = speed;
        this.color = color;
    }

    void move() {
        y += speed;
    }

    Rectangle getBounds() {
        return new Rectangle(x, y, size, size);
    }

    abstract void draw(Graphics g);
}

// star class
class Star extends GameObject {
    Star(int x, int y) {
        super(x, y, 20, 4, Color.YELLOW);
    }

    void draw(Graphics g) {
        g.drawImage(Images.star, x, y, size, size, null);
    }
}

class BlueStar extends GameObject {
    BlueStar(int x, int y) {
        super(x, y, 20, 4, Color.CYAN);
    }

    void draw(Graphics g) {
        g.drawImage(Images.bluestar, x, y, size, size, null);
    }
}

// asteroid class
class Asteroid extends GameObject {
    Asteroid(int x, int y) {
        super(x, y, 40, 5, Color.GRAY);
    }

    void draw(Graphics g) {
        g.drawImage(Images.asteroid, x, y, size, size, null);
    }
}

// heart class
class Heart extends GameObject {

    Heart(int x, int y) {
        super(x, y, 20, 3, Color.RED);
    }

    void draw(Graphics g) {
        g.drawImage(Images.heart, x, y, size, size, null);
    }
}


// player (ufo)
class UFO {
    int x = 250;
    int y = 400;
    int size = 40;

    Rectangle getBounds() {
        return new Rectangle(x, y, size, size);
    }

    void draw(Graphics g) {
        g.setColor(Color.CYAN);
        g.fillOval(x, y, size, size);
    }
}

// game panel
class GamePanel extends JPanel implements ActionListener {

    javax.swing.Timer timer = new javax.swing.Timer(20, this);
    UFO player = new UFO();
    ArrayList<GameObject> objects = new ArrayList<>();

    int score = 0;
    int lives = 3;
    int highScore = 0;

    boolean running = false;
    boolean left = false, right = false;

    double asteroidRate = 0.03;

    GamePanel(int highScore) {
        this.highScore = highScore;

        protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        g.drawImage(background, x, y, 100, 100, this);

        InputMap im = getInputMap(JComponent.WHEN_IN_FOCUSED_WINDOW);
        ActionMap am = getActionMap();

        im.put(KeyStroke.getKeyStroke("pressed LEFT"), "leftP");
        im.put(KeyStroke.getKeyStroke("released LEFT"), "leftR");
        im.put(KeyStroke.getKeyStroke("pressed RIGHT"), "rightP");
        im.put(KeyStroke.getKeyStroke("released RIGHT"), "rightR");

        am.put("leftP", new AbstractAction() { public void actionPerformed(ActionEvent e) { left = true; }});
        am.put("leftR", new AbstractAction() { public void actionPerformed(ActionEvent e) { left = false; }});
        am.put("rightP", new AbstractAction() { public void actionPerformed(ActionEvent e) { right = true; }});
        am.put("rightR", new AbstractAction() { public void actionPerformed(ActionEvent e) { right = false; }});
    }
}

    void startGame() {
        objects.clear();
        score = 0;
        lives = 3;
        running = true;
        timer.start();
    }

    void easy()   { asteroidRate = 0.01; }
    void medium() { asteroidRate = 0.03; }
    void hard()   { asteroidRate = 0.05; }

    public void actionPerformed(ActionEvent e) {
        if (!running) return;

        if (left)  player.x -= 5;
        if (right) player.x += 5;

        if (player.x < 0) player.x = 0;
        if (player.x > getWidth() - player.size)
            player.x = getWidth() - player.size;

        if (Math.random() < 0.05) objects.add(new Star(randX(), 0));
        if (Math.random() < 0.01) objects.add(new BlueStar(randX(), 0));
        if (Math.random() < asteroidRate) objects.add(new Asteroid(randX(), 0));

        Iterator<GameObject> it = objects.iterator();
        
    while (it.hasNext()) {
    GameObject obj = it.next();
    obj.move();

    if (player.getBounds().intersects(obj.getBounds())) {

        if (obj instanceof Star) score += 5;
        if (obj instanceof BlueStar) score += 10;

        if (obj instanceof Asteroid) {
            lives--;
            if (lives <= 0) {
                lives = 0;
                running = false;
                timer.stop();
            }
        }

        if (obj instanceof Heart && lives < 3) {
            lives++; // +1 life (max 3)
        }

        it.remove();
    }

    if (obj.y > getHeight()) {
        it.remove();
    }
}

        if (score > highScore) highScore = score;

        repaint();
    }

    int randX() {
        return (int)(Math.random() * 500);
    }

    protected void paintComponent(Graphics g) {
    super.paintComponent(g);

    g.drawImage(Images.background, 0, 0, getWidth(), getHeight(), null);

    player.draw(g);
    for (GameObject o : objects) o.draw(g);

    g.setColor(Color.WHITE);
    g.drawString("Score: " + score, 10, 20);
    g.drawString("Lives: " + lives, 10, 40);

    g.drawString("High Score:", 420, 20);
    g.drawString("" + highScore, 420, 40);
}


// login page
class LoginFrame extends JFrame {

    JTextField user = new JTextField();
    JPasswordField pass = new JPasswordField();

    LoginFrame() {
        setTitle("Login");
        setSize(300, 200);
        setLocationRelativeTo(null);
        setDefaultCloseOperation(EXIT_ON_CLOSE);

        JPanel p = new JPanel(new GridLayout(5,1,5,5));
        p.setBorder(BorderFactory.createEmptyBorder(10,10,10,10));

        p.add(new JLabel("Username"));
        p.add(user);
        p.add(new JLabel("Password"));
        p.add(pass);

        JPanel buttons = new JPanel();
        JButton login = new JButton("Login");
        JButton register = new JButton("Register");

        buttons.add(login);
        buttons.add(register);
        p.add(buttons);

        add(p);

        login.addActionListener(e -> login());
        register.addActionListener(e -> register());

        setVisible(true);
    }

    void login() {
        try {
            Scanner sc = new Scanner(new File("users.txt"));
            while (sc.hasNextLine()) {
                String[] d = sc.nextLine().split(",");
                if (d[0].equals(user.getText()) &&
                    d[1].equals(new String(pass.getPassword()))) {

                    new GalaxyGlideGame(user.getText());
                    dispose();
                    return;
                }
            }
            JOptionPane.showMessageDialog(this, "Invalid login");
        } catch (Exception e) {
            JOptionPane.showMessageDialog(this, "No users file");
        }
    }

    void register() {
        try (FileWriter fw = new FileWriter("users.txt", true)) {
            fw.write(user.getText() + "," + new String(pass.getPassword()) + "\n");
            JOptionPane.showMessageDialog(this, "Registered!");
        } catch (Exception e) {}
    }
}

// main frame for the game playing page
public class GalaxyGlideGame extends JFrame {

    String username;
    GamePanel panel;

    public GalaxyGlideGame(String user) {
        username = user;

        int highScore = loadHighScore();

        setTitle("Galaxy Glide - " + user);
        setSize(550, 500);
        setDefaultCloseOperation(EXIT_ON_CLOSE);

        panel = new GamePanel(highScore);
        add(panel);

        JPanel buttons = new JPanel();
        JButton start = new JButton("Start");
        JButton easy = new JButton("Easy");
        JButton medium = new JButton("Medium");
        JButton hard = new JButton("Hard");

        start.addActionListener(e -> panel.startGame());
        easy.addActionListener(e -> panel.easy());
        medium.addActionListener(e -> panel.medium());
        hard.addActionListener(e -> panel.hard());

        buttons.add(easy);
        buttons.add(medium);
        buttons.add(hard);
        buttons.add(start);

        add(buttons, BorderLayout.SOUTH);

        setVisible(true);
    }

    int loadHighScore() {
        int max = 0;
        try {
            File f = new File("scores.txt");
            if (!f.exists()) return 0;

            Scanner sc = new Scanner(f);
            while (sc.hasNextLine()) {
                String[] d = sc.nextLine().split(",");
                if (d[0].equals(username)) {
                    max = Math.max(max, Integer.parseInt(d[1]));
                }
            }
        } catch (Exception e) {}
        return max;
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(LoginFrame::new);
    }
}

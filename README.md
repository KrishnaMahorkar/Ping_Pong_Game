## 🎮 Project Ping Pong Game?

**2-player Ping Pong game** where players use their **hands** to control the bats (paddles) — **no keyboard or mouse needed**. The game uses your **webcam** to track your **left and right hands**, and lets you play ping pong with a moving ball.

It’s like a **virtual table tennis game** that works with your body!

---

## 📸 How Does It Work?

### 🧠 You used these tools:
- **OpenCV** – to handle the webcam and images.
- **cvzone + MediaPipe** – to track your hands in real time.
- **Numpy** – to help with positioning and movement.
- **PNG images** – to make the game look nice (background, ball, bats, etc.).

---

## 💡 What Features Does Your Game Have?

| Feature | What it does |
|--------|--------------|
| 🎥 Webcam control | Tracks your hands using the webcam. |
| ✋ Hand detection | Detects left and right hands to move the paddles. |
| 🏓 Bouncing ball | Ball moves and bounces like in real ping pong. |
| 🎯 Paddle collision | If the ball hits your bat, it bounces back and adds to your score. |
| ❌ Game Over screen | If the ball goes off the screen, the game ends. |
| 🔁 Restart / Quit | Press `r` to restart, or `q` to quit the game. |
| 🌆 Background and UI | Adds a background image, score, bats, and ball image for a better look. |

---

## 👨‍💻 How the Game Flows:

1. **Start the game** – Your webcam opens, and the background + game elements appear.
2. **Move your hands** – Left and right hands act like bats to hit the ball.
3. **Ball moves** – It keeps bouncing until someone misses.
4. **Scoring** – Every time a player hits the ball, they get 1 point.
5. **Game Over** – If the ball goes too far left or right, the game ends.
6. **Restart or quit** – Press `r` to restart or `q` to quit.

---

## 🔥 Why This Is Awesome

- It's **hands-free gameplay** — great use of AI and computer vision.
- It feels like **augmented reality ping pong**.
- No need for extra controllers or hardware — just a **webcam**.
- It's a fun project that shows your skills in **Python + OpenCV + AI**.

---

## 🚀 You Can Add More Cool Stuff Later Like:

- 🎵 Add sound effects.
- 👾 Add a single-player mode with AI.
- ⏱️ Add a timer or match mode.
- 📱 Turn it into a mobile AR game someday!

---

Absolutely! Let’s break down your **Ping Pong Game with hand tracking** step-by-step in super simple and clear terms so anyone can understand what’s happening — no complicated jargon.

---

## 🧠 What You're Building
You’re building a **Ping Pong game** that:
- Uses a webcam.
- Tracks two hands.
- Lets each hand control a paddle (bat).
- Moves a ball that bounces and scores points.
- Ends the game when the ball goes too far left or right.
- Shows a background, score, and “game over” screen.

---

## 📦 Step-by-Step Breakdown of the Code

### 1. **Importing Libraries**
```python
import cv2           # For webcam and image handling
import cvzone        # Makes it easier to work with OpenCV + MediaPipe
from cvzone.HandTrackingModule import HandDetector  # For hand tracking
import numpy as np   # For number arrays
import mediapipe as mp  # (Used internally by cvzone for hand tracking)
```

---

### 2. **Webcam Setup**
```python
cap = cv2.VideoCapture(0)  # Open webcam
cap.set(3, 1280)  # Set width
cap.set(4, 720)   # Set height
```
👉 You're opening the webcam and setting the frame size to 1280×720 (HD).

---

### 3. **Loading Game Images**
```python
imgBackground = cv2.imread("...Background.png")
imgGameOver = cv2.imread("...gameOver.png")
imgBall = cv2.imread("...Ball.png", cv2.IMREAD_UNCHANGED)
imgBat1 = cv2.imread("...bat1.png", cv2.IMREAD_UNCHANGED)
imgBat2 = cv2.imread("...bat2.png", cv2.IMREAD_UNCHANGED)
```
👉 These are the visuals for:
- **Background** of the game
- **Game over screen**
- **Ball**
- **Left bat** and **right bat**

---

### 4. **Initialize Hand Tracker**
```python
detector = HandDetector(detectionCon=0.8, maxHands=2)
```
👉 This sets up hand detection with 80% confidence and allows **up to 2 hands**.

---

### 5. **Game Variables**
```python
ballPos = [100, 100]  # Ball’s starting position (x, y)
speedX = 15           # Ball speed in X direction
speedY = 15           # Ball speed in Y direction
gameOver = False      # Flag to track if the game is over
score = [0, 0]        # Scores for Player 1 and Player 2
```

---

### 6. **Main Game Loop**
```python
while True:
```
👉 This loop keeps running the game until you press `q` to quit.

---

### 7. **Read and Flip Webcam Image**
```python
_, img = cap.read()  # Get current frame from webcam
img = cv2.flip(img, 1)  # Flip it horizontally (like a mirror)
```

---

### 8. **Track Hands**
```python
hands, img = detector.findHands(img, flipType=False)
```
👉 It detects hands and gives you:
- Location of hands (bounding box).
- Which hand (left or right).

---

### 9. **Add Background**
```python
img = cv2.addWeighted(img, 0.2, imgBackground, 0.8, 0)
```
👉 This mixes your camera feed **lightly** with the background image for a nice visual effect.

---

### 10. **Control the Bats With Hands**
```python
for hand in hands:
    x, y, w, h = hand['bbox']  # Get hand position
    h1, w1, _ = imgBat1.shape
    y1 = y - h1 // 2           # Center the bat on the hand
    y1 = np.clip(y1, 20, 415)  # Keep bat within screen
```

#### 👉 If it’s the Left Hand:
```python
if hand['type'] == "Left":
    img = cvzone.overlayPNG(img, imgBat1, (59, y1))  # Draw bat
    if 59 < ballPos[0] < 59 + w1 and y1 < ballPos[1] < y1 + h1:
        speedX = -speedX  # Reverse ball direction
        ballPos[0] += 30  # Push ball away
        score[0] += 1     # Add score to Player 1
```

#### 👉 If it’s the Right Hand:
```python
if hand['type'] == "Right":
    img = cvzone.overlayPNG(img, imgBat2, (1195, y1))  # Draw bat
    if 1195 - 50 < ballPos[0] < 1195 and y1 < ballPos[1] < y1 + h1:
        speedX = -speedX
        ballPos[0] -= 30
        score[1] += 1
```

---

### 11. **Game Over Logic**
```python
if ballPos[0] < 40 or ballPos[0] > 1200:
    gameOver = True
```
👉 If the ball goes too far left or right, the game ends.

---

### 12. **If Game Over: Show Game Over Image**
```python
if gameOver:
    img = imgGameOver
    cv2.putText(img, str(score[0] + score[1]).zfill(2), (585, 360), ...)
```

---

### 13. **If Game Is Running: Move Ball & Show Scores**
```python
else:
    if ballPos[1] >= 500 or ballPos[1] <= 10:
        speedY = -speedY  # Bounce off top/bottom

    ballPos[0] += speedX
    ballPos[1] += speedY

    img = cvzone.overlayPNG(img, imgBall, ballPos)

    cv2.putText(img, str(score[0]), (300, 650), ...)
    cv2.putText(img, str(score[1]), (900, 650), ...)
```

---

### 14. **Show Webcam Preview (Small)**
```python
img[580:700, 20:233] = cv2.resize(imgRaw, (213, 120))
```
👉 Shows a small live preview of your camera feed at the bottom.

---

### 15. **Keyboard Controls**
```python
key = cv2.waitKey(1)
if key == ord('r'):  # Restart game
    ballPos = [100, 100]
    speedX = 15
    speedY = 15
    gameOver = False
    score = [0, 0]
if key == ord('q'):  # Quit game
    break
```

---

### 16. **Release the Camera and Close Window**
```python
cap.release()
cv2.destroyAllWindows()
```

---

## 🎮 Summary

| Feature       | How it works |
|---------------|--------------|
| Hand tracking | Uses webcam + MediaPipe to find hands |
| Paddle control | Left/right hand moves left/right bat |
| Ball motion   | Ball bounces around and interacts with paddles |
| Scoring       | Player gets a point when they hit the ball |
| Game over     | If ball exits screen, game ends |
| UI            | Background, ball, bats, scores, camera preview |

---

## OUTPUT 

![Output](https://github.com/user-attachments/assets/63364baf-a460-4080-a1fb-1e02019186f6)

# Virtual Drag and Drop using Hand Tracking in Python
This project implements a **virtual drag-and-drop system** using **OpenCV** and **CVZone**. By leveraging real-time **hand tracking** through a webcam, users can move rectangles on the screen by using simple hand gestures. This project is an excellent example of how computer vision can be used for interactive applications.
## Features
- **Real-time hand tracking**: Detect and track hand movements via a webcam using CVZone's `HandDetector`.
- **Drag-and-drop functionality**: Move rectangles by "gripping" with hand gestures (pinching index and middle fingers).
- **Multiple draggable rectangles**: Interact with several rectangles simultaneously.
- **Visual effects**: Both solid and transparent rectangle visuals provide interactive feedback.
# Requirements
- **Python 3.x**
- **OpenCV**: `pip install opencv-python`
- **CVZone**: `pip install cvzone`
- **Numpy**: `pip install numpy`
## How It Works
1. **Hand Detection**: The program uses CVZone’s `HandDetector` to track hand movements in real-time.
2. **Interaction Logic**: If the distance between the index and middle finger is less than a threshold, it is considered a "grip," and the rectangle follows the hand’s movements.
3. **Drawing Rectangles**: Solid and semi-transparent rectangles are drawn to give the user visual feedback while interacting with them.
## Code Overview
- **DragRect Class**: Defines the rectangle’s properties (color, size, position) and updates its position when dragged.
- **Hand Tracking**: Captures hand landmarks and calculates the distance between fingers to trigger drag-and-drop actions.
- **Main Loop**: Continuously detects hand gestures and updates the position of rectangles accordingly.
  
```python
import cv2
from cvzone.HandTrackingModule import HandDetector
import numpy as np

cap = cv2.VideoCapture(0)
cap.set(3, 1280)
cap.set(4, 720)
detector = HandDetector(detectionCon=0.8)
colorR = (255, 0, 255)

# Rectangle initialization
rectList = []
for x in range(5):
    rectList.append(DragRect(colorR, [x * 250 + 150, 150]))

while True:
    success, img = cap.read()
    img = cv2.flip(img, 1)
    hands, img = detector.findHands(img, flipType=False)
    
    if hands:
        lmList = hands[0]['lmList']
        cursor = lmList[8][:2]  # x, y coordinates of the index finger
        length, _, img = detector.findDistance(lmList[8][:2], lmList[12][:2], img)

        if length < 40:
            for rect in rectList:
                rect.update(cursor)

    for rect in rectList:
        cx, cy = rect.posCenter
        w, h = rect.size
        cv2.rectangle(img, (cx - w // 2, cy - h // 2), (cx + w // 2, cy + h // 2), rect.colorR, cv2.FILLED)
        cv2.rectangle(img, (cx - w // 2, cy - h // 2), (cx + w // 2, cy + h // 2), (50, 50, 50), 3)
    
    # Transparent effect
    overlay = img.copy()
    for rect in rectList:
        cx, cy = rect.posCenter
        w, h = rect.size
        cv2.rectangle(overlay, (cx - w // 2, cy - h // 2), (cx + w // 2, cy + h // 2), rect.colorR, -1)
        alpha = 0.5
        img = cv2.addWeighted(overlay, alpha, img, 1 - alpha, 0)

    cv2.imshow("Image", img)
    cv2.waitKey(1)
```

## How to Run

1. Clone the repository:
    ```bash
    git clone https://github.com/your-username/virtual-drag-drop-opencv.git
    ```
2. Navigate to the project directory:
    ```bash
    cd virtual-drag-drop-opencv
    ```
3. Install the required dependencies:
    ```bash
    pip install -r requirements.txt
    ```
4. Run the code:
    ```bash
    python virtual_drag_drop.py
    ```

## Usage

- Simply move your hand in front of the webcam.
- Pinch with your index and middle finger to drag the rectangles around the screen.
- Release your fingers to drop the rectangles.

## Project Structure

- **`virtual_drag_drop.py`**: The main code that runs the virtual drag-and-drop functionality.
- **`requirements.txt`**: The list of dependencies required to run the project.

## Future Enhancements

- **Support for multi-hand tracking**.
- **Advanced gesture-based interactions** like resizing or rotating objects.
- **Customizable shapes and colors for the rectangles**.
  
## Contributing

Feel free to contribute to this project by opening issues or submitting pull requests.

## License

This project is licensed under the MIT License.

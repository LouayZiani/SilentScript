# SilentScript
Real-time ASL hand gesture recognition and translation to English

SilentScript is a solo class project that detects American Sign Language (ASL) hand gestures from a live webcam feed and translates them into English text in real time. The system is designed as an engaging, interactive way to learn ASL without the need for wearable sensors or gloves.

## Demo / Overview
The pipeline runs in five stages:

Camera — captures the hand gesture

Hand Detection — OpenCV/MediaPipe locates the hand and extracts features (21 landmark points)

Finger Motion Analysis — decodes the gesture geometry

Gesture Classifier — a pre-trained KNN model identifies the ASL letter

English Letter Output — the translated text is displayed in a Processing UI in real time

## Why This Project?
70 million people use sign language globally (WHO, 2021).

Deaf people rely on ASL to communicate, but there is a significant communication barrier, especially in public.

SilentScript offers a no-cost, sensor-free, interactive way to bridge that gap and help people learn ASL.

## Features
Focus on the ASL alphabet (and common words)

No wearable sensors or gloves required — just a webcam

Processing for lightweight, artistic visuals and interaction

Python for vision and machine learning (MediaPipe → OSC bridge)

Room for future improvement (sentence generation, voice output)

## Tech Stack
Component	Technology
UI & Interaction	Processing (oscP5)
Vision / Hand Tracking	MediaPipe (Python) / OpenCV
Communication	OSC bridge (python-osc ↔ oscP5)
Classifier	KNN (scikit-learn)
Architecture
text
Webcam → MediaPipe (Python) → OSC message → Processing UI
                                     ↓
                              KNN Classifier
                                     ↓
                            ASL Letter → English Text
Python handles vision and AI; Processing handles the UI and interaction. The two communicate over OSC (UDP) on port 12345.

## Results
Classifies 26 gestures from live video with ~90% accuracy on a held-out test set of 80 samples.

Trained KNN on features extracted from nearly 300 training samples.

Added histogram equalization to improve robustness under varying lighting, raising accuracy in dim lighting from 72% → 85%.

## Setup
Prerequisites
Python 3.x

Processing 4.x

Webcam

Python dependencies
bash
pip install opencv-python mediapipe scikit-learn python-osc
Processing libraries
Install via the Contribution Manager:

oscP5

netP5

## Usage
Start the Python vision script (hand tracking + KNN classification + OSC sender):

bash
python hand_tracking.py
Run the Processing sketch (hand_detection.pde) to receive OSC messages and render the UI.

Show an ASL letter to the camera — the detected character will appear on screen.

Example OSC message (Python → Processing)
python
from pythonosc.udp_client import SimpleUDPClient

client = SimpleUDPClient("127.0.0.1", 12345)  # IP and port of Processing

gesture = "D"
client.send_message("/asl", gesture)
Receiving in Processing
java
import oscP5.*;
import netP5.*;

OscP5 oscP5;
String receivedGesture = "";

void setup() {
  size(600, 400);
  oscP5 = new OscP5(this, 12345); // Same port as Python sends to
}

void draw() {
  background(0);
  fill(255);
  textSize(64);
  textAlign(CENTER, CENTER);
  text("Detected: " + receivedGesture, width/2, height/2);
}

void oscEvent(OscMessage msg) {
  if (msg.checkAddrPattern("/asl")) {
    receivedGesture = msg.get(0).stringValue();
  }
}
KNN Classifier
python
from sklearn.neighbors import KNeighborsClassifier

# X_train = list of vectors like [x1, y1, x2, y2, ..., x21, y21]
# y_train = list of labels like ["A", "B", "C", ...]

model = KNeighborsClassifier(n_neighbors=3)
model.fit(X_train, y_train)

# For new input from the webcam
prediction = model.predict([new_hand_vector])
print("Predicted ASL letter:", prediction[0])
Challenges & Solutions
Challenge	Solution
Tracking may not be very accurate	Chose KNN, which works better with small datasets
Lighting affects performance	Hand-detection confidence thresholding + histogram equalization
Future Improvements
Sentence generation from sequences of letters

Voice output (text-to-speech)

Expanded vocabulary beyond the ASL alphabet

Improved tracking with larger, more diverse datasets

### Built with Processing, OpenCV, MediaPipe, and scikit-learn.

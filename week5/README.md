# Week 5 - Pose detection

- Intro Machine Learning. Data > Training > Model.
- Werken met pose detection met pretrained model van mediapipe.
- game of app besturen met poses

<br><br><br>

## MediaPipe

[MediaPipe](https://developers.google.com/mediapipe/solutions/examples) is een library van google, waarin een model is getraind om poses in webcam beelden te herkennen. Je krijgt deze poses terug als vector data (`x,y` coördinaten). 

Je kan een `<canvas>` element gebruiken om de poses over het webcam beeld heen te tekenen.

<img src="../images/hand_landmark_960.png" width="400">

<img src="../images/pose_detector_960.png" width="400">

<img src="../images/face_landmarker_960.png" width="400">

<br><br><br>

# Opdracht

## Stap 1

Bouw een html pagina met webcam pose detection van [MediaPipe](https://developers.google.com/mediapipe/solutions/examples). Kies hand, body of face detection. Gebruik de documentatie om de webcam te lezen en de poses in een canvas te tekenen.

- [✌️ Demo Hand Pose](https://mediapipe-studio.webapps.google.com/demo/hand_landmarker) en [Code Examples](https://developers.google.com/mediapipe/solutions/vision/hand_landmarker#get_started)
- [🕺 Demo Body Pose](https://mediapipe-studio.webapps.google.com/demo/pose_landmarker) en [Code Examples](https://developers.google.com/mediapipe/solutions/vision/pose_landmarker#get_started)
- [😱 Demo Face Pose](https://mediapipe-studio.webapps.google.com/demo/face_landmarker) en [Code Examples](https://developers.google.com/mediapipe/solutions/vision/face_landmarker#get_started)

## Stap 2

Omdat je de poses in een canvas tekent, heb je toegang tot de `x,y` pose coördinaten. Toon deze coördinaten in de browser console of in een html veld. 

Maak een button die de coördinaten alleen toont `on click`, omdat de pagina traag kan worden als je 60 keer per seconde een grote hoeveelheid data logt.

## Stap 3

Bedenk een game of applicatie waarbij je gebruik maakt van de `x,y` coördinaten van de pose. Bijvoorbeeld:

- Een fashion site waar je zonnebrillen virtueel kan uitproberen.
- Een safety instruction die kan zien of je te dicht op je computerscherm zit.
- Een pong game waarbij de positie van de paddles bepaald wordt door de positie van de hand.
- Squid game: je moet naar de laptop toe lopen zonder dat de evil robot je ziet.

> *Tip: Is er een `z` coördinaat beschikbaar? En kan je aan de afstand tussen beide ogen ook zien hoe ver iemand van de webcam verwijderd is?*

<img src="../images/pose-sun.png" width="400">

<img src="../images/posture.png" width="400">

<img src="../images/posepong.png" width="400">

<img src="../images/pose-squid.png" width="400">

<br><br><br>

## Links

- [MediaPipe Javascript Documentation](https://developers.google.com/mediapipe/api/solutions/js/tasks-vision)
- [MediaPipe Examples](https://developers.google.com/mediapipe/solutions/examples)
- [Codepen Hand](https://codepen.io/mediapipe-preview/pen/gOKBGPN)
- [Codepen Body](https://codepen.io/mediapipe-preview/pen/abRLMxN)
- [Codepen Face](https://codepen.io/mediapipe-preview/pen/OJBVQJm)
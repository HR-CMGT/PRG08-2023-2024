# React MediaPipe

Voorbeeld van het werken met MediaPipe, Webcam en Canvas in React.

<br><br><br>

## Installatie

```sh
npm create vite@latest my-cool-project
cd my-cool-project
npm install
npm install react-webcam
npm install @mediapipe/tasks-vision
```

## Webcam component

```js
const videoConstraints = {
    width: 480,
    height: 270,
    facingMode: "user"
};

export default function App() {
    const webcamRef = useRef(null)
    const canvasRef = useRef(null)

    return (
        <section>
            <Webcam
                width="480"
                height="270"
                mirrored
                id="webcam"
                audio={false}
                videoConstraints={videoConstraints}
                ref={webcamRef}
            />
        </section>
    )
}
```
## Plaats webcam en canvas element boven elkaar.

Omdat de webcam is gespiegeld moet je het canvas spiegelen in de CSS.

```js
<section className="videosection">
    <Webcam ... />
    <canvas ref={canvasRef} width="480" height="270"></canvas>
</section>
```
css
```css
.videosection {
    background-color: rgb(70, 70, 70);
    height:270px;
}

.videosection video {
    position: absolute;
}

.videosection canvas {
    position: absolute;
    transform: rotateY(180deg);
}
```
## Documentatie (face, hand, body): 

- https://developers.google.com/mediapipe/solutions/setup_web
- https://www.npmjs.com/package/@mediapipe/tasks-vision

<br><br><br>

## Landmarker laden

Start de landmarker in `useEffect`. Sla een referentie naar het model op met `useRef`. 

In dev mode triggert useEffect soms twee keer, dit is een bug in react.

```js
 useEffect(() => {
    const createHandLandmarker = async () => {
        const vision = await FilesetResolver.forVisionTasks("https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision@0.10.0/wasm");
        const handLandmarker = await HandLandmarker.createFromOptions(vision, {
            baseOptions: {
                modelAssetPath: `https://storage.googleapis.com/mediapipe-models/hand_landmarker/hand_landmarker/float16/1/hand_landmarker.task`,
                delegate: "GPU"
            },
            runningMode: "VIDEO",
            numHands: 2
        });
        landmarkerRef.current = handLandmarker
        console.log("handlandmarker is created!")
        // start capturing - zie hieronder
        // capture()
    };
    createHandLandmarker()
}, []);
```

<br><br><br>

## Start detectie landmarks

De `capture` functie update de state als er nieuwe punten zijn. Je kan de `capture` functie voor het eerst aanroepen nadat je model is geladen. Zodra er poses zijn wordt de state geupdate. 

```js
const capture = async() => {
    if (webcamRef.current && landmarkerRef.current && webcamRef.current.getCanvas()) {
        const video = webcamRef.current.video
        if (video.currentTime > 0) {
            const result = await landmarkerRef.current.detectForVideo(video, performance.now())
            if(result.landmarks) {
                setPoseData(result.landmarks)
            }
        }
    }
    requestAnimationFrame(capture)
}
```


<br><br><br>

# Posedata tonen

Omdat `poseData` nu een `state` is kan je de objecten tonen in je component. 
```jsx
<section>
    <h1>Handen</h1>
    <div>
        {poseData.map((hand, i) => (
            <div key={i}>
                <h2>Hand {i + 1}</h2>
                <ul>
                    {hand.map((landmark, j) => (
                        <li key={j}>
                            {landmark.x}, {landmark.y}, {landmark.z}
                        </li>
                    ))}
                </ul>
            </div>
        ))}
    </div>
</section>
```
Je kan deze hele code ook in een eigen component plaatsen:
```html
<Coordinates poseData={poseData}/>
```
<br><br><br>

# Posedata naar console of canvas

Deze `useEffect` luistert naar state changes. Dit kan je gebruiken als je de coordinaten in de console wil loggen of als je de pose in het canvas wil tekenen.

```js
// Posedata: array van handen. Hand: array van landmarks. Landmark: object met x,y,z
useEffect(() => {
    if(poseData.length > 0) {
        const hand = poseData[0]
        console.log(hand)
    }
}, [poseData]);
```


<br><br><br>

# Posedata tekenen

- Zelf tekenen in canvas element
- DrawingUtils gebruiken

<br>

## Zelf tekenen in canvas element

Je hebt een `useRef` nodig om een referentie naar het canvas te krijgen. 

```js
const canvasRef = useRef(null)
```
```html
<canvas ref={canvasRef} width="480" height="270"></canvas>
```
Zodra `poseData` verandert, wordt deze `useEffect` aangeroepen. Je kan nu de `landmarks` gebruiken om te tekenen. Omdat `x,y,z` getallen zijn van 0 tot 1 moet je het vermenigvuldigen met de afmeting van je canvas. 

```js
useEffect(() => {
    const ctx = canvasRef.current.getContext('2d')
    ctx.fillStyle = 'red';
    for (const hand of poseData) {
        ctx.fillRect(hand[8].x * 480, hand[8].y * 270, 10, 10);
    }
}, [poseData]);
```
### Eigen component

Het is mooier om de canvas en drawing code in een eigen component te zetten. De posedata geef je dan door als props:

```js
<Painting poseData={poseData}/>
```

<br><br>

## DrawingUtils

Je kan de MediaPipe `DrawingUtils` gebruiken om poses te tekenen. Je moet een `new DrawingUtils()` aanmaken bij de eerste `useEffect` call. 

```js
const canvasRef = useRef(null)
useEffect(() => {
    const ctx = canvasRef.current.getContext('2d')
    drawingUtilsRef.current = new DrawingUtils(ctx)
}, [])
```
Daarna kan je de drawing utils aanroepen elke keer dat `poseData` is veranderd. Dit kan je ook in een eigen canvas component plaatsen.
```js
useEffect(() => {
    const ctx = canvasRef.current.getContext('2d')
    if(drawingUtilsRef.current) {
        ctx.clearRect(0, 0, 480, 270)
        for (const hand of poseData) {
            drawingUtilsRef.current.drawConnectors(hand, HandLandmarker.HAND_CONNECTIONS, {color: "#00FF00",lineWidth: 5});
            drawingUtilsRef.current.drawLandmarks(hand, { radius: 4, color: "#FF0000", lineWidth: 2 });
        }
    }
}, [poseData]);
```
### Eigen component

Ook hier is het mooier om de drawingutils code in een eigen component te zetten. De posedata geef je dan door als props:

```js
<Hands poseData={poseData}/>
```
Alle code die met de DrawingUtils te maken heeft verplaats je naar het component:

```js
import { useEffect, useRef } from 'react';
import { PoseLandmarker, HandLandmarker, FilesetResolver, DrawingUtils } from "@mediapipe/tasks-vision";

export default function Hands({ poseData }) {
    const canvasRef = useRef(null)
    const drawingUtilsRef = useRef(null)

    useEffect(() => {
        ...
    }, [poseData]);

    useEffect(() => {
        const ctx = canvasRef.current.getContext('2d')
        drawingUtilsRef.current = new DrawingUtils(ctx)
    }, [])

    return (
        <canvas ref={canvasRef} width="480" height="270"></canvas>
    )
}

```

<br><br><br>


# Finished project

Bekijk de [src](./src) folder voor het complete project.
# Week 7

- Werken met Neural Networks in Javascript
- Data, trainen en model opslaan
- Voorspellingen doen
- Troubleshooting

<br><br><br>

## Neural Networks

![nn](../images/nn.png)

In week 6 hebben we pose data leren herkennen met het "K-Nearest-Neighbour" algoritme. We gaan nu het "Neural Network" algoritme gebruiken. Een aantal voordelen:

- Het KNN model moet altijd alle data onthouden.
- Een KNN model kan erg groot zijn als er veel data is.
- Het NN model kan juist erg klein zijn.
- Een NN is beter in het vinden van complexe of onlogisch lijkende patronen.

<br>
<br>
<br>

# Neural Network

We gaan een Neural Network trainen met de `mediapipe posedata` uit les 5 en 6. Het doel is dat we een kleiner model krijgen dat beter is in het voorspellen van de webcam pose.

Net zoals in week 6 werk je met twee projecten:

- Project 1: laden posedata en trainen van het model
- Project 2: inladen model en daarmee live webcam poses voorspellen

<br>

### Data klaar zetten

Zorg dat je posedata beschikbaar is in je project. Data kan in de vorm van objecten of arrays zijn. In dit voorbeeld ziet onze data er zo uit:

```js
data = [
    {pose:[4,2,5,2,1,...], label:"rock"},
    {pose:[3,1,4,4,1,...], label:"rock"},
    ...
]
```
Voor een neural network is het belangrijk om je data te randomizen:
```js
data.sort(() => (Math.random() - 0.5))
```
<br>

### Neural network

We voegen de [ML5](https://learn.ml5js.org/#/reference/neural-network) library toe aan ons project met een `<script>` tag.

```html
<script src="https://unpkg.com/ml5@latest/dist/ml5.min.js"></script>
```
We maken een neural network aan voor classification. 

```js
const nn = ml5.neuralNetwork({ task: 'classification', debug: true })
```
Je kan je data als volgt toevoegen aan het neural network: het eerste argument is een array van numbers. Het tweede argument is een object met een `label` property:

```js
nn.addData([3,5,2,1,4,3,5,2], {label:"rock"})
```
Je hebt een `for` loop nodig om al je datapunten in de juiste vorm toe te voegen aan het neural network:

```js
for(let item of data) {
    const inputs = item.pose
    const output = { label: item.label }
    nn.addData(inputs, output)
}
```


## Trainen

Bij het trainen moet je aangeven hoeveel `epochs` dit moet duren. Hier kan je zelf mee experimenteren. De blauwe lijn moet zo dicht mogelijk bij de waarde 0 komen. Als hier geen verbetering meer in zit, heb je genoeg epochs.

```javascript
function startTraining() {
    nn.normalizeData()
    nn.train({ epochs: 10 }, () => finishedTraining()) 
}
async function finishedTraining(){
    console.log("Finished training!")
}
```

<img src="../images/epochs.png" width="350">

<br>
<br>
<br>

## Maak een voorspelling

Met de `classify` functie kunnen we nieuwe data voorspellen. Om te testen of het trainen wel goed is gegaan kan je een enkel datapunt uit je originele data array testen:

```js
async function makePrediction() {
    // haal een array met numbers uit je originele data, bijvoorbeeld:
    let test = [3,5,5,3,3,5,6,3,3,...]
    // kijk wat voor label voorspeld wordt
    const results = await nn.classify(test)
    console.log(results)
}
```

<br>
<br>
<br>

## Model opslaan

Omdat je niet telkens opnieuw een model wil trainen gaan we het opslaan.

```js
nn.save("model", () => console.log("model was saved!"))
```
<br>
<br>
<br>

## Model laden

Vanaf dit punt werk je in je tweede project. Hierin wordt de live webcam getoond met poses. Er wordt geen data ingeladen en geen model getraind.

In plaats daarvan maken we een nieuw neural network, waarin we het getrainde model uit de vorige stap inladen.

```js
function createNetwork() {
    const options = { task: 'classification', debug: true }
    nn = ml5.neuralNetwork(options)

    const modelDetails = {
        model: 'model/model.json',
        metadata: 'model/model_meta.json',
        weights: 'model/model.weights.bin'
    }
    nn.load(modelDetails, () => console.log("het model is geladen!"))
}
```
Nadat het model is geladen *(let op de callback functie)*, kan je live posedata uit de webcam gaan voorspellen met het neural network. Verzamel data van één live pose, en roep hiermee de `classify()` functie aan.

<br>
<br>
<br>

# Troubleshooting

### Workflow

Bij het werken met Neural Networks heb je vaak meerdere projecten tegelijk open staan:

- Het project waarin je data verzamelt uit de webcam en er een label aan geeft. 
- Het project waarin je een model aan het trainen bent met de gelabelde data. Hier heb je de webcam input niet nodig.
- Het project waarin je test of je model goed werkt met nieuwe input. Dit kan je doen met testdata of met live webcam input.
- In het eindproduct hoef je niet altijd de pose als lijntjes over het webcam beeld heen te tekenen.

### Asynchrone functies

Een ML5 neural network werkt met *callbacks* en *asynchrone functies*. Dat betekent dat je moet *wachten* totdat een bepaalde taak is afgrond, *voordat* je de volgende taak kan uitvoeren! Bijvoorbeeld:

- Laden van een JSON file met `fetch`
- Trainen van een ML5 Neural Network
- Inladen van een model
- Doen van een voorspelling

> *🚨Een veel voorkomende fout is om te proberen een voorspelling te doen terwijl het trainen nog niet klaar is, of als het model nog niet is ingeladen.*

### Trainen

Het trainen van een model kan makkelijk mis gaan. De meest voorkomende oorzaken:

- De data is niet consistent. De inhoud van elk datapunt *(een array met getallen)* moet voor elk datapunt exact hetzelfde zijn. Als één pose uit 100 punten bestaat, dan moeten alle poses uit 100 punten bestaan.
- De labels kloppen niet of je bent labels vergeten.
- Er is iets mis gegaan bij het opslaan van de posedata. Niet elke pose heeft evenveel getallen, of je hebt getallen opgeslagen als strings. (bv. `pose="5,2,5,2"`)
- Je verzamelde data geef je niet in de juiste vorm door aan het algoritme. In dit voorbeeld gaat het fout omdat alle `fingers` van een handpose zijn opgeslagen in objecten, terwijl het neural network één array met getallen verwacht:

```js
// data uit webcam pose
const data = [
    {pose:[{indexfinger:[4,5,2,2], thumb:[2,4,5,3], ...}], label:"rock"}
    ...
]
// data toevoegen aan neural network gaat fout
nn.addData(data[0].pose, data[0].label)
```

<br>
<br>
<br>

## Documentatie

- [ML5 AI library voor Javascript](https://learn.ml5js.org/#/)
- [ML5 Neural Networks](https://learn.ml5js.org/#/reference/neural-network)
- [ML5 Neural Networks Hidden Layers](./snippets/layers.md)
- [📺 Crash Course Neural Networks](https://www.youtube.com/watch?v=JBlm4wnjNMY)
- [📺 But what is a neural network?](https://www.youtube.com/watch?v=aircAruvnKk)
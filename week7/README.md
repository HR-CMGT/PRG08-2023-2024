# Week 7

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
- Project 2: inladen model en daarmee webcam posedata voorspellen

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

We maken een [ML5](https://learn.ml5js.org/#/reference/neural-network) neural network aan voor classification. 

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

Met de `classify` functie kunnen we nieuwe data voorspellen! Je kan dit snel testen door een pose uit je data array te halen en te kijken of het goede label tevoorschijn komt.

```js
async function makePrediction() {
    // een array met numbers
    // bv. [3,5,5,3,3,5,6,3,3,...]
    let test = data[0].pose

    nn.classify(test)
    const results = await nn.classify(input)
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

In je webcam project kan je nu ook een neural network aanmaken, waarin je meteen het getrainde model inlaadt.

```js
function createNetwork() {
    const options = { task: 'classification', debug: true }
    nn = ml5.neuralNetwork(options)

    const modelDetails = {
        model: 'model/model.json',
        metadata: 'model/model_meta.json',
        weights: 'model/model.weights.bin'
    }
    nn.load(modelDetails, () => modelLoaded())
}
```
Je kan nu posedata uit de webcam gaan voorspellen met het neural network!


<br>
<br>
<br>

# Volgorde in je code

De voorbeeldcode uit dit document moet je in de goede volgorde uitvoeren. Onderstaande acties kosten tijd *(de acties zijn asynchroon)*, wat betekent dat je moet wachten tot het klaar is, voordat je verder kan.

- laden van een JSON file met `fetch`
- trainen van een neural network
- doen van een voorspelling
- inladen van een model

We gebruiken `callback` functies en `async await` om te wachten totdat een voorgaande functie klaar is. We maken `nn` een globale variabele, zodat we deze in alle functies kunnen gebruiken.

<br>
<br>
<br>

## Documentatie

- [ML5 Neural Networks in Javascript](https://learn.ml5js.org/#/reference/neural-network)
- [ML5 Neural Networks hidden layers](./snippets/layers.md)
- [📺 Crash Course Neural Networks](https://www.youtube.com/watch?v=JBlm4wnjNMY)
- [📺 But what is a neural network?](https://www.youtube.com/watch?v=aircAruvnKk)
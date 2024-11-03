# Manual Tecnico
**Luis Manuel Chay Marroquín // 202000343**

## Index
Esta pagina nos permite seleccionar el modelo que queremos usar

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Proyecto 2 IA</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container my-5">
        <h1 class="text-center mb-4">Proyecto 2 IA - 202000343</h1>
        <h3 class="text-center mb-4">Luis Manuel Chay Marroquín</h3>

        <!-- Menú de Pestañas -->
        <ul class="nav nav-tabs">
            <li class="nav-item">
                <a class="nav-link" href="linearRegression.html">Regresión Lineal</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="polynomialRegression.html">Regresión Polinomial</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="decisionTree.html">Árbol de Decisión</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="naiveBayes.html">Naive Bayes</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="neuralNetworks.html">Redes Neuronales</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="kMeans.html">K-Means</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="kNN.html">K-Nearest Neighbors</a>
            </li>
        </ul>

        <p class="mt-4">Selecciona un algoritmo para ver más detalles.</p>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

## Regresión Lineal

Esta pagina nos permite entrenar una regresion lineal haciendo uso de la libreria de tytus, para ello se debe ingresar un archivo csv para entrenar el modelo y luego se puede ingresar los parametros a modificar

```javascript
        google.charts.load('current', { 'packages': ['corechart'] });

        let xTrain = [];
        let yTrain = [];
        let yPredict = [];
        let linear = null;

        // Lee y procesa el archivo CSV cargado y entrena el modelo
        document.getElementById("csvFile").addEventListener("change", function(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const content = e.target.result;
                    alert("Archivo cargado correctamente.");
                    processCSV(content);
                    trainModel();  // Llama al entrenamiento después de procesar el CSV
                };
                reader.readAsText(file);
            }
        });

        // Procesa el contenido del CSV para obtener xTrain e yTrain
        function processCSV(data) {
            const rows = data.split("\n").map(row => row.split(","));
            xTrain = [];
            yTrain = [];
            
            rows.forEach((row, index) => {
                if (index > 0 && row.length >= 2) { // Ignora la cabecera
                    const xValue = parseFloat(row[0]);
                    const yValue = parseFloat(row[1]);
                    if (!isNaN(xValue) && !isNaN(yValue)) {
                        xTrain.push(xValue);
                        yTrain.push(yValue);
                    }
                }
            });

            document.getElementById("log").innerHTML = "Datos cargados desde CSV:<br>X: " + xTrain + "<br>Y: " + yTrain;
        }

        // Entrena el modelo
        function trainModel() {
            if (xTrain.length !== yTrain.length) {
                alert("La cantidad de valores en X debe coincidir con la de Y.");
                return;
            }

            const trainTestSplit = parseInt(document.getElementById("trainTestSplit").value);
            const learningRate = parseFloat(document.getElementById("learningRate").value);
            const iterations = parseInt(document.getElementById("iterations").value);

            linear = new LinearRegression({ 
                learningRate: learningRate, 
                iterations: iterations 
            });

            const trainSize = Math.floor((trainTestSplit / 100) * xTrain.length);
            const xTrainSplit = xTrain.slice(0, trainSize);
            const yTrainSplit = yTrain.slice(0, trainSize);

            linear.fit(xTrainSplit, yTrainSplit);
            yPredict = linear.predict(xTrain);

            document.getElementById("log").innerHTML += 
                '<br>Entrenamiento completado.<br>' +
                'Tasa de Aprendizaje: ' + learningRate + '<br>' +
                'Iteraciones: ' + iterations + '<br>' +
                'X Train: ' + xTrain + '<br>' +
                'Y Train: ' + yTrain;
        }

        document.getElementById("predictButton").addEventListener("click", function () {
            if (yPredict.length > 0) {
                document.getElementById("log").innerHTML += '<br>Predicciones: ' + yPredict;
            } else {
                alert("Primero entrena el modelo.");
            }
        });

        document.getElementById("showGraphButton").addEventListener("click", function () {
            if (yPredict.length > 0) {
                drawChart();
            } else {
                alert("Primero entrena el modelo para ver la gráfica.");
            }
        });

        document.getElementById("evaluateButton").addEventListener("click", function () {
            if (yPredict.length > 0 && linear) {
                const mse = linear.mserror(yTrain, yPredict);
                const r2 = linear.coeficientR2(yTrain, yPredict);
                document.getElementById("log").innerHTML += 
                    '<br>Error Cuadrático Medio (MSE): ' + mse + 
                    '<br>Coeficiente de Determinación (R²): ' + r2;
            } else {
                alert("Primero entrena y realiza predicciones para evaluar el modelo.");
            }
        });

        function drawChart() {
            const dataArray = [['X', 'Y Train', 'Y Predict']];
            for (let i = 0; i < xTrain.length; i++) {
                dataArray.push([xTrain[i], yTrain[i], yPredict[i]]);
            }

            const data = google.visualization.arrayToDataTable(dataArray);
            const options = {
                title: 'Regresión Lineal',
                hAxis: { title: 'X' },
                vAxis: { title: 'Y' },
                seriesType: 'scatter',
                series: { 1: { type: 'line' } }
            };

            const chart = new google.visualization.ComboChart(document.getElementById('graphContainer'));
            chart.draw(data, options);
        }
```

## Regresión Polinomial

Esta pagina nos permite entrenar una regresion polinomial haciendo uso de la libreria de tytus, para ello se debe ingresar un archivo csv para entrenar el modelo y luego se puede ingresar los parametros a modificar

```javascript
         google.charts.load('current', { 'packages': ['corechart'] });

        function joinArrays() {
            var a = [];
            if (arguments.length == 10) {
                a.push([arguments[0], arguments[2], arguments[4], arguments[6], arguments[8]]);
                for (var i = 0; i < arguments[1].length; i++) {
                    a.push([arguments[1][i], arguments[3][i], arguments[5][i], arguments[7][i], arguments[9][i]]);
                }
            }
            return a;
        }

        let xTrain = [];
        let yTrain = [];
        let predictArray = [];
        let yPredict, yPredict2, yPredict3;
        let r2, r22, r23;

        // Lee y procesa el archivo CSV
        document.getElementById("csvInput").addEventListener("change", function (event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function (e) {
                const csvData = e.target.result;
                const lines = csvData.split("\n");

                // Arrays temporales para X, Y y X_to_predict
                let csvX = [];
                let csvY = [];
                let csvPredict = [];

                for (let i = 1; i < lines.length; i++) { // Saltar encabezado
                    const [x, y, xPredict] = lines[i].split(",").map(Number);
                    if (!isNaN(x) && !isNaN(y) && !isNaN(xPredict)) {
                        csvX.push(x);
                        csvY.push(y);
                        csvPredict.push(xPredict);
                    }
                }

                if (csvX.length !== csvY.length || csvX.length === 0) {
                    alert("El archivo CSV no es válido o está vacío.");
                    return;
                }

                // Asignar datos desde CSV a los arrays de entrenamiento y predicción
                xTrain = csvX;
                yTrain = csvY;
                predictArray = csvPredict;
                alert("Datos cargados exitosamente desde el archivo CSV.");
            };

            reader.readAsText(file);
        });

        document.getElementById("trainButton").addEventListener("click", function () {
            if (xTrain.length === 0 || yTrain.length === 0 || predictArray.length === 0) {
                alert("Primero cargue un archivo CSV válido.");
                return;
            }

            const trainTestSplit = parseInt(document.getElementById("trainTestSplit").value, 10) / 100;
            const trainSize = Math.floor(xTrain.length * trainTestSplit);

            const xTrainSubset = xTrain.slice(0, trainSize);
            const yTrainSubset = yTrain.slice(0, trainSize);
            const polynomial = new PolynomialRegression();

            // Entrena el modelo para distintos grados
            polynomial.fit(xTrainSubset, yTrainSubset, 2);
            yPredict = polynomial.predict(predictArray);
            r2 = polynomial.getError();

            polynomial.fit(xTrainSubset, yTrainSubset, 3);
            yPredict2 = polynomial.predict(predictArray);
            r22 = polynomial.getError();

            polynomial.fit(xTrainSubset, yTrainSubset, 4);
            yPredict3 = polynomial.predict(predictArray);
            r23 = polynomial.getError();

            alert("Entrenamiento completado.");
        });

        document.getElementById("predictButton").addEventListener("click", function () {
            if (yPredict && yPredict2 && yPredict3) {
                document.getElementById("log1").innerHTML = 'X Train: [' + xTrain + ']';
                document.getElementById("log2").innerHTML = 'Y Train: [' + yTrain + ']';
                document.getElementById("log3").innerHTML = 'X To Predict: [' + predictArray + ']';
                document.getElementById("log4").innerHTML = 'Y Prediction Degree 2: [' + yPredict + ']';
                document.getElementById("log5").innerHTML = 'Y Prediction Degree 3: [' + yPredict2 + ']';
                document.getElementById("log6").innerHTML = 'Y Prediction Degree 4: [' + yPredict3 + ']';
                document.getElementById("log7").innerHTML = 'R^2 for Degree 2: ' + r2.toFixed(2);
                document.getElementById("log8").innerHTML = 'R^2 for Degree 3: ' + r22.toFixed(2);
                document.getElementById("log9").innerHTML = 'R^2 for Degree 4: ' + r23.toFixed(2);
            } else {
                alert("Primero entrena el modelo.");
            }
        });

        document.getElementById("showGraphButton").addEventListener("click", function () {
            if (yPredict && yPredict2 && yPredict3) {
                drawChart();
            } else {
                alert("Primero entrena el modelo para ver la gráfica.");
            }
        });

        function drawChart() {
            const dataArray = joinArrays('x', predictArray, 'Training', yTrain, 'Prediction Degree 2', yPredict, 'Prediction Degree 3', yPredict2, 'Prediction Degree 4', yPredict3);

            const data = google.visualization.arrayToDataTable(dataArray);
            const options = {
                title: 'Regresión Polinomial para Distintos Grados',
                seriesType: 'scatter',
                series: {
                    1: { type: 'line' },
                    2: { type: 'line' },
                    3: { type: 'line' }
                }
            };

            const chart = new google.visualization.ComboChart(document.getElementById('graphContainer'));
            chart.draw(data, options);
        }
```

## Árbol de Decisión

Esta pagina nos permite entrenar un arbol de decision haciendo uso de la libreria de tytus, para ello se debe ingresar un archivo JSON para entrenar el modelo y luego se puede ingresar los parametros a modificar

```javascript
        let header = [];
        let trainingData = [];
        let dTree = null;
        let root = null;

        // Cargar datos desde archivo JSON y generar el árbol de decisión
        document.getElementById('loadJson').onclick = function () {
            const fileInput = document.getElementById('jsonFile');
            const file = fileInput.files[0];
            
            if (file) {
                const reader = new FileReader();
                
                reader.onload = function(e) {
                    const jsonData = JSON.parse(e.target.result);
                    
                    // Asignar encabezado y datos de entrenamiento
                    header = jsonData.header;
                    trainingData = jsonData.training_data;

                    console.log("Datos JSON cargados:");
                    console.log("Encabezado:", header);
                    console.log("Datos de entrenamiento:", trainingData);

                    // Generar el árbol con los datos de entrenamiento
                    alert("Datos cargados correctamente. Generando árbol de decisión...");
                    generateTree();
                };
                
                reader.readAsText(file);
            } else {
                alert("Por favor, selecciona un archivo JSON.");
            }
        };

        function generateTree() {
            var chart = document.getElementById("tree");
            var arrData = [header].concat(trainingData);
            dTree = new DecisionTreeID3(arrData);
            root = dTree.train(dTree.dataset);

            console.log("Árbol de decisión generado.");

            // Generar y mostrar el gráfico del árbol
            var dotStr = dTree.generateDotString(root);
            var parsDot = vis.network.convertDot(dotStr);
            var data = { nodes: parsDot.nodes, edges: parsDot.edges };
            var options = {
                layout: {
                    hierarchical: {
                        levelSeparation: 100,
                        nodeSpacing: 100,
                        parentCentralization: true,
                        direction: 'UD',
                        sortMethod: 'directed'
                    },
                },
            };
            var network = new vis.Network(chart, data, options);
        }

        // Realizar la predicción cuando el usuario ingrese los datos y haga clic en "Predecir"
        document.getElementById('predict').onclick = function () {
            if (!root || !dTree) {
                alert("Por favor, carga los datos desde JSON y genera el árbol antes de intentar predecir.");
                return;
            }

            var pred = document.getElementById('data').value;
            var arrPred = pred.split(",").map(item => item.trim());
            
            console.log("Datos de predicción ingresados:", arrPred);

            var predHeader = header.slice(0, header.length - 1);
            let predictNode = dTree.predict([predHeader, arrPred], root);

            if (predictNode) {
                document.getElementById('prediction').innerText = header[header.length - 1] + ": " + predictNode.value;
                console.log("Predicción realizada:", predictNode.value);
            } else {
                document.getElementById('prediction').innerText = "No se pudo generar una predicción con los datos proporcionados.";
                console.error("No se generó ninguna predicción válida. Verifica los datos de entrada.");
            }
        };
```

## Naive Bayes

Esta pagina nos permite entrenar un naive bayes haciendo uso de la libreria de tytus, para ello se debe ingresar un archivo JSON para entrenar el modelo y luego se puede ingresar los parametros a modificar

```javascript
         const nb = new NaiveBayes();

        // Cargar datos desde JSON
        document.getElementById("loadJson").onclick = () => {
            const fileInput = document.getElementById("jsonFile");
            const file = fileInput.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    const jsonData = JSON.parse(e.target.result);
                    jsonData.causes.forEach((cause) => {
                        nb.insertCause(cause.name, cause.array);
                    });
                    console.log("Causas cargadas:", nb.causes);
                    alert("Datos cargados correctamente.");
                };
                reader.readAsText(file);
            } else {
                alert("Por favor, selecciona un archivo JSON.");
            }
        };

        // Agregar causa manualmente
        document.getElementById("addCause").onclick = () => {
            const causeName = document.getElementById("causeName").value.trim();
            const causeValues = document.getElementById("causeValues").value.split(",").map((v) => v.trim());
            nb.insertCause(causeName, causeValues);
            console.log("Causa agregada:", causeName, causeValues);
        };

        // Realizar predicción
        document.getElementById("predict").onclick = () => {
            const effect = document.getElementById("effect").value.trim();
            const eventsInput = document.getElementById("events").value.trim().split(";");
            const events = eventsInput.map((e) => e.split(",").map((item) => item.trim()));
            const result = nb.predict(effect, events);
            document.getElementById("predictionResult").innerText = `Resultado: ${result[0]}, Probabilidad: ${result[1]}`;
        };
```

## Redes Neuronales

Esta pagina nos permite entrenar una red neuronal haciendo uso de la libreria de tytus, para ello se debe ingresar un archivo CSV para entrenar el modelo y luego se puede ingresar los parametros a modificar

```javascript
         let trainingExamples = [];

        document.getElementById('csvFileInput').addEventListener('change', function(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const content = e.target.result.trim();
                    const rows = content.split("\n");
                    // Eliminar la primera fila (encabezado) y cargar solo los datos
                    trainingExamples = rows.slice(1).map(line => line.split(",").map(Number));
                    alert("Datos de entrenamiento cargados exitosamente");
                };
                reader.readAsText(file);
            }
        });

        document.getElementById('predict1').onclick = function () {
            const design = [2, 4, 3, 2];
            const n1 = parseInt(document.getElementById('n1_1').value);
            const n2 = parseInt(document.getElementById('n2_1').value);
            const brain = new NeuralNetwork(design);

            if (trainingExamples.length === 0) {
                alert("Cargue un archivo CSV con datos de entrenamiento");
                return;
            }

            trainingExamples.forEach(([num1, num2]) => {
                brain.Entrenar([num1, num2], num1 > num2 ? [1, 0] : [0, 1]);
            });

            const prediction = brain.Predecir([n1, n2]).map(value => value.toFixed(5));
            document.getElementById("prediction_1").innerText = `Predicción [${n1}, ${n2}]: ${prediction}`;
        };

        document.getElementById('predict2').onclick = function () {
            const design = [2, 4, 3, 2];
            const n1 = parseInt(document.getElementById('n1_2').value);
            const n2 = parseInt(document.getElementById('n2_2').value);
            const brain = new NeuralNetwork(design);

            if (trainingExamples.length === 0) {
                alert("Cargue un archivo CSV con datos de entrenamiento");
                return;
            }

            trainingExamples.forEach(([num1, num2]) => {
                brain.Entrenar([num1, num2], num1 < num2 ? [1, 0] : [0, 1]);
            });

            const prediction = brain.Predecir([n1, n2]).map(value => value.toFixed(5));
            document.getElementById("prediction_2").innerText = `Predicción [${n1}, ${n2}]: ${prediction}`;
        };

        document.getElementById('entrenarBoton').onclick = function () {
            alert("Modelo entrenado exitosamente");
        };
```

## K-Means

Esta pagina nos permite entrenar un k-means haciendo uso de la libreria de tytus, para ello se debe ingresar un archivo CSV para entrenar el modelo y luego se puede ingresar los parametros a modificar

```javascript
        document.getElementById('calculate_button').onclick = function () {
            const type = document.getElementById('type_select').value;
            const clusterCount = parseInt(document.getElementById('cluster_count').value);
            const iterations = parseInt(document.getElementById('iterations').value);
            const fileInput = document.getElementById('data_file');

            if (!fileInput.files.length) {
                alert('Por favor, seleccione un archivo CSV.');
                return;
            }

            const file = fileInput.files[0];
            const reader = new FileReader();
            reader.onload = function(event) {
                
                const csvData = event.target.result;
                const parsedData = parseCSV(csvData, type);
                
                if (parsedData.length < clusterCount) {
                    alert(`El número de clusters (${clusterCount}) no puede ser mayor que la cantidad de datos (${parsedData.length})`);
                    return;
                }

                if (type === 'lineal') {
                    calculateLinealKMeans(clusterCount, iterations, parsedData);
                } else {
                    calculate2DKMeans(clusterCount, iterations, parsedData);
                }

                alert("KMeans calculado correctamente.");
            };
            reader.readAsText(file);
        };

        function parseCSV(data, type) {
            const rows = data.trim().split('\n');
            if (type === 'lineal') {
                return rows.slice(1).map(row => parseFloat(row));
            } else {
                return rows.slice(1).map(row => {
                    const [x, y] = row.split(',').map(Number);
                    return [x, y];
                });
            }
        }

        function calculateLinealKMeans(clusterCount, iterations, data) {
            const kmeans = new LinearKMeans(clusterCount, data);
            const clusterizedData = kmeans.clusterize(clusterCount, data, iterations);

            const clusters = Array.from(new Set(clusterizedData.map(a => a[1]))).map(cluster => [cluster, getRandomColor()]);
            google.charts.load('current', { 'packages': ['corechart'] });
            google.charts.setOnLoadCallback(() => drawChartLineal(clusterizedData, clusters));
        }

        function calculate2DKMeans(clusterCount, iterations, data) {
            const kmeans = new _2DKMeans(clusterCount, data);
            const clusterizedData = kmeans.clusterize(clusterCount, data, iterations);

            const clusters = Array.from(new Set(clusterizedData.map(a => JSON.stringify(a[1]))))
                .map(cluster => [JSON.parse(cluster), getRandomColor()]);
            google.charts.load('current', { 'packages': ['corechart'] });
            google.charts.setOnLoadCallback(() => drawChart2D(clusterizedData, clusters));
        }

        function drawChartLineal(clusterizedData, clusters) {
            const graphData = new google.visualization.DataTable();
            graphData.addColumn('number', 'X');
            graphData.addColumn('number', 'Y');
            graphData.addColumn({ type: 'string', role: 'style' });

            clusterizedData.forEach(e => {
                graphData.addRow([e[0], 0, `point { size: 7; shape-type: diamond; fill-color: ${clusters.find(c => c[0] === e[1])[1]}`]);
            });

            clusters.forEach(c => {
                graphData.addRow([c[0], 0, `point { size: 10; shape-type: square; fill-color: #FF0000`]);
            });

            const options = {
                title: 'Distribución Lineal de Clusters',
                seriesType: 'scatter',
                hAxis: { title: 'X' },
                vAxis: { title: 'Y' },
                legend: 'none'
            };

            const chart = new google.visualization.ScatterChart(document.getElementById('chart_div'));
            chart.draw(graphData, options);
        }

        function drawChart2D(clusterizedData, clusters) {
            const graphData = new google.visualization.DataTable();
            graphData.addColumn('number', 'X');
            graphData.addColumn('number', 'Y');
            graphData.addColumn({ type: 'string', role: 'style' });

            clusterizedData.forEach(e => {
                graphData.addRow([e[0][0], e[0][1], `point { size: 7; shape-type: diamond; fill-color: ${clusters.find(c => JSON.stringify(c[0]) === JSON.stringify(e[1]))[1]}`]);
            });

            clusters.forEach(c => {
                graphData.addRow([c[0][0], c[0][1], `point { size: 10; shape-type: square; fill-color: #FF0000`]);
            });

            const options = {
                title: 'Distribución de Clusters en 2D',
                seriesType: 'scatter',
                hAxis: { title: 'X' },
                vAxis: { title: 'Y' },
                legend: 'none'
            };

            const chart = new google.visualization.ScatterChart(document.getElementById('chart_div'));
            chart.draw(graphData, options);
        }

        function getRandomColor() {
            return "#" + Math.floor(Math.random() * 16777215).toString(16);
        }

        document.getElementById("entrenarBoton").onclick = function () {
            alert("Modelo entrenado");
        };
```

## K-Nearest Neighbors

Esta pagina nos permite entrenar un k-nearest neighbors haciendo uso de la libreria de tytus, para ello se debe ingresar un archivo CSV para entrenar el modelo y luego se puede ingresar los parametros a modificar

```javascript
        let knn;
        let ind = [], x2 = [], y2 = [], z = [], group = [];
        let point = null;

        function cargarDatosDesdeJSON() {
            const fileInput = document.getElementById("jsonFileInput").files[0];
            if (!fileInput) return alert("Por favor selecciona un archivo JSON de datos.");

            const reader = new FileReader();
            reader.onload = function(event) {
                alert("Datos cargados correctamente.");
                const data = JSON.parse(event.target.result);

                if (data.length !== 7 || data[0].length !== 5) {
                    alert("El archivo JSON debe contener exactamente 7 filas y 5 columnas en la estructura esperada.");
                    return;
                }

                ind = data.map(row => row[0]);
                x2 = data.map(row => row[1]);
                y2 = data.map(row => row[2]);
                z = data.map(row => row[3]);
                group = data.map(row => row[4]);

                mostrarDatosEnTabla(data);
                inicializarKNN();
                drawChart();
            };
            reader.readAsText(fileInput);
        }

        function mostrarDatosEnTabla(data) {
            let tablaHTML = "<table class='table table-bordered'><thead><tr><th>Index</th><th>X</th><th>Y</th><th>Z</th><th>Group</th></tr></thead><tbody>";
            data.forEach(fila => {
                tablaHTML += `<tr><td>${fila[0]}</td><td>${fila[1]}</td><td>${fila[2]}</td><td>${fila[3]}</td><td>${fila[4]}</td></tr>`;
            });
            tablaHTML += "</tbody></table>";
            document.getElementById("tabla").innerHTML = tablaHTML;
        }

        function inicializarKNN() {
            const individuals = x2.map((x, i) => [x, y2[i], z[i], group[i]]);
            knn = new KNearestNeighbor(individuals);
        }

        function cargarPuntoDesdeTexto() {
            const input = document.getElementById("pointInput").value.trim();
            const partes = input.split(" ");

            if (partes.length !== 4) {
                alert("Por favor ingresa el punto en el formato correcto: ID X Y Z");
                return;
            }

            // Extraer coordenadas y convertir a número
            const x = parseFloat(partes[1]);
            const y = parseFloat(partes[2]);
            const zVal = parseFloat(partes[3]);

            if (isNaN(x) || isNaN(y) || isNaN(zVal)) {
                alert("Asegúrate de ingresar valores numéricos para X, Y y Z.");
                return;
            }

            point = [x, y, zVal];
            calcularDistancias();
        }

        function calcularDistancias() {
            if (!knn || !point) return alert("Primero carga los datos de entrenamiento y el punto de evaluación.");

            // Calcular distancias
            const euc = knn.euclidean(point).join(", ");
            const man = knn.manhattan(point).join(", ");
            document.getElementById("logE").innerText = euc;
            document.getElementById("logM").innerText = man;
        }

        // Cargar y dibujar el gráfico
        google.charts.load("current", { packages: ["corechart"] });
        
        function drawChart() {
            if (x2.length === 0 || y2.length === 0 || group.length === 0) return;

            const data = new google.visualization.DataTable();
            data.addColumn('number', 'X');
            data.addColumn('number', 'Y');
            data.addColumn({ type: 'string', role: 'style' });
            data.addColumn({ type: 'string', role: 'annotation' });

            // Asigna colores y formas específicas según el grupo y añade las etiquetas
            x2.forEach((x, i) => {
                let color = '';
                let shape = '';

                switch (group[i]) {
                    case 'I':
                        color = 'red';
                        shape = 'square';
                        break;
                    case 'II':
                        color = 'orange';
                        shape = 'triangle';
                        break;
                    case 'III':
                        color = 'blue';
                        shape = 'circle';
                        break;
                }

                data.addRow([x, y2[i], `point {fill-color: ${color}; shape-type: ${shape};}`, ind[i]]);
            });

            const options = {
                legend: 'none',
                pointSize: 20,
                hAxis: { title: 'X' },
                vAxis: { title: 'Y' },
                annotations: {
                    textStyle: {
                        fontSize: 12,
                        bold: true,
                    }
                }
            };

            const chart = new google.visualization.ScatterChart(document.getElementById('chart_div'));
            chart.draw(data, options);
        }
```


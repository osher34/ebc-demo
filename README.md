<!DOCTYPE html>
<html>
<head>
    <title>EBC Live Challenge Demo</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background: linear-gradient(to bottom right, #0f2027, #203a43, #2c5364);
            color: white;
            padding-top: 50px;
        }
        h1 {
            font-size: 28px;
        }
        button {
            padding: 15px 25px;
            font-size: 18px;
            border: none;
            border-radius: 8px;
            background-color: #00c6ff;
            color: black;
            cursor: pointer;
        }
        #status {
            margin-top: 30px;
            font-size: 22px;
        }
        #score {
            font-size: 40px;
            margin-top: 20px;
            font-weight: bold;
        }
    </style>
</head>
<body>

<h1>EBC Human Stability Challenge</h1>
<p>Hold your phone steady for 5 seconds</p>

<button onclick="startChallenge()">Start Challenge</button>

<div id="status"></div>
<div id="score"></div>

<script>
let readings = [];
let collecting = false;

function startChallenge() {
    readings = [];
    document.getElementById("score").innerText = "";
    document.getElementById("status").innerText = "Collecting data...";
    collecting = true;

    requestPermission();

    setTimeout(() => {
        collecting = false;
        calculateScore();
    }, 5000);
}

function requestPermission() {
    if (typeof DeviceMotionEvent.requestPermission === 'function') {
        DeviceMotionEvent.requestPermission()
            .then(permissionState => {
                if (permissionState === 'granted') {
                    window.addEventListener('devicemotion', handleMotion);
                }
            })
            .catch(console.error);
    } else {
        window.addEventListener('devicemotion', handleMotion);
    }
}

function handleMotion(event) {
    if (!collecting) return;

    let x = event.accelerationIncludingGravity.x;
    let y = event.accelerationIncludingGravity.y;
    let z = event.accelerationIncludingGravity.z;

    let magnitude = Math.sqrt(x*x + y*y + z*z);
    readings.push(magnitude);
}

function calculateScore() {
    if (readings.length === 0) return;

    let mean = readings.reduce((a,b)=>a+b,0) / readings.length;
    let variance = readings.reduce((a,b)=>a + Math.pow(b-mean,2),0) / readings.length;

    let humanScore = Math.min(100, variance * 50);

    document.getElementById("status").innerText = "Analysis Complete";
    document.getElementById("score").innerText = "Human Stability Score: " + humanScore.toFixed(0) + "%";
}
</script>

</body>
</html>

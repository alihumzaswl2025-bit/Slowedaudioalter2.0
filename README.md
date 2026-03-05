# Slowedaudioalter2.0
https://yourusername.github.io/audioalter2.0/
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AudioAlter 2.0 - Slowed Reverb</title>
<style>
    body {
        background: #0f0f0f;
        color: white;
        font-family: Arial, sans-serif;
        text-align: center;
        padding: 40px;
    }

    h1 {
        font-size: 32px;
        margin-bottom: 20px;
    }

    input, button {
        margin: 10px;
        padding: 10px;
        border-radius: 8px;
        border: none;
        font-size: 16px;
    }

    button {
        background: #ff0055;
        color: white;
        cursor: pointer;
    }

    button:hover {
        background: #ff3366;
    }

    audio {
        margin-top: 20px;
        width: 80%;
    }
</style>
</head>
<body>

<h1>🎵 Slowed + Reverb Converter</h1>

<input type="file" id="audioFile" accept="audio/*">
<br>

<label>Speed:</label>
<input type="range" id="speedControl" min="0.5" max="1" step="0.1" value="0.8">
<br>

<label>Reverb Level:</label>
<input type="range" id="reverbControl" min="0" max="5" step="0.1" value="2">
<br>

<button onclick="processAudio()">Apply Effect</button>

<br>
<audio id="audioPlayer" controls></audio>

<script>
let audioContext;
let source;
let convolver;
let gainNode;

function createReverb(context, seconds) {
    const rate = context.sampleRate;
    const length = rate * seconds;
    const impulse = context.createBuffer(2, length, rate);
    
    for (let channel = 0; channel < 2; channel++) {
        const impulseData = impulse.getChannelData(channel);
        for (let i = 0; i < length; i++) {
            impulseData[i] = (Math.random() * 2 - 1) * Math.pow(1 - i / length, 2);
        }
    }

    const convolver = context.createConvolver();
    convolver.buffer = impulse;
    return convolver;
}

function processAudio() {
    const file = document.getElementById('audioFile').files[0];
    if (!file) {
        alert("Please upload a song first!");
        return;
    }

    const speed = document.getElementById('speedControl').value;
    const reverbAmount = document.getElementById('reverbControl').value;

    const reader = new FileReader();
    reader.onload = function(e) {
        audioContext = new (window.AudioContext || window.webkitAudioContext)();

        audioContext.decodeAudioData(e.target.result, function(buffer) {
            source = audioContext.createBufferSource();
            source.buffer = buffer;
            source.playbackRate.value = speed;

            convolver = createReverb(audioContext, reverbAmount);

            gainNode = audioContext.createGain();
            gainNode.gain.value = 1;

            source.connect(convolver);
            convolver.connect(gainNode);
            gainNode.connect(audioContext.destination);

            source.start(0);
        });
    };

    reader.readAsArrayBuffer(file);

    document.getElementById("audioPlayer").src = URL.createObjectURL(file);
}
</script>

</body>
</html>

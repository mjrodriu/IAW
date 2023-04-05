# PROYECTO FIN DE CICLO
Con este proyecto se ha creado una pequeña "plataforma" dirigída al aprendizaje de algunas letras y números en lenguaje de signos.
Cabe destacar que este proyecto no se ha realizado desde cero, sino que se ha utilizado fuentes de código abierto de otros autores, sin embargo se han mosificado hasta conseguir
nuestro objetivo.
**Fuente**: https://www.npmjs.com/package/fingerpose?activeTab=readme#demo 

## EJEMPLO.HTML (html principal)
```HTML
<!DOCTYPE html>
<html>
<head>
	<title>Botones con logos</title>
	<style>
		body {
			display: flex;
			justify-content: center;
			align-items: center;
			height: 100vh;
			margin: 0;
			background: linear-gradient(to bottom, #00FFFF,#738EE4);
		}

		button {
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			width: 150px;
			height: 150px;
			margin: 0 10px;
			border: none;
			border-radius: 50%;
			background-color: #ffffff;
			box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.3);
			cursor: pointer;
		}

		button:hover {
			transform: scale(1.1);
			transition: all 0.3s ease;
		}

		button img {
			width: 80px;
			height: 80px;
			object-fit: contain;
			margin-bottom: 10px;
		}
        video{
      position:absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      object-fit: cover;
}
	</style>
	<script>
		function enviar1() {
		window.location.href = 'paco.html';
}
		function enviar2() {
    window.location.href = 'finger.html';
}
    function enviar3() {
    window.location.href = 'paco.html';
}

function enviar(param) {
	
    if (param === "uno") {
		window.location.href = 'fingernumero.html';
	}
	else if(param === "dos"){
		window.location.href = 'finger.html';
	}
	else if(param === "tres"){	
		window.location.href = 'demo.html';
	}
}
	</script>
</head>
<body>

	<button name="ejemplo1" id="uno" onclick="enviar('uno')">
		<img src="ejemplo1.png" alt="Logo 1" >
	</button>
	<button name="ejemplo2" id="dos" onclick="enviar('dos')">
		<img src="ejemplo2.png" alt="Logo 2" >
	</button>
	<button id="tres" onclick="enviar('tres')">
		<img src="ejemplo3.png" alt="Logo 3" >
	</button>

</body>
</html>
```
## FINGER.HTML
```HTML
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html" charset="utf-8"/>
  <title>FingerPose Example</title>

  <!-- Require the peer dependencies of handpose. -->
  <script src="https://unpkg.com/@tensorflow/tfjs-core@3.7.0/dist/tf-core.js"></script>
  <script src="https://unpkg.com/@tensorflow/tfjs-converter@3.7.0/dist/tf-converter.js"></script>

  <!-- You must explicitly require a TF.js backend if you're not using the tfs union bundle. -->
  <script src="https://unpkg.com/@tensorflow/tfjs-backend-webgl@3.7.0/dist/tf-backend-webgl.js"></script>

  <!-- The main handpose library -->
  <script src="https://unpkg.com/@tensorflow-models/handpose@0.0.7/dist/handpose.js"></script>

  <!-- The fingerpose library -->
  <script src="index.js" type="text/javascript"></script>

  <style>

    * {
      box-sizing: border-box;
      user-select: none;
    }

    html, body {
      width: 100%;
      height: 100%;
      overflow: hidden;
      font-family: Arial, sans-serif;
      background-color: #ffffff;
      color: #333333;
    }

    body {
      margin: 0;
      padding: 0;
    }

    .container {
      margin: 20px auto;
      display: flex;
    }

    .video,
    .debug {
      padding: 0 20px;
    }

    table.summary {
      border: 1px solid #333;
      border-collapse: collapse;
    }

    table.summary td,
    table.summary th {
      border: 1px solid #333;
      padding: 5px 8px;
    }

    #video-container {
      width: 640px;
      height: 480px;
      position: relative;
    }

    .layer {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
    }

    #pose-video {
      transform: scaleX(-1);
    }

    #pose-result {
      font-size: 100px;
      text-align: right;
      padding: 20px 30px 0 0;
    }
  </style>
</head>
<body>

  <div class="container">

    <div class="video">
      <div id="video-container">
        <video id="pose-video" class="layer" playsinline></video>
        <canvas id="pose-canvas" class="layer"></canvas>
        <div id="pose-result" class="layer"></div>
      </div>
    </div>

    <div class="debug">
      <table class="summary">
        <thead>
          <tr>
            <th>Idx</th>
            <th>Finger</th>
            <th style="width: 110px">Curl</th>
            <th style="width: 170px">Direction</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>0</td>
            <td>Thumb</td>
            <td><span id="curl-0">-</span></td>
            <td><span id="dir-0">-</span></td>
          </tr>
          <tr>
            <td>1</td>
            <td>Index</td>
            <td><span id="curl-1">-</span></td>
            <td><span id="dir-1">-</span></td>
          </tr>
          <tr>
            <td>2</td>
            <td>Middle</td>
            <td><span id="curl-2">-</span></td>
            <td><span id="dir-2">-</span></td>
          </tr>
          <tr>
            <td>3</td>
            <td>Ring</td>
            <td><span id="curl-3">-</span></td>
            <td><span id="dir-3">-</span></td>
          </tr>
          <tr>
            <td>4</td>
            <td>Pinky</td>
            <td><span id="curl-4">-</span></td>
            <td><span id="dir-4">-</span></td>
          </tr>
        </tbody>
      </table>
    </div>

  </div>

  <script>

    const config = {
      video: { width: 640, height: 480, fps: 30 }
    };

    const landmarkColors = {
      thumb: 'red',
      indexFinger: 'blue',
      middleFinger: 'yellow',
      ringFinger: 'green',
      pinky: 'pink',
      palmBase: 'white'
    };

    const gestureStrings = {
      'letraA' : 'A',
      'letraB' : 'B',
      'letraC' : 'C',
      'letraD' : 'D',
      'letraE' : 'E',
      'letraF' : 'F',
      'letraG' : 'G',
      'letraH': 'H',
      'letraI': 'I',
      'letraK': 'K',
      'letraL': 'L',
      'letraN' : 'N',
      'letraM' : 'M',
      'letraO' : 'O',
      'letraP' : 'P',
      'letraQ' : 'Q',
      'letraR': ' R',
      'letraS' : 'S',
      'letraT': 'T',
      'victory': 'U'
    };

    async function main() {

      const video = document.querySelector("#pose-video");
      const canvas = document.querySelector("#pose-canvas");
      const ctx = canvas.getContext("2d");

      const resultLayer = document.querySelector("#pose-result");
      
    // LETRA A
      const letraA = new fp.GestureDescription('letraA');
      letraA.addCurl(fp.Finger.Thumb, fp.FingerCurl.HalfCurl);
      letraA.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalUp, 1.0); 
      letraA.addCurl(fp.Finger.Index, fp.FingerCurl.FullCurl, 1.0);
      letraA.addDirection(fp.Finger.Index, fp.FingerDirection.DiagonalUpLeft, 1.0);
      letraA.addCurl(fp.Finger.Middle, fp.FingerCurl.FullCurl, 1.0);
      letraA.addDirection(fp.Finger.Middle, fp.FingerDirection.DiagonalUpLeft, 1.0);
      letraA.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      letraA.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      letraA.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      letraA.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalUp, 1.0);

    // LETRA B   
      const letraB = new fp.GestureDescription('letraB');
      letraB.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraB.addDirection(fp.Finger.Thumb, fp.FingerDirection.HorizontalRight, 1.0); 
      letraB.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraB.addDirection(fp.Finger.Index, fp.FingerDirection.HorizontalRight, 1.0);      
      letraB.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      letraB.addDirection(fp.Finger.Middle, fp.FingerDirection.HorizontalRight, 1.0);
      letraB.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
      letraB.addDirection(fp.Finger.Ring, fp.FingerDirection.HorizontalRight, 1.0);
      letraB.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
      letraB.addDirection(fp.Finger.Pinky, fp.FingerDirection.HorizontalRight, 1.0);

    // LETRA C
      const letraC = new fp.GestureDescription('letraC');
      letraC.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraC.addDirection(fp.Finger.Thumb, fp.FingerDirection.HorizontalLeft, 1.0);
      letraC.addCurl(fp.Finger.Index, fp.FingerCurl.HalfCurl, 1.0);
      letraC.addDirection(fp.Finger.Index, fp.FingerDirection.DiagonalUpLeft, 1.0);
      letraC.addCurl(fp.Finger.Middle, fp.FingerCurl.HalfCurl, 1.0);
      letraC.addDirection(fp.Finger.Middle, fp.FingerDirection.DiagonalUpLeft, 1.0);
      letraC.addCurl(fp.Finger.Ring, fp.FingerCurl.HalfCurl, 1.0);
      letraC.addDirection(fp.Finger.Ring, fp.FingerDirection.DiagonalUpLeft, 1.0);
      letraC.addCurl(fp.Finger.Pinky, fp.FingerCurl.HalfCurl, 1.0);
      letraC.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpLeft, 1.0);
 
    // LETRA D    
      const letraD = new fp.GestureDescription('letraD');
      letraD.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraD.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0);      
      letraD.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraD.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);     
      letraD.addCurl(fp.Finger.Middle, fp.FingerCurl.HalfCurl, 1.0);
      letraD.addDirection(fp.Finger.Middle, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraD.addCurl(fp.Finger.Ring, fp.FingerCurl.HalfCurl, 1.0);
      letraD.addDirection(fp.Finger.Ring, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraD.addDirection(fp.Finger.Ring, fp.FingerDirection.HorizontalRight, 1.0);
      letraD.addCurl(fp.Finger.Pinky, fp.FingerCurl.HalfCurl, 1.0);
      letraD.addDirection(fp.Finger.Pinky, fp.FingerDirection.HorizontalRight, 1.0);
    
    // LETRA E    
      const letraE = new fp.GestureDescription('letraE');
      letraE.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraE.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0);      
      letraE.addCurl(fp.Finger.Index, fp.FingerCurl.FullCurl, 1.0);
      letraE.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);     
      letraE.addCurl(fp.Finger.Middle, fp.FingerCurl.FullCurl, 1.0);
      letraE.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      letraE.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      letraE.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      letraE.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      letraE.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalUp, 1.0);
     
    // LETRA F   
      const letraF = new fp.GestureDescription('letraF');
      letraF.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraF.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0);      
      letraF.addCurl(fp.Finger.Index, fp.FingerCurl.FullCurl, 1.0);
      letraF.addCurl(fp.Finger.Index, fp.FingerCurl.HalfCurl, 1.0);
      letraF.addDirection(fp.Finger.Index, fp.FingerDirection.DiagonalUpRight, 1.0);     
      letraF.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      letraF.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      letraF.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
      letraF.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      letraF.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
      letraF.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpLeft, 1.0);

    // LETRA G
      const letraG = new fp.GestureDescription('letraG');
      letraG.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraG.addDirection(fp.Finger.Thumb, fp.FingerDirection.HorizontalLeft, 1.0);
      letraG.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraG.addDirection(fp.Finger.Index, fp.FingerDirection.HorizontalLeft, 1.0);
      letraG.addCurl(fp.Finger.Middle, fp.FingerCurl.FullCurl, 1.0);
      letraG.addDirection(fp.Finger.Middle, fp.FingerDirection.DiagonalU, 1.0);
      letraG.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      letraG.addDirection(fp.Finger.Ring, fp.FingerDirection.HorizontalLeft, 1.0);
      letraG.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      letraG.addDirection(fp.Finger.Pinky, fp.FingerDirection.HorizontalLeft, 1.0);

    // LETRA H
      const letraH = new fp.GestureDescription('letraH');
      letraH.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl,1.0);
      letraH.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalUp, 1.0); 
      letraH.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraH.addDirection(fp.Finger.Index, fp.FingerDirection.DiagonalUpLeft, 1.0); 
      letraH.addCurl(fp.Finger.Middle, fp.FingerCurl.FullCurl, 1.0);
      letraH.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      letraH.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      letraH.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      letraH.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
      letraH.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpRight, 1.0);
    
    // LETRA I
      const letraI = new fp.GestureDescription('letraI');
      letraI.addCurl(fp.Finger.Thumb, fp.FingerCurl.HalfCurl,1.0);
      letraI.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl,1.0);
      letraI.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0); 
      letraI.addCurl(fp.Finger.Index, fp.FingerCurl.FullCurl, 1.0);
      letraI.addDirection(fp.Finger.Index, fp.FingerDirection.DiagonalUpRight, 1.0); 
      letraI.addCurl(fp.Finger.Middle, fp.FingerCurl.FullCurl, 1.0);
      letraI.addDirection(fp.Finger.Middle, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraI.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      letraI.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      letraI.addDirection(fp.Finger.Ring, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraI.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
      letraI.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalUp, 1.0);

    // LETRA K
      const letraK = new fp.GestureDescription('letraK');
      letraK.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl,1.0);
      letraK.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0); 
      letraK.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraK.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0); 
      letraK.addCurl(fp.Finger.Middle, fp.FingerCurl.FullCurl, 1.0);
      letraK.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      letraK.addDirection(fp.Finger.Middle, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraK.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      letraK.addDirection(fp.Finger.Ring, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraK.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      letraK.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpRight, 1.0);   
      
    // LETRA L
      const letraL = new fp.GestureDescription('letraL');
      letraL.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl,1.0);
      letraL.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0); 
      letraL.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraL.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0); 
      letraL.addCurl(fp.Finger.Middle, fp.FingerCurl.FullCurl, 1.0);
      letraL.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      letraL.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      letraL.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      letraL.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      letraL.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpLeft, 1.0);   

    // Letra M
      const letraM = new fp.GestureDescription('letraM');
      letraM.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraM.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalDown, 1.0);
      letraM.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraM.addDirection(fp.Finger.Index, fp.FingerDirection.DiagonalDownRight, 1.0);
      letraM.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      letraM.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalDown, 1.0);
      letraM.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
      letraM.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalDown, 1.0);
      letraM.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      letraM.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalDown, 1.0);

      // Letra N
      const letraN = new fp.GestureDescription('letraN');
      letraN.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraN.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalDown, 1.0);
      letraN.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraN.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalDown, 1.0);
      letraN.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      letraN.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalDown, 1.0);
      letraN.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      letraN.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalDown, 1.0);
      letraN.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      letraN.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalDown, 1.0);
 
    // Letra O  
      const letraO = new fp.GestureDescription('letraO');
      letraO.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraO.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpLeft, 1.0); 
      letraO.addCurl(fp.Finger.Index, fp.FingerCurl.HalfCurl, 1.0);
      letraO.addDirection(fp.Finger.Index, fp.FingerDirection.DiagonalUpLeft, 1.0);
      letraO.addCurl(fp.Finger.Middle, fp.FingerCurl.HalfCurl, 1.0);
      letraO.addDirection(fp.Finger.Middle, fp.FingerDirection.DiagonalUpLeft, 1.0);
      letraO.addCurl(fp.Finger.Ring, fp.FingerCurl.HalfCurl, 1.0);
      letraO.addDirection(fp.Finger.Ring, fp.FingerDirection.DiagonalUpLeft, 1.0);
      letraO.addCurl(fp.Finger.Pinky, fp.FingerCurl.HalfCurl, 1.0);
      letraO.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpLeft, 1.0);

      // También hay que poner en caso de hacerlo con la izquierda
      letraO.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraO.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraO.addDirection(fp.Finger.Thumb, fp.FingerDirection.HorizontalRight, 1.0); 
      letraO.addCurl(fp.Finger.Index, fp.FingerCurl.HalfCurl, 1.0);
      letraO.addDirection(fp.Finger.Index, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraO.addCurl(fp.Finger.Middle, fp.FingerCurl.HalfCurl, 1.0);
      letraO.addCurl(fp.Finger.Middle, fp.FingerCurl.FullCurl, 1.0);
      letraO.addDirection(fp.Finger.Middle, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraO.addCurl(fp.Finger.Ring, fp.FingerCurl.HalfCurl, 1.0);
      letraO.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      letraO.addDirection(fp.Finger.Ring, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraO.addCurl(fp.Finger.Pinky, fp.FingerCurl.HalfCurl, 1.0);
      letraO.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      letraO.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpRight, 1.0);

    // Letra P  
      const letraP = new fp.GestureDescription('letraP');
      letraP.addCurl(fp.Finger.Thumb, fp.FingerCurl.HalfCurl);
      letraP.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraP.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalUp, 1.0);
      letraP.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraP.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
      letraP.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      letraP.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      letraP.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
      letraP.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      letraP.addCurl(fp.Finger.Pinky, fp.FingerCurl.HalfCurl, 1.0);
      letraP.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      letraP.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalUp, 1.0);
     
    // letra Q  
      const letraQ = new fp.GestureDescription('letraQ');
      letraQ.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraQ.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalUp, 0.9);
      letraQ.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraQ.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
      letraQ.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      letraQ.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      letraQ.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
      letraQ.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      letraQ.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
      letraQ.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalUp, 1.0);

    // letra R  
      const letraR = new fp.GestureDescription('letraR');
      letraR.addCurl(fp.Finger.Thumb, fp.FingerCurl.HalfCurl, 1.0);
      letraR.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraR.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalUp, 1.0);
      letraR.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraR.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
      letraR.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      letraR.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      letraR.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      letraR.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      letraR.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      letraR.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraR.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalUp, 1.0);


    // Letra S 
      const letraS = new fp.GestureDescription('letraS');
      letraS.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraS.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0); 
      letraS.addCurl(fp.Finger.Index, fp.FingerCurl.HalfCurl, 1.0);
      letraS.addDirection(fp.Finger.Index, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraS.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      letraS.addCurl(fp.Finger.Middle, fp.FingerCurl.HalfCurl, 1.0);
      letraS.addDirection(fp.Finger.Middle, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraS.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
      letraS.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      letraS.addDirection(fp.Finger.Ring, fp.FingerDirection.DiagonalUpRight, 1.0);
      letraS.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
      letraS.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalUp, 1.0);

    // Letra T 
      const letraT = new fp.GestureDescription('letraT');
      letraT.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      letraT.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalUp, 1.0); 
      letraT.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      letraT.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
      letraT.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      letraT.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      letraT.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
      letraT.addDirection(fp.Finger.Ring, fp.FingerDirection.DiagonalUpLeft, 1.0);
      letraT.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      letraT.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
      letraT.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpLeft, 1.0);      

      const knownGestures = [
        fp.Gestures.VictoryGesture,
        letraA,
        letraB,
        letraC,
        letraD,
        letraE,
        letraF,
        letraG,
        letraH,
        letraI,
        letraK,
        letraL,
        letraM,
        letraN,
        letraO,
        letraP,
        letraQ,
        letraR,
        letraS,
        letraT     
      ];

      
      const GE = new fp.GestureEstimator(knownGestures);
      // load handpose model
      const model = await handpose.load();
      console.log("Handpose model loaded");

      // main estimation loop
      const estimateHands = async () => {

        // clear canvas overlay
        ctx.clearRect(0, 0, config.video.width, config.video.height);
        resultLayer.innerText = '';

        // get hand landmarks from video
        // Note: Handpose currently only detects one hand at a time
        // Therefore the maximum number of predictions is 1
        const predictions = await model.estimateHands(video, true);

        for(let i = 0; i < predictions.length; i++) {

          // draw colored dots at each predicted joint position
          for(let part in predictions[i].annotations) {
            for(let point of predictions[i].annotations[part]) {
              drawPoint(ctx, point[0], point[1], 3, landmarkColors[part]);
            }
          }

          // estimate gestures based on landmarks
          // using a minimum score of 9 (out of 10)
          // gesture candidates with lower score will not be returned
          const est = GE.estimate(predictions[i].landmarks, 9);

          if(est.gestures.length > 0) {

            // find gesture with highest match score
            let result = est.gestures.reduce((p, c) => { 
              return (p.score > c.score) ? p : c;
            });

            resultLayer.innerText = gestureStrings[result.name];
          }

          // update debug info
          updateDebugInfo(est.poseData);
        }

        // ...and so on
        setTimeout(() => { estimateHands(); }, 1000 / config.video.fps);
      };

      estimateHands();
      console.log("Starting predictions");
    }

    async function initCamera(width, height, fps) {

      const constraints = {
        audio: false,
        video: {
          facingMode: "user",
          width: width,
          height: height,
          frameRate: { max: fps }
        }
      };

      const video = document.querySelector("#pose-video");
      video.width = width;
      video.height = height;

      // get video stream
      const stream = await navigator.mediaDevices.getUserMedia(constraints);
      video.srcObject = stream;

      return new Promise(resolve => {
        video.onloadedmetadata = () => { resolve(video) };
      });
    }

    function drawPoint(ctx, x, y, r, color) {
      ctx.beginPath();
      ctx.arc(x, y, r, 0, 2 * Math.PI);
      ctx.fillStyle = color;
      ctx.fill();
    }

    function updateDebugInfo(data) {
      for(let fingerIdx in data) {
        document.getElementById("curl-" + fingerIdx).innerText = data[fingerIdx][1];
        document.getElementById("dir-" + fingerIdx).innerText = data[fingerIdx][2];
      }
    }

    window.addEventListener("DOMContentLoaded", () => {

      initCamera(
        config.video.width, config.video.height, config.video.fps
      ).then(video => {
        video.play();
        video.addEventListener("loadeddata", event => {
          console.log("Camera is ready");
          main();
        });
      });

      const canvas = document.querySelector("#pose-canvas");
      canvas.width = config.video.width;
      canvas.height = config.video.height;
      console.log("Canvas initialized");
    });
   
  </script>

</body>
</html>
```
## JUEGO.JS
```JAVASCRIPT 
const express = require('express')
const app = express()
const bodyParser = require('body-parser')
const mysql = require('mysql')


var mysqlConnection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '',
  database: 'andel',
  multipleStatements: true
  
});


mysqlConnection.connect((err)=> {
  if(!err)
  console.log('Conexion bbdd correcta...');
  else
  console.log('Connection Failed!'+ JSON.stringify(err,undefined,2));
  });

  //especificamos el subdirectorio donde se encuentran las páginas estáticas
app.use(express.static(__dirname + '/paginas'))

//extended: false significa que parsea solo string (no archivos de imagenes por ejemplo)
app.use(bodyParser.urlencoded({ extended: true }))



app.post('/ingreso', (req, res) => {
  let Nombre = req.body.nombre
  let Apellido = req.body.apellido
 
  var sql= `INSERT alumnos (nombre, apellidos) VALUES ( '${Nombre}', '${Apellido}')`;
  console.log (sql);
  //lanzo la query
  mysqlConnection.query(sql, (err) => {
    if (!err) {
      res.redirect("ejemplo.html");
    }
    else {
      console.log ("Error al añadir usuario");
    }
  })});  

var server = app.listen(8888, () => {
  console.log('Servidor web iniciado')
});
```
## FINGERNUMERO.HTML
``` HTML
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html" charset="utf-8"/>
  <title>FingerPose Example</title>

  <!-- Require the peer dependencies of handpose. -->
  <script src="https://unpkg.com/@tensorflow/tfjs-core@3.7.0/dist/tf-core.js"></script>
  <script src="https://unpkg.com/@tensorflow/tfjs-converter@3.7.0/dist/tf-converter.js"></script>

  <!-- You must explicitly require a TF.js backend if you're not using the tfs union bundle. -->
  <script src="https://unpkg.com/@tensorflow/tfjs-backend-webgl@3.7.0/dist/tf-backend-webgl.js"></script>

  <!-- The main handpose library -->
  <script src="https://unpkg.com/@tensorflow-models/handpose@0.0.7/dist/handpose.js"></script>

  <!-- The fingerpose library -->
  <script src="index.js" type="text/javascript"></script>

  <style>

    * {
      box-sizing: border-box;
      user-select: none;
    }

    html, body {
      width: 100%;
      height: 100%;
      overflow: hidden;
      font-family: Arial, sans-serif;
      background-color: #ffffff;
      color: #333333;
    }

    body {
      margin: 0;
      padding: 0;
    }

    .container {
      margin: 20px auto;
      display: flex;
    }

    .video,
    .debug {
      padding: 0 20px;
    }

    table.summary {
      border: 1px solid #333;
      border-collapse: collapse;
    }

    table.summary td,
    table.summary th {
      border: 1px solid #333;
      padding: 5px 8px;
    }

    #video-container {
      width: 640px;
      height: 480px;
      position: relative;
    }

    .layer {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
    }

    #pose-video {
      transform: scaleX(-1);
    }

    #pose-result {
      font-size: 100px;
      text-align: right;
      padding: 20px 30px 0 0;
    }
  </style>
</head>
<body>

  <div class="container">

    <div class="video">
      <div id="video-container">
        <video id="pose-video" class="layer" playsinline></video>
        <canvas id="pose-canvas" class="layer"></canvas>
        <div id="pose-result" class="layer"></div>
      </div>
    </div>

    <div class="debug">
      <table class="summary">
        <thead>
          <tr>
            <th>Idx</th>
            <th>Finger</th>
            <th style="width: 110px">Curl</th>
            <th style="width: 170px">Direction</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>0</td>
            <td>Thumb</td>
            <td><span id="curl-0">-</span></td>
            <td><span id="dir-0">-</span></td>
          </tr>
          <tr>
            <td>1</td>
            <td>Index</td>
            <td><span id="curl-1">-</span></td>
            <td><span id="dir-1">-</span></td>
          </tr>
          <tr>
            <td>2</td>
            <td>Middle</td>
            <td><span id="curl-2">-</span></td>
            <td><span id="dir-2">-</span></td>
          </tr>
          <tr>
            <td>3</td>
            <td>Ring</td>
            <td><span id="curl-3">-</span></td>
            <td><span id="dir-3">-</span></td>
          </tr>
          <tr>
            <td>4</td>
            <td>Pinky</td>
            <td><span id="curl-4">-</span></td>
            <td><span id="dir-4">-</span></td>
          </tr>
        </tbody>
      </table>
    </div>

  </div>

  <script>

    const config = {
      video: { width: 640, height: 480, fps: 30 }
    };

    const landmarkColors = {
      thumb: 'red',
      indexFinger: 'blue',
      middleFinger: 'yellow',
      ringFinger: 'green',
      pinky: 'pink',
      palmBase: 'white'
    };

    const gestureStrings = {
      'numero1' : '1',
      'victory' : '2',
      'numero3' : '3',
      'numero4' : '4',
      'numero5' : '5',
      'numero6' : '6',
      'numero7' : '7',
      'numero8' : '8',
      'numero9' : '9',
      'thumbs_up' : '10'
        };

    async function main() {

      const video = document.querySelector("#pose-video");
      const canvas = document.querySelector("#pose-canvas");
      const ctx = canvas.getContext("2d");
      const resultLayer = document.querySelector("#pose-result");
      
    // NUMERO 1
      const numero1 = new fp.GestureDescription('numero1');
      numero1.addCurl(fp.Finger.Thumb, fp.FingerCurl.HalfCurl);
      numero1.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0); 
      numero1.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalUp, 1.0); 
      numero1.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      numero1.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
      numero1.addCurl(fp.Finger.Middle, fp.FingerCurl.FullCurl, 1.0);
      numero1.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      numero1.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      numero1.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      numero1.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      numero1.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpRight, 1.0);


    // NUMERO 3
      const numero3 = new fp.GestureDescription('numero3');
      numero3.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      numero3.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpLeft, 1.0); 
      numero3.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      numero3.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
      numero3.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      numero3.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      numero3.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      numero3.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      numero3.addCurl(fp.Finger.Pinky, fp.FingerCurl.FullCurl, 1.0);
      numero3.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpRight, 1.0);


    // NUMERO 4
        const numero4 = new fp.GestureDescription('numero4');
        numero4.addCurl(fp.Finger.Thumb, fp.FingerCurl.HalfCurl);
        numero4.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0); 
        numero4.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
        numero4.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
        numero4.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
        numero4.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
        numero4.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
        numero4.addDirection(fp.Finger.Ring, fp.FingerDirection.DiagonalUpRight, 1.0);
        numero4.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
        numero4.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpRight, 1.0);


    // NUMERO 5
        const numero5 = new fp.GestureDescription('numero5');
        numero5.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
        numero5.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpLeft, 1.0); 
        numero5.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
        numero5.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
        numero5.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
        numero5.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
        numero5.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
        numero5.addDirection(fp.Finger.Ring, fp.FingerDirection.DiagonalUpRight, 1.0);
        numero5.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
        numero5.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpRight, 1.0);


    // NUMERO 6  
      const numero6 = new fp.GestureDescription('numero6');
      numero6.addCurl(fp.Finger.Thumb, fp.FingerCurl.HalfCurl);
      numero6.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpRight, 1.0);
      numero6.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      numero6.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
      numero6.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      numero6.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      numero6.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
      numero6.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      numero6.addCurl(fp.Finger.Pinky, fp.FingerCurl.HalfCurl, 1.0);
      numero6.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalUp, 1.0);
     

   // NUMERO 7
   
      const numero7 = new fp.GestureDescription('numero7');
      numero7.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      numero7.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalUp, 1.0);
      numero7.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      numero7.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
      numero7.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      numero7.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      numero7.addCurl(fp.Finger.Ring, fp.FingerCurl.FullCurl, 1.0);
      numero7.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      numero7.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
      numero7.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpRight, 1.0);
     
 // NUMERO 8 - IZQ
   
      const numero8 = new fp.GestureDescription('numero8');
      numero8.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      numero8.addDirection(fp.Finger.Thumb, fp.FingerDirection.VerticalUp, 1.0);
      numero8.addCurl(fp.Finger.Index, fp.FingerCurl.NoCurl, 1.0);
      numero8.addDirection(fp.Finger.Index, fp.FingerDirection.DiagonalUpRight, 1.0);
      numero8.addCurl(fp.Finger.Middle, fp.FingerCurl.FullCurl, 1.0);
      numero8.addDirection(fp.Finger.Middle, fp.FingerDirection.DiagonalUpRight, 1.0);
      numero8.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
      numero8.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      numero8.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
      numero8.addDirection(fp.Finger.Pinky, fp.FingerDirection.VerticalUp, 1.0);
     
 // NUMERO 9
   
      const numero9 = new fp.GestureDescription('numero9');
      numero9.addCurl(fp.Finger.Thumb, fp.FingerCurl.NoCurl);
      numero9.addDirection(fp.Finger.Thumb, fp.FingerDirection.DiagonalUpLeft, 1.0);
      numero9.addCurl(fp.Finger.Index, fp.FingerCurl.HalfCurl, 1.0);
      numero9.addDirection(fp.Finger.Index, fp.FingerDirection.VerticalUp, 1.0);
      numero9.addCurl(fp.Finger.Middle, fp.FingerCurl.NoCurl, 1.0);
      numero9.addDirection(fp.Finger.Middle, fp.FingerDirection.VerticalUp, 1.0);
      numero9.addCurl(fp.Finger.Ring, fp.FingerCurl.NoCurl, 1.0);
      numero9.addDirection(fp.Finger.Ring, fp.FingerDirection.VerticalUp, 1.0);
      numero9.addCurl(fp.Finger.Pinky, fp.FingerCurl.NoCurl, 1.0);
      numero9.addDirection(fp.Finger.Pinky, fp.FingerDirection.DiagonalUpRight, 1.0);

      const knownGestures = [
      numero1,
      fp.Gestures.VictoryGesture,
      numero3, 
      numero4, 
      numero5,
      numero6,
      numero7,
      numero8,
      numero9,
      fp.Gestures.ThumbsUpGesture
      ];

      
      const GE = new fp.GestureEstimator(knownGestures);
      // load handpose model
      const model = await handpose.load();
      console.log("Handpose model loaded");

      // main estimation loop
      const estimateHands = async () => {

        // clear canvas overlay
        ctx.clearRect(0, 0, config.video.width, config.video.height);
        resultLayer.innerText = '';

        // get hand landmarks from video
        // Note: Handpose currently only detects one hand at a time
        // Therefore the maximum number of predictions is 1
        const predictions = await model.estimateHands(video, true);

        for(let i = 0; i < predictions.length; i++) {

          // draw colored dots at each predicted joint position
          for(let part in predictions[i].annotations) {
            for(let point of predictions[i].annotations[part]) {
              drawPoint(ctx, point[0], point[1], 3, landmarkColors[part]);
            }
          }

          // estimate gestures based on landmarks
          // using a minimum score of 9 (out of 10)
          // gesture candidates with lower score will not be returned
          const est = GE.estimate(predictions[i].landmarks, 9);

          if(est.gestures.length > 0) {

            // find gesture with highest match score
            let result = est.gestures.reduce((p, c) => { 
              return (p.score > c.score) ? p : c;
            });

            resultLayer.innerText = gestureStrings[result.name];
          }

          // update debug info
          updateDebugInfo(est.poseData);
        }

        // ...and so on
        setTimeout(() => { estimateHands(); }, 1000 / config.video.fps);
      };

      estimateHands();
      console.log("Starting predictions");
    }

    async function initCamera(width, height, fps) {

      const constraints = {
        audio: false,
        video: {
          facingMode: "user",
          width: width,
          height: height,
          frameRate: { max: fps }
        }
      };

      const video = document.querySelector("#pose-video");
      video.width = width;
      video.height = height;

      // get video stream
      const stream = await navigator.mediaDevices.getUserMedia(constraints);
      video.srcObject = stream;

      return new Promise(resolve => {
        video.onloadedmetadata = () => { resolve(video) };
      });
    }

    function drawPoint(ctx, x, y, r, color) {
      ctx.beginPath();
      ctx.arc(x, y, r, 0, 2 * Math.PI);
      ctx.fillStyle = color;
      ctx.fill();
    }

    function updateDebugInfo(data) {
      for(let fingerIdx in data) {
        document.getElementById("curl-" + fingerIdx).innerText = data[fingerIdx][1];
        document.getElementById("dir-" + fingerIdx).innerText = data[fingerIdx][2];
      }
    }

    window.addEventListener("DOMContentLoaded", () => {

      initCamera(
        config.video.width, config.video.height, config.video.fps
      ).then(video => {
        video.play();
        video.addEventListener("loadeddata", event => {
          console.log("Camera is ready");
          main();
        });
      });

      const canvas = document.querySelector("#pose-canvas");
      canvas.width = config.video.width;
      canvas.height = config.video.height;
      console.log("Canvas initialized");
    });
   
  </script>

</body>
</html>
```


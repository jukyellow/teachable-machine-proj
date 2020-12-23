# image-classification-mobile-desktop

- For Mobile(Iphone 7 checked!) and Desktop Export(Javascript) Sample!  

- DESC: Mobile Camera Device -> Stream -> Vedio(HTML5) -> Canvas(HTML5) capture image -> predict image  

```javascript
	////////////////////////////////////////////////////
	// Mobile - Iphone 7 checked!
	////////////////////////////////////////////////////
	// Set constraints for the video stream
	var cameraView = null;
	var cameraSensor = null; //canvas
	var constraints;
	//var track = null;
	// Define constants
	async function init_mob_cam(btn){
		document.getElementById("loading").style.display="block";
		const modelURL = URL + "model.json";
        const metadataURL = URL + "metadata.json";
		
		// load the model and metadata
        // Refer to tmImage.loadFromFiles() in the API to support files from a file picker
        // or files from your local hard drive
		// Note: the pose library adds "tmImage" object to your window (window.tmImage)
        model = await tmImage.load(modelURL, metadataURL);
		maxPredictions = model.getTotalClasses();
		document.getElementById("loading").style.display="none";
		
		labelContainer = document.getElementById("label-container");
        for (let i = 0; i < maxPredictions; i++) { // and class labels
            labelContainer.appendChild(document.createElement("div"));
        }
		
		cameraView = document.querySelector("#camera--view");
		cameraSensor = document.querySelector("#camera--sensor");
		constraints = { video: { facingMode: "user" }, audio: false };
		cameraStart();
	}
	
	// Access the device camera and stream to cameraView
	async function cameraStart() {
		navigator.mediaDevices
			.getUserMedia(constraints)
			.then(function(stream) {
				//track = stream.getTracks()[0];
				cameraView.srcObject = stream;
			})
			.catch(function(error) {
				console.error("Oops. Something is broken.", error);
			});
	}
	
	async function capture_predict(){
		cameraSensor.width = cameraView.videoWidth;
		cameraSensor.height = cameraView.videoHeight;
		await cameraSensor.getContext("2d").drawImage(cameraView, 0, 0)
		
		await predict_mob(cameraSensor); //transfer canvas
	}
	
	async function predict_mob(canvas) {
		// predict can take in an image, video or canvas html element
        const prediction = await model.predict(canvas);
		for (let i = 0; i < maxPredictions; i++) {
		    const classPrediction =
                prediction[i].className + ": " + prediction[i].probability.toFixed(2);
		    labelContainer.childNodes[i].innerHTML = classPrediction;
		}
  }
```

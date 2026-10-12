# Quantum and Classical MNIST Comparison

This project is an educational comparison tool for classifying handwritten MNIST digits with three model types:

- A classical convolutional neural network (CNN) implemented with PyTorch.
- A hybrid neural network that combines a PyTorch CNN with a Qiskit quantum neural network.
- A quantum neural network (QNN) classifier implemented with Qiskit Machine Learning.

The web application lets a user draw a digit, send it to a Flask API, and display the predicted label. The trained models and MNIST data used by the project are included in the repository.

## Repository Layout

| Path | Purpose |
| --- | --- |
| `backend_apis.py` | Flask server and prediction upload endpoints |
| `backend_cnn.py` | PyTorch CNN definition, model loading, and preprocessing |
| `backend_hybrid.py` | Hybrid CNN/QNN definition, training helpers, and model loading |
| `backend_qnn.py` | QNN model loading and 28x28-to-8x8 preprocessing |
| `trial.html` / `trial.js` | More complete comparison interface with separate prediction panels |
| `frontend.html` / `frontend.js` | Simpler drawing interface |
| `frontend.css` | Styles for the simpler interface |
| `*.ipynb` | Experiments and training notebooks |
| `mnist_model.pt` | Saved classical CNN weights |
| `model4.pt` | Saved hybrid model weights |
| `real_qcnn_classifier.model` | Saved QNN classifier |
| `data/MNIST` | Local MNIST dataset files |
| `images/` | Diagrams and screenshots used by the pages |
| `digits_sample/` | Example and uploaded digit images |

## How It Works

1. The browser draws a white digit on a black canvas.
2. JavaScript scales the canvas to 28x28 pixels and uploads it as a PNG using `multipart/form-data`.
3. Flask saves the upload in `digits_sample/` and passes it to the selected model.
4. The selected backend converts the image to the format expected by its model and returns JSON such as:

```json
{"label": 1}
```

The classical and hybrid models classify the two-digit MNIST task used by the demo. The QNN preprocessing reduces the image to an 8x8 representation and currently returns `0`, `1`, or `"Unknown"`.

## Requirements

Use a Python environment with the packages imported by the backend:

- Flask and Flask-CORS
- Pillow
- NumPy
- PyTorch and TorchVision
- Matplotlib
- scikit-image
- Qiskit
- Qiskit Machine Learning

The project does not currently include a `requirements.txt`. A typical PowerShell setup is:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install flask flask-cors pillow numpy matplotlib scikit-image torch torchvision qiskit qiskit-machine-learning
```

Qiskit and Qiskit Machine Learning versions must be mutually compatible with the serialized model files. If loading `real_qcnn_classifier.model` or `model4.pt` fails after installation, use the versions from the environment in which those artifacts were trained.

## Run the API

From the project directory:

```powershell
python backend_apis.py
```

The Flask development server listens on `http://127.0.0.1:5000` by default. On startup, the server loads all three model backends, so all model files and their dependencies must be available even if only one endpoint is used.

## API Endpoints

All upload endpoints expect a `POST` request with a file field named `file`.

| Method and route | Model | Result |
| --- | --- | --- |
| `POST /upload_and_predict_cnn` | Classical PyTorch CNN | Predicted digit label |
| `POST /upload_and_predict_hybrid` | Hybrid CNN/QNN model | Predicted `0` or `1` |
| `POST /upload_and_predict_qnn` | Qiskit QNN classifier | Predicted `0`, `1`, or `"Unknown"` |
| `POST /predict` | Placeholder route | Always returns label `0` |

Example request:

```powershell
curl.exe -X POST -F "file=@digits_sample\drawing_0.png" http://127.0.0.1:5000/upload_and_predict_cnn
```

Expected response shape:

```json
{"label": 0}
```

## Open the Frontend

After starting the API, open `trial.html` in a browser and use its drawing and prediction controls. The JavaScript sends requests to `http://127.0.0.1:5000`.

Opening HTML files directly is sufficient; no separate frontend build step is required. If the browser blocks requests from a local file, serve the directory with any simple static file server and open the resulting local URL. CORS is enabled by the Flask application for this development setup.

## Training and Experiments

The notebooks contain the project experiments and training work:

- `mnist_cnn_torchvision.ipynb` explores the classical CNN.
- `Hybrid_MNIST_ZZ.ipynb` explores the hybrid CNN/QNN model using a `ZZFeatureMap` and `RealAmplitudes` ansatz.
- `Quantum_Convolution_Neural_Network.ipynb` contains the quantum convolutional experiments.

The training functions in `backend_cnn.py` and `backend_hybrid.py` are present for experimentation, but the API loads the saved model artifacts instead of retraining at startup.

## License

See [LICENSE.md](LICENSE.md).

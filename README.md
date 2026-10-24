# Customer Satisfaction Analysis Using Facial Detection

Group project (team of 4) for the Foundations of Artificial Intelligence module, MSc Artificial Intelligence, National College of Ireland, April 2025.

The system estimates customer satisfaction from a facial expression photo taken when an order is collected, but only if the customer agrees.

## My part

I worked on the CNN models: training them on facial emotion images and comparing three models. I also worked on the consent pop-up. The model training notebook is not in this repo yet.

## Model comparison (from the project report)

| Model | Accuracy |
|---|---|
| CNN | 74% |
| EfficientNetB0 | 82.3% |
| ResNet-50 | 85.71% |

ResNet-50 was chosen for the application.

## How the application works

1. `order.html`: the customer clicks Place Order. A consent pop-up asks if a photo can be taken. The answer (Yes or No) is saved with the order.
2. `backend.py` (Flask): saves each order in MongoDB with a `hasConsented` flag and an order number.
3. `delivery.html`: shows the active orders. A webcam photo is taken only for orders where the customer said Yes.
4. `backend.py` encrypts the photo and stores it in a separate MongoDB collection, then calls `model.py`.
5. `model.py`: finds the face (OpenCV Haar Cascade), resizes it to 48x48, predicts seven emotions with the trained model, and turns them into a satisfaction score from 0 to 100.
6. `details.html`: shows the captured orders.
7. `metrics.html`: shows the average satisfaction score per day, week or month (Chart.js). Only completed orders with consent are counted.

## Not included

The trained model file (`model.h5`) and the emotion dataset are not in this repo, so the code will not run as it is. `backend.py` also expects a MongoDB server, set in the connection string near the top.

## Changes from the submitted version

- `details.html`: removed a test request that sent each order photo to httpbin.org (a public test service). Photos should never leave the system.

## Known issues

- The consent pop-up in `order.html` says the photo "will not be stored or shared", but the backend stores it, encrypted. The pop-up text should be updated to say this.
- The 24 hour photo deletion does not work reliably. Photos are saved without a timestamp, and the code that adds timestamps updates every stored photo each time a new one arrives, so photos can be kept longer than 24 hours.
- The encryption key is created each time the server starts and is not saved, so stored photos cannot be decrypted after a restart.
- If no face is found in the photo, `model.py` raises an error instead of returning a result.
- The API has no login, allows requests from any origin, and runs in debug mode. It is a coursework prototype, not production code.
- `model.py` contains a simple CNN training function. The ResNet-50 comparison code is not part of this repo.

## Tech

Python, Flask, MongoDB, TensorFlow, Keras, OpenCV, HTML, CSS, JavaScript, Chart.js.

# Bengaluru House Price Prediction

A small end-to-end example that predicts house prices in Bangalore.

This project contains a trained regression model (pickle), a Flask API that serves predictions, and a simple static frontend to collect user inputs and display estimated prices.

## Contents

- `model/main.ipynb` - Jupyter notebook (data exploration, preprocessing, model training) used to create the saved artifacts.
- `server/server.py` - Flask application exposing API endpoints used by the frontend.
- `server/util.py` - Utility functions to load artifacts and make predictions (`get_estimated_price`, `get_location_names`).
- `server/artifacts/banglore_home_prices_model.pickle` - Pickled trained model (sklearn).
- `server/artifacts/columns.json` - JSON file listing `data_columns` used by the model (first three are `total_sqft`, `bath`, `bhk`; the rest are location one-hot columns).
- `client/app.html` - Static frontend page to accept inputs and show results.
- `client/app.js` - Frontend logic to fetch locations and call the prediction endpoint.
- `client/app.css` - Styles for the frontend.

## Quick start (run locally)

1. (Optional) Create and activate a Python virtual environment.

2. Install dependencies. There is no `requirements.txt` in this repo; install the minimal packages used:

```powershell
pip install flask numpy scikit-learn pandas
```

Note: The exact `scikit-learn` version used for saving the pickled model should match when loading; if you get an unpickling error, install the same sklearn version used when the model was created.

3. Start the Flask server from the project root:

```powershell
python "server/server.py"
```

You should see: "Starting Python Flask Server For Home Price Prediction..." and the server will listen on `http://127.0.0.1:5000` by default.

4. Open the frontend: open `client/app.html` in your browser (double-click the file or open via the browser "Open File"). The page will fetch available locations from the backend and allow you to submit inputs to get a predicted price.

## API

- GET `/get_location_names`
  - Response: `{ "locations": ["location1", "location2", ...] }`

- POST `/predict_home_price`
  - Expected form fields (x-www-form-urlencoded): `total_sqft`, `bhk`, `bath`, `location`
  - Response: `{ "estimated_price": <number> }` (value returned in the same units your model was trained on; frontend displays it as "Lakh").

## How predictions work (implementation notes)

- `server/util.py` loads `columns.json` and the pickled model. It builds a feature vector where:
  - index 0 = `total_sqft`
  - index 1 = `bath`
  - index 2 = `bhk`
  - indexes 3... = one-hot encoded locations (lowercase)
- If a location is not found in `data_columns`, the model currently leaves all location one-hot positions as 0 (i.e., treats it as unknown).

## Recommendations & next steps

- Add a `requirements.txt` with pinned versions (e.g. `Flask==x.y.z`, `scikit-learn==x.y.z`, `numpy`, `pandas`).
- Add input validation on both frontend and backend (numeric checks, missing values).
- Provide better error responses from the backend (HTTP error codes + JSON messages).
- Consider saving a scikit-learn `Pipeline` (preprocessing + estimator) so you can avoid manual feature-vector construction.
- Add unit tests for `server/util.py` and a small integration test for the Flask endpoints.
- Dockerize the app for reproducible deployment.

## Re-training the model

- Use `model/main.ipynb` to re-run preprocessing and training. After training, export:
  - The model as `server/artifacts/banglore_home_prices_model.pickle` (use `joblib` or `pickle` but keep sklearn version compatibility in mind).
  - The feature `data_columns` as `server/artifacts/columns.json` with a shape like `{ "data_columns": ["total_sqft","bath","bhk","location1", ...] }`.

## License and author

- Project owner: `JayanthK007`
- Add a license file if you plan to publish this repo (e.g., `MIT`).

---

If you want, I can:
- Add a `requirements.txt` with suggested versions,
- Create this `README.md` file in the repo (already created),
- Or add basic unit tests and a `Dockerfile`.

Tell me which of those you'd like next.

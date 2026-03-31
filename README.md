## Base paper: 
https://www.esann.org/sites/default/files/proceedings/legacy/es2015-56.pdf

## research question: 
How do different model architectures (statistical vs ML vs deep learning) compare in a production MLOps pipeline for time series anomaly detection, and how does concept drift impact their reliability over time?

## Dataset: 
Yahoo S5 Anomaly Detection Benchmark or NYC Taxi demand dataset

### The three models to compare: 
- Baseline — ARIMA/SARIMA. Statistical model, fast, interpretable. Use statsmodels. This is your null hypothesis — the "good enough" baseline many teams use in production.
- Improved — Facebook Prophet. Handles seasonality and holidays automatically. pip install prophet. Better than ARIMA on most real-world series with trends.
- Best — LSTM Autoencoder. Reconstruct the time series; high reconstruction error = anomaly. Use PyTorch or Keras. This is your "improvement" claim for Track-II — it should outperform the others, and you explain why.

## Our Research claim:
LSTM Autoencoders outperform statistical baselines in production anomaly detection pipelines, but introduce latency and drift sensitivity that requires automated monitoring to maintain.

## Work Timeline
- ### Iteration 1 (Research setup):
  Read **8–10 papers** and write the **related work section** draft. Good papers to start with: Sculley et al. "Hidden Technical Debt in ML Systems" (Google, NIPS 2015), Klaise et al. "Monitoring and Explainability of Models in Production" (ICML 2020 Workshop), and any 3–4 papers on LSTM anomaly detection in time series. Set up the **GitHub repo**, create the branch strategy (main, develop, feature/*), and configure the project skeleton.
- ### Iteration 2 (ML core):
  Load the **dataset**, write the **preprocessing** pipeline, and **train all three models**. Every single run — even failed ones — gets logged to **MLflow** with mlflow.log_param, mlflow.log_metric, and mlflow.log_artifact. Register the best model. Write the **docker-compose.yml** bringing up four services: api, mlflow, prometheus, grafana. Design the **experiment matrix** (which hyperparameters to vary, which metrics to record).
- ### Iteration 3 (Packaging & CI/CD):
  Write the **Dockerfile** for the FastAPI service. Wrap the **best model in a FastAPI app** with /predict, /health, and /metrics endpoints. The CI/CD workflow (.github/workflows/ci.yml) does: lint → test → docker build → push to Docker Hub. This is straightforward but polish it — the grader will run it.
- ### Iteration 4 (Monitoring):
  Instrument the FastAPI /metrics endpoint using prometheus_client to expose: prediction latency histogram, anomaly detection rate gauge, and input feature statistics (mean/std of incoming windows). Connect Grafana to Prometheus, build a dashboard with at least five panels. Use Evidently AI to generate a drift report comparing your training distribution to "production" data — simulate drift by gradually shifting the test set mean by 10–20%.
- ### Iteration 5 (Paper, demo, polish):
  IEEE paper. Generate all result tables and figures. For the 15-minute presentation: 2 min intro/problem, 3 min architecture walkthrough, 5 min live demo (docker-compose up, hit the API, show Grafana dashboard, trigger drift alert), 3 min results, 2 min conclusion.

# Mobile Network Traffic Forecasting

Comparative empirical study of one-step-ahead mobile Internet traffic forecasting using the Telecom Italia Milan telecommunications dataset.

## Research Question

**How do different sequential models compare for one-step-ahead mobile network traffic forecasting, and how does their performance vary across geographical areas with different traffic characteristics?**

## Forecasting Approaches

- Persistence baseline
- Fourier-seasonal ARIMA
- Long Short-Term Memory (LSTM)
- Transformer

All approaches are evaluated at the original **10-minute resolution** using the same geographical areas, chronological partitions, one-step-ahead forecasting horizon, and evaluation metrics.

## Dataset

The project uses the Telecom Italia Milan telecommunications dataset. Raw records contain telecommunications activity across 10,000 geographical grid squares at 10-minute intervals. Internet activity is aggregated across country-code records to obtain one value per square and timestamp.

The raw dataset is **not stored in this repository** because of its size. The notebook accesses the dataset through Kaggle/KaggleHub.

**Dataset:** [Milan Telecom Traffic 2013 — Kaggle](https://www.kaggle.com/datasets/arsenekabasinga/milan-telecom-traffic-2013/data)

## Data Processing

The workflow:

1. inspects the raw daily files and schema;
2. measures baseline memory usage;
3. loads only the columns required for Internet-traffic forecasting using reduced numeric dtypes;
4. aggregates country-level observations by geographical square and timestamp;
5. investigates missingness and temporal coverage;
6. performs exploratory and time-series analysis;
7. selects three complete geographical areas with contrasting traffic profiles.

## Selected Areas

| Traffic profile | Grid square | Mean traffic | CV |
| --- | ---: | ---: | ---: |
| Low/stable | 2404 | 13.04 | 0.334 |
| Medium/moderate | 6433 | 30.94 | 0.393 |
| High/variable | 5243 | 148.07 | 0.767 |

The profile labels describe statistical traffic behaviour only.

## Forecasting Protocol

- **Resolution:** 10 minutes
- **Forecast horizon:** one step ahead (10 minutes)
- **Split:** 70% training / 15% validation / 15% held-out test
- **Metrics:** MAE, RMSE and sMAPE
- **Random seed:** 42

Hyperparameters are selected using training and validation data. Final configurations are refitted on the combined training and validation period and evaluated on the held-out test period.

## Final Configurations

### Fourier-seasonal ARIMA

- ARIMA order: (1, 1, 1)
- Daily Fourier period: 144
- Fourier harmonics: K = 2
- Rolling one-step forecasting

A full seasonal SARIMA with a 144-step period was initially investigated but exceeded the available runtime memory at the original 10-minute resolution. Fourier terms were therefore used to retain daily seasonality without changing the forecasting resolution.

### LSTM

- Lookback: 144 observations
- LSTM units: 32
- Learning rate: 0.001
- Batch size: 32
- Optimizer: Adam
- Loss: MSE

### Transformer

- Lookback: 72 observations
- Model dimension: 16
- Attention heads: 2
- Feed-forward dimension: 32
- Encoder blocks: 1
- Dropout: 0.1
- Learning rate: 0.0005
- Batch size: 32

## Held-Out Test Results

| Profile | Model | MAE | RMSE | sMAPE (%) |
| --- | --- | ---: | ---: | ---: |
| Low/stable | Persistence | 0.9456 | 1.2387 | 9.3011 |
| Low/stable | Fourier-ARIMA | 0.8979 | 1.1630 | 8.9053 |
| Low/stable | LSTM | **0.8732** | **1.1264** | **8.6529** |
| Low/stable | Transformer | 1.2024 | 1.4794 | 12.5616 |
| Medium/moderate | Persistence | 5.3005 | 6.6660 | 21.6372 |
| Medium/moderate | Fourier-ARIMA | 4.1276 | 5.2465 | 16.7736 |
| Medium/moderate | LSTM | **4.0457** | **5.1868** | **16.1416** |
| Medium/moderate | Transformer | 4.9909 | 6.3359 | 19.8070 |
| High/variable | Persistence | 7.3329 | 9.3655 | 18.5031 |
| High/variable | Fourier-ARIMA | 7.6517 | 9.4290 | 20.9351 |
| High/variable | LSTM | **5.7516** | **7.3438** | **14.3716** |
| High/variable | Transformer | 6.9017 | 8.7723 | 17.1690 |

The evaluated LSTM configuration produced the lowest aggregate test MAE, RMSE and sMAPE in all three selected areas. Failure analysis also showed that all approaches experienced larger errors during abrupt traffic changes and tended to underestimate high-traffic peaks.

## Repository Structure

```text
mobile-network-traffic-forecasting/
├── README.md
├── requirements.txt
├── .gitignore
├── formative_assignment.ipynb
└── Mobile_Network_Traffic_Forecasting_Final_Report.pdf
```

## Installation

Clone the repository:

```bash
git clone https://github.com/Tapiwanashe6/mobile-network-traffic-forecasting.git
cd mobile-network-traffic-forecasting
```

Create a virtual environment if desired, then install the dependencies:

```bash
pip install -r requirements.txt
```

## Running the Project

Open `formative_assignment.ipynb` in Jupyter Notebook, JupyterLab, Google Colab, or Kaggle and run the cells in order.

Kaggle authentication/access may be required for dataset download. The raw dataset is intentionally excluded from Git.

## Reproducibility Notes

- Standardization parameters for neural models are fitted using training data during model selection.
- Chronological splits are used instead of random train/test splitting.
- The held-out test targets are not used for hyperparameter selection.
- One-step evaluation allows each forecast to use observations available before its target timestamp.
- Executed notebook outputs document the experiments and reported results.

## Report

The final research report is available at the repository root as `Mobile_Network_Traffic_Forecasting_Final_Report.pdf`.

## Video Presentation

[Video Presentation](https://tapiwanashe6.github.io/mobile-network-traffic-forecasting-video/)

## Author

**Tapiwanashe Gift Marufu**  
Bachelor of Software Engineering (Machine Learning)  
African Leadership University

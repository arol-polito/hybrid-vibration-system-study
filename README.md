# A Hybrid Vibration-Based Anomaly Detection System for Predictive Maintenance of Capping Equipment

## Overview

This PhD research project aims to assist **AROL** in technological innovation by identifying deviations in sensor behavior from expected patterns. The goal is to generate **preventive machine stoppages** and avoid unplanned shutdowns that could disrupt production.  

In the **Industrial Internet of Things (IIoT)** domain, it is crucial to collect raw sensor data, extract meaningful features, and define metrics that support predictive maintenance. Data may come from the machine itself or third parties, providing insights beyond the manufacturer’s original knowledge.

The project focuses on predicting **acceleration vectors** from machine sensors to detect anomalies caused by mechanical wear or loosening.

## Related Publications

* [A Hybrid Vibration-Based Anomaly Detection System for Predictive Maintenance of Capping Equipment](https://iris.polito.it/handle/11583/3002793)  
To be Published

## Experimental Setup

- **Signal properties**:
  - Magnitude of the acceleration vector.
  - No global filtering or normalization applied.
  - Signals segmented based on **machine cycle duration (2.4 s per cycle)** for consistent temporal alignment.

- **Test conditions**:
  - **Bushings**: B1 (nominal), B2 (slight wear), B3 (worn)
  - **Springs**: S1 (nominal), S2 (slight loosening), S3 (loosened)

- **Experimental campaigns**:
  1. **Capping head tests**: increasing bushing wear and varying gear preload.
  2. **Fork-mounted tests**: progressively loosened springs.

- **Anomaly detection process**:
  1. Creation of anomalies from three datasets.
  2. Study of the system signals (sampling frequency, RPM).
  3. Application of **six methods** (PSD, ADF, DTW, Envelope RMS, STFT, Statistical methods) on each temporal section.
  4. Voting on each temporal section: if the majority of methods detects an anomaly, it is flagged as such.
  5. Temporal adjustment: combine sections to avoid isolated occurrences.
  6. Use of **LSTM, Autoencoder, and Isolation Forest** to detect punctual anomalies.

## ✅ Summary of Each Technique
Here’s what each test **measures** and **detects**:

| Method        | Technical Description | What It Detects | Strengths | Limitations |
|---------------|----------------------|----------------|-----------|-------------|
| **PSD**       | Power Spectral Density | **Energy distribution across frequencies** | Power variations, identification of **anomalous bands** | Does not indicate **when** it happened (no temporal info) |
| **ADF**       | Augmented Dickey-Fuller Test | **Signal stationarity** | Detects **drift**, slow changes, or state transitions | Sensitive to noise, cannot localize changes |
| **Classical Statistics** | Mean, variance, max, std over windows | Global or local variations in signal features | Lightweight, interpretable, good for slow trends | Too simple for complex anomalies |
| **Envelope RMS** | Mean amplitude (signal energy envelope) | **Impulsive anomalies, energy spikes**, shocks | Fast, effective for spikes and vibrations | Less sensitive to drift or structural anomalies |
| **Fixed Drift with DTW** | Dynamic Time Warping with fixed drift | **Long-term temporal anomalies**, nonlinear trends | Great for **nonlinear trends** and **temporal alignment** | Sensitive to noise, not always suitable for short signals |
| **STFT**      | Short-Time Fourier Transform (moving window Fourier) | **Spectral changes over time**, unstable frequencies | Detects **local frequency anomalies**, useful for cyclic or transient signals | Limited time/frequency resolution (trade-off) |

## 📊 Use Case Table

| Anomaly Type                         | PSD  | ADF  | Statistics | Envelope | Fixed Drift with DTW | STFT |
|-------------------------------------|------|------|------------|----------|--------------------|------|
| **Impact / Spike**                   | ❌   | ❌   | ✅         | 🔝       | ❌                  | 🔝   |
| **Sudden energy increase**           | ✅   | ❌   | ✅         | 🔝       | ❌                  | ✅   |
| **Cyclic change (e.g., vibration)** | 🔝   | ❌   | ❌         | ✅       | ❌                  | 🔝   |
| **Slow drift**                        | ❌   | 🔝   | ✅         | ❌       | ✅                  | ❌   |
| **Stationarity state change**        | ❌   | 🔝   | ✅         | ❌       | ✅                  | ❌   |
| **Band-specific anomaly**            | 🔝   | ❌   | ❌         | ❌       | ❌                  | 🔝   |
| **Slow morphological variation**     | ❌   | ✅   | ✅         | ❌       | ✅                  | ❌   |
| **Local non-energy anomaly**         | ❌   | ❌   | ❌         | ❌       | ❌                  | ✅   |

 ✅ = useful, 🔝 = very useful, ❌ = little or not useful

## Combinations

- **Envelope + STFT** → for **transient anomalies**  
- **PSD + ADF** → for **trends** and **anomalous patterns**  
- **Statistics + Fixed Drift with DTW** → for monitoring **long-term trends**  
- **Envelope + Fixed Drift with DTW** → to detect **nonlinear trends** and **sudden impacts**

## LSTM and ML Models

- LSTM, Autoencoder, and Isolation Forest models are used to detect punctual anomalies in acceleration signals.
- The models are trained and tested to identify the normal behaviour of capping machines, and then to understand if there are problems in thei behaviour.

Isolation Forest parameters:
- 'contamination': 'auto'
- 'max_features': 0.5
- 'max_samples': 'auto'
- 'n_estimators': 100
- 'random_state': 42

LSTM Autoencoder model:

<img width="695" height="264" alt="Screenshot 2025-10-23 alle 15 38 09" src="https://github.com/user-attachments/assets/c4ee2b50-d5f0-45ce-893d-5071635149d9" />

LSTM model:

<img width="659" height="246" alt="Screenshot 2025-10-23 alle 15 36 04" src="https://github.com/user-attachments/assets/69dc73dd-9281-4cad-899a-6f0726c13c59" />


## Results

### Drift in time – bushing wear
| No. | Anomaly (s) | Detection (s) | Difference (s) |
|-----|-------------|---------------|----------------|
| 1   | 439.9767    | 439.9773      | -0.0006        |

**Performance metrics**:  
| Label   | Precision | Recall | F1-Score | Support |
|---------|-----------|--------|----------|---------|
| Good    | 0.999     | 1.000  | 0.999    | 300     |
| Anomaly | 1.000     | 0.999  | 0.999    | 700     |

**Error rate**: 0.004% over total data.

### Transient anomaly – bushing wear
| No. | Anomaly (s) | Detection (s) | Difference (s) |
|-----|-------------|---------------|----------------|
| 1   | 439.9767    | 439.9773      | -0.0006        |
| 2   | 102.6123    | 102.6130      | -0.0007        |

**Metrics**: Precision/Recall/F1 ≈ 0.999, total error ≈ 0.0009%.

### Mixed anomaly – bushing wear
**Metrics**:  
- Good: F1 0.995  
- Anomaly: F1 0.999  
- Total error: 0.148%  

**Detected anomalies**: 6 anomalies across different temporal sections, very close to true anomalies.

## Requirements

- **Python 3.x**
- **NumPy**
- **Pandas**
- **TensorFlow** (for TFLite inference)
- **Scikit-learn**
- **h5py, ujson** (for model and data handling)

```bash
pip install numpy pandas tensorflow scikit-learn h5py ujson
````

## Usage

1. Ensure LSTM/Autoencoder/Isolation Forest models in TFLite format are in the working directory.
2. Connect to TCP server to receive real-time acceleration data.
3. Run the script:

```bash
python acceleration_prediction.py
```

* Predictions are saved in a CSV file with timestamps, actual values, predicted values, and anomaly labels.

## License

This project is licensed under the MIT License.

## Contributing

Feel free to open an issue or submit a pull request if you'd like to contribute to the project.

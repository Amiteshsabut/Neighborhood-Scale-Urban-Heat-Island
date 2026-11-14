# Neighborhood-Scale Nighttime Urban Heat Island: Trained Random Forest Models

This repository provides **nine fitted Random Forest regression models** used to investigate neighborhood-scale nighttime urban heat island (UHI) patterns in **Philadelphia, Pennsylvania, and Dallas–Fort Worth, Texas, USA**, using seasonal ECOSTRESS observations and urban/environmental predictors. The associated study analyzes observations from **2019–2025**.

The models predict a **dimensionless, city- and season-specific normalized nighttime UHI index**, not near-surface air temperature, degrees Celsius, or conventional urban-minus-rural UHI intensity.


## 1. Contents:

| Model file | Training city | Season | 
|---|---|---|
| `RF_Philadelphia_UHI_Summer_ME_Model.joblib` | Philadelphia | Summer (JJA) | 
| `RF_Philadelphia_UHI_Autumn_ME_Model.joblib` | Philadelphia | Autumn (SON) | 
| `RF_Philadelphia_UHI_Spring_ME_Model.joblib` | Philadelphia | Spring (MAM) |
| `RF_Philadelphia_UHI_Winter_M_Model.joblib` | Philadelphia | Winter (DJF) | 
| `RF_Dallas_UHI_Summer_ME_Model.joblib` | Dallas | Summer (JJA) |
| `Philadelphia_Summer_Tuned_Wind_BLH.joblib` | Philadelphia | Summer (JJA) |
| `Philadelphia_Autumn_Tuned_Wind_BLH.joblib` | Philadelphia | Autumn (SON) | 
| `Philadelphia_Spring_Tuned_Wind_BLH.joblib` | Philadelphia | Spring (MAM) |
| `Philadelphia_Winter_Tuned_Wind_BLH.joblib` | Philadelphia | Winter (DJF) | 


### Training configuration read from saved estimators

- **Philadelphia 19-predictor models:** 500 trees, `max_features="sqrt"`, bootstrap enabled, unrestricted maximum depth, `random_state=42`.
- **Dallas summer 19-predictor model:** 400 trees, `max_features="sqrt"`, bootstrap enabled, unrestricted maximum depth, `random_state=42`.

## 2. Two different input and preprocessing conventions

### A. Original 19-predictor models

Inputs, in the **exact order** used during fitting:

```python
FEATURES_19 = [
    "A_AIR_MD", "A_PRK_MD", "A_IMP_MD", "A_MOT_MD", "A_PRI_MD",
    "M_BLD_MD", "M_STR_MD", "M_SF_MD", "M_MF_MD", "M_HR_MD",
    "M_CI_MD", "M_CC_MD", "P_CON_MD", "P_GLA_MD", "P_MET_MD",
    "ParkPct", "PCInc_e", "POC_e", "PopDensity"
]
```

The original models were fitted to the **standardized predictor values** stored in files such as `Philadelphia_BG_Summer_UHI_LST_STANDARDIZED.xlsx` and `Dallas_BG_Summer_UHI_LST_STANDARDIZED.xlsx`. The serialized objects are **RandomForestRegressor only**: they **do not contain** the feature scalers or a target inverse-transform pipeline. 

The output of `.predict()` is a **standardized (z-score) UHI target**, which must be back-transformed using the *appropriate city and season* target parameters.

### B. Meteorological 21-predictor models

```python
FEATURES_21 = [
    "A_AIR_MD", "A_PRK_MD", "A_IMP_MD", "A_MOT_MD", "A_PRI_MD",
    "M_BLD_MD", "M_STR_MD", "M_SF_MD", "M_MF_MD", "M_HR_MD",
    "M_CI_MD", "M_CC_MD", "P_CON_MD", "P_GLA_MD", "P_MET_MD",
    "ParkPct", "PCInc_e", "POC_pct", "PopDen_old",
    "AORC_WIND", "HRRR_BLH"
]
```

- `AORC_WIND`: NOAA AORC 10 m wind-speed predictor.
- `HRRR_BLH`: HRRR atmospheric boundary-layer-height predictor.
- All other fields represent urban infrastructure, built materials, land cover, parks, and socioeconomic attributes.

```text
Pipeline
├── x_scaler: fitted StandardScaler (21 input features)
└── model: fitted TransformedTargetRegressor
    ├── regressor_: fitted RandomForestRegressor (350 trees)
    └── transformer_: fitted StandardScaler for the target
```



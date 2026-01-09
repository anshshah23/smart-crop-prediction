# Chapter 4 - Mermaid Diagrams
## Smart Crop Prediction System - Analysis Modeling

Copy these mermaid scripts to render diagrams in Markdown, GitHub, or [Mermaid Live Editor](https://mermaid.live/)

---

## 4.1 DATA MODELING - Entity Relationship Diagram

```mermaid
erDiagram
    CROP_DATA ||--o{ SYNTHETIC_DATA : generates
    WEATHER_DATA ||--o{ SYNTHETIC_DATA : generates
    SYNTHETIC_DATA ||--|| ML_MODEL : trains
    
    CROP_DATA {
        string state
        string district
        string season
        string crop
        int crop_year
        float area_hectares
        float production_tons
        float yield_t_per_ha
    }
    
    WEATHER_DATA {
        date date
        string location
        float temp_max
        float temp_min
        float precipitation
        float soil_temp_0cm
        float soil_temp_6cm
        float soil_moisture
    }
    
    SYNTHETIC_DATA {
        string district_code
        string season_code
        int crop_year
        float area
        float production
        float temp_max
        float temp_min
        float precipitation
        float soil_metrics
        boolean is_synthetic
        int yield_category
    }
    
    ML_MODEL {
        string model_type
        int n_estimators
        int max_depth
        float accuracy_score
        array predictions
    }
```

---

## 4.2 ACTIVITY DIAGRAM - System Workflow

```mermaid
flowchart TD
    Start([START]) --> Load[Load Raw Dataset<br/>238,838 rows]
    Load --> Filter[Filter Data<br/>Crop: Bajra<br/>State: UP]
    Filter --> API[API Call<br/>Weather Data<br/>90 days]
    API --> Merge[Merge Data]
    Merge --> Check{Data Size OK?<br/>&gt;5000 samples}
    Check -->|NO| SMOTE[Apply SMOTE<br/>Generate 5x Data]
    Check -->|YES| Split
    SMOTE --> Split[Train/Val/Test Split<br/>70/15/15]
    Split --> Train[Train RF Model<br/>120 trees]
    Train --> Evaluate{Evaluate Model<br/>Accuracy &gt; 70%?}
    Evaluate -->|YES| Save[Save Model<br/>.pkl]
    Evaluate -->|NO| Tune[Tune Parameters<br/>Retry]
    Tune --> Train
    Save --> Predict[Predict<br/>New Samples]
    Predict --> End([END])
    
    style Start fill:#90EE90
    style End fill:#FFB6C1
    style SMOTE fill:#87CEEB
    style Train fill:#DDA0DD
    style Save fill:#F0E68C
```

---

## 4.2 CLASS DIAGRAM - System Components

```mermaid
classDiagram
    class DataLoader {
        -string file_path
        -DataFrame dataframe
        +load_csv() DataFrame
        +filter_data(crop, state) DataFrame
        +get_statistics() dict
    }
    
    class WeatherAPIClient {
        -string api_url
        -float latitude
        -float longitude
        +fetch_weather(days) DataFrame
        +parse_response(json) DataFrame
        +save_to_csv(path) bool
    }
    
    class DataPreprocessor {
        -LabelEncoder encoder
        -StandardScaler scaler
        +encode_labels(df) DataFrame
        +scale_features(df) DataFrame
        +handle_missing() DataFrame
    }
    
    class SMOTEGenerator {
        -int k_neighbors
        -string sampling_strategy
        +generate_synthetic(X, y) tuple
        +validate_quality() bool
        +export_csv(path) bool
    }
    
    class RandomForestModel {
        -int n_estimators
        -int max_depth
        -RandomForestClassifier model
        +train(X_train, y_train) None
        +predict(X_test) array
        +evaluate() dict
        +get_feature_importance() array
        +save_model(path) bool
    }
    
    class Evaluator {
        -array y_true
        -array y_pred
        +accuracy_score() float
        +classification_report() dict
        +confusion_matrix() array
        +plot_metrics() None
    }
    
    DataLoader ..> WeatherAPIClient : uses
    WeatherAPIClient ..> DataPreprocessor : provides data to
    DataPreprocessor ..> SMOTEGenerator : feeds
    SMOTEGenerator ..> RandomForestModel : augments
    RandomForestModel ..> Evaluator : uses
```

---

## 4.3 FUNCTIONAL MODELING - Use Case Diagram

```mermaid
graph LR
    DataScientist((Data<br/>Scientist))
    API[Open-Meteo<br/>API]
    
    subgraph System["SMART CROP PREDICTION SYSTEM"]
        UC1[Load Crop Dataset<br/>merged_crop_data.csv]
        UC2[Filter by Crop & Location<br/>Bajra, Uttar Pradesh]
        UC3[Fetch Weather Data<br/>90-day historical]
        UC4[Generate Synthetic Data<br/>SMOTE - 5x augmentation]
        UC5[Train ML Model<br/>Random Forest]
        UC6[Evaluate Performance<br/>Accuracy, F1-Score]
        UC7[Predict Crop Yield<br/>Low/Medium/High]
    end
    
    DataScientist --> UC1
    DataScientist --> UC2
    DataScientist --> UC3
    DataScientist --> UC4
    DataScientist --> UC5
    DataScientist --> UC6
    DataScientist --> UC7
    UC3 <--> API
    
    style DataScientist fill:#FFE4B5
    style API fill:#B0E0E6
    style System fill:#F0F8FF
```

---

## 4.3 FUNCTIONAL MODULES - Module Hierarchy

```mermaid
graph TD
    System[Smart Crop Prediction System]
    
    System --> M1[Module 1: Data Management]
    System --> M2[Module 2: Weather Integration]
    System --> M3[Module 3: Synthetic Data Generation]
    System --> M4[Module 4: Machine Learning]
    System --> M5[Module 5: Reporting]
    
    M1 --> M1F1[load_dataset]
    M1 --> M1F2[filter_data]
    M1 --> M1F3[merge_weather]
    
    M2 --> M2F1[fetch_weather_api]
    M2 --> M2F2[parse_weather_json]
    
    M3 --> M3F1[apply_smote]
    M3 --> M3F2[validate_synthetic]
    
    M4 --> M4F1[train_model]
    M4 --> M4F2[predict_yield]
    M4 --> M4F3[evaluate_model]
    
    M5 --> M5F1[generate_report]
    
    style System fill:#FF6B6B
    style M1 fill:#4ECDC4
    style M2 fill:#95E1D3
    style M3 fill:#F38181
    style M4 fill:#AA96DA
    style M5 fill:#FCBAD3
```

---

## 4.4 TIMELINE CHART - Gantt Chart

```mermaid
gantt
    title Smart Crop Prediction - Project Timeline
    dateFormat YYYY-MM-DD
    
    section Planning
    Problem Identification           :p1, 2025-09-01, 7d
    Literature Review                :p2, after p1, 7d
    Dataset Acquisition              :p3, after p2, 7d
    Technology Stack                 :p4, after p3, 7d
    
    section Data Collection
    Exploratory Data Analysis        :d1, 2025-10-01, 7d
    Weather API Integration          :d2, after d1, 7d
    Data Filtering                   :d3, after d2, 7d
    Feature Engineering              :d4, after d3, 7d
    
    section SMOTE Implementation
    Algorithm Implementation         :s1, 2025-11-01, 7d
    Synthetic Data Generation        :s2, after s1, 7d
    Quality Validation               :s3, after s2, 7d
    Train/Val/Test Split            :s4, after s3, 7d
    
    section Model Development
    Random Forest Training           :m1, 2025-12-01, 7d
    Model Evaluation                 :m2, after m1, 7d
    Feature Importance Analysis      :m3, after m2, 7d
    Model Optimization               :m4, after m3, 7d
    
    section Documentation
    Technical Report                 :doc1, 2025-12-29, 7d
    Final Review                     :doc2, after doc1, 7d
    
    section Milestones
    Project Proposal                 :milestone, m_p, 2025-09-28, 0d
    Clean Dataset Ready              :milestone, m_d, 2025-10-28, 0d
    Augmented Dataset                :milestone, m_s, 2025-11-28, 0d
    Trained ML Model                 :milestone, m_m, 2025-12-28, 0d
    Project Submission               :milestone, m_f, 2026-01-12, 0d
```

---

## 4.4 DELIVERABLES TIMELINE - Simplified View

```mermaid
timeline
    title Key Deliverables Timeline
    section Week 1-4
        Planning & Research : Project Proposal Complete
    section Week 5-8
        Data Collection : Clean Dataset Ready
                       : Weather API Integrated
    section Week 9-12
        SMOTE Implementation : Augmented Dataset (5,860 samples)
                            : 8 CSV files generated
    section Week 13-16
        Model Training : Random Forest Model
                      : 70-85% Accuracy Achieved
    section Week 16-17
        Final Submission : 35-page Technical Report
                        : All Notebooks Complete
```

---

## 4.4 CRITICAL PATH DIAGRAM

```mermaid
graph LR
    P[Planning<br/>4 weeks] --> DC[Data Collection<br/>4 weeks]
    DC --> SMOTE[SMOTE Generation<br/>4 weeks]
    SMOTE --> MT[Model Training<br/>4 weeks]
    MT --> Eval[Evaluation<br/>1 week]
    
    style P fill:#FFE4B5
    style DC fill:#B0E0E6
    style SMOTE fill:#98D8C8
    style MT fill:#F7B7A3
    style Eval fill:#FFDFD3
```

---

## RESOURCE ALLOCATION PIE CHART

```mermaid
pie title Resource Allocation by Phase
    "Planning & Research" : 20
    "Data Collection" : 20
    "SMOTE Implementation" : 25
    "Model Development" : 25
    "Documentation" : 10
```

---

## DATA FLOW SEQUENCE DIAGRAM

```mermaid
sequenceDiagram
    participant User as Data Scientist
    participant DL as DataLoader
    participant API as Weather API
    participant SMOTE as SMOTE Generator
    participant RF as Random Forest
    participant Eval as Evaluator
    
    User->>DL: Load CSV (238,838 rows)
    DL->>DL: Filter Bajra + UP (1,172)
    DL->>API: Request weather (90 days)
    API-->>DL: Return weather data (273 records)
    DL->>DL: Merge crop + weather
    DL->>SMOTE: Send 1,172 samples
    SMOTE->>SMOTE: Generate synthetic (5x)
    SMOTE-->>User: 5,860 synthetic samples
    User->>RF: Train model (70/15/15 split)
    RF->>RF: Fit 120 trees
    RF-->>User: Trained model
    User->>Eval: Evaluate performance
    Eval-->>User: Accuracy, F1-Score, Confusion Matrix
```

---

## DECISION TREE - Technology Selection

```mermaid
graph TD
    Start{Choose ML Algorithm}
    Start -->|Need interpretability| RF[Random Forest ✓]
    Start -->|Need deep patterns| NN[Neural Network]
    Start -->|Small dataset| SVM[SVM]
    
    Data{Handle Imbalance?}
    Data -->|Yes| SMOTE_Choice[Use SMOTE ✓]
    Data -->|Complex generation| GAN[Use GAN]
    
    API_Choice{Weather Data Source?}
    API_Choice -->|Free tier| OpenMeteo[Open-Meteo ✓]
    API_Choice -->|Enterprise| OWM[OpenWeatherMap]
    
    RF --> Success[70-85% Accuracy]
    NN --> Overfitting[Needs &gt;10k samples ✗]
    SVM --> Slow[Slow training ✗]
    
    SMOTE_Choice --> Fast[&lt;2 sec generation ✓]
    GAN --> Training[30+ min training ✗]
    
    OpenMeteo --> Free[Free 10k/day ✓]
    OWM --> Cost[$50/month ✗]
    
    style RF fill:#90EE90
    style SMOTE_Choice fill:#90EE90
    style OpenMeteo fill:#90EE90
    style Success fill:#90EE90
    style Fast fill:#90EE90
    style Free fill:#90EE90
    style Overfitting fill:#FFB6C1
    style Slow fill:#FFB6C1
    style Training fill:#FFB6C1
    style Cost fill:#FFB6C1
```

---

## SYSTEM STATE DIAGRAM

```mermaid
stateDiagram-v2
    [*] --> RawData : Load Dataset
    RawData --> Filtered : Apply Filters
    Filtered --> Merged : API Integration
    Merged --> Augmented : SMOTE Generation
    Augmented --> Training : 70/15/15 Split
    Training --> Trained : Model Fit
    Trained --> Evaluated : Test Set
    
    Evaluated --> Deployed : Accuracy > 70%
    Evaluated --> Training : Accuracy < 70%<br/>(Tune Hyperparameters)
    
    Deployed --> Prediction : New Input
    Prediction --> [*]
    
    note right of Augmented
        5,860 synthetic samples
        Zero NaN values
        Distributions preserved
    end note
    
    note right of Trained
        Random Forest
        n_estimators: 120
        max_depth: 10
    end note
```

---

## IMPLEMENTATION PHASES - SWIM LANE DIAGRAM

```mermaid
graph TB
    subgraph "Phase 1: Data Preparation"
        A1[Load Data] --> A2[EDA]
        A2 --> A3[Filter & Clean]
        A3 --> A4[Weather API]
    end
    
    subgraph "Phase 2: Augmentation"
        B1[Configure SMOTE] --> B2[Generate 5x Data]
        B2 --> B3[Validate Quality]
        B3 --> B4[Export CSVs]
    end
    
    subgraph "Phase 3: Model Training"
        C1[Split Data] --> C2[Train RF]
        C2 --> C3[Predict Test]
        C3 --> C4[Evaluate Metrics]
    end
    
    subgraph "Phase 4: Deployment"
        D1[Save Model] --> D2[Documentation]
        D2 --> D3[Testing]
        D3 --> D4[Production Ready]
    end
    
    A4 --> B1
    B4 --> C1
    C4 --> D1
    
    style "Phase 1: Data Preparation" fill:#E3F2FD
    style "Phase 2: Augmentation" fill:#FFF3E0
    style "Phase 3: Model Training" fill:#F3E5F5
    style "Phase 4: Deployment" fill:#E8F5E9
```

---

## Usage Instructions

### Rendering Options:

1. **GitHub/GitLab**: Paste directly in `.md` files
2. **Mermaid Live**: [https://mermaid.live/](https://mermaid.live/)
3. **VS Code**: Install "Markdown Preview Mermaid Support" extension
4. **Jupyter**: Use `%%mermaid` magic command with mermaid-py
5. **Word**: Export as SVG/PNG from Mermaid Live, insert as image

### Tips:
- Use `mermaid-cli` to generate PNG/SVG: `mmdc -i diagram.mmd -o output.png`
- For presentations: Export high-resolution PNG (300 DPI)
- For documentation: Keep mermaid source in `.mmd` files
- Color customization: Modify `style` lines in each diagram

---

**Total Diagrams Created**: 13 interactive diagrams
**Formats Supported**: Flowchart, Class, ER, Gantt, Sequence, State, Timeline, Pie

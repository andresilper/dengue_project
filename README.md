# Dengue Severity Clustering

Unsupervised clustering analysis to identify patient groups with similar dengue disease severity using hierarchical clustering on binary clinical symptom data.

---

## Problem Statement

Dengue fever presents a wide spectrum of clinical severity, ranging from mild to severe hemorrhagic forms. Without labeled data, this project applies unsupervised learning to automatically group patients by symptom profile, validating the results against the WHO clinical classification (mild, warning signs, severe).

---

## Dataset

- **File:** `dengue_A.csv`
- **Patients:** 622
- **Variables:** 28 binary symptoms (0 = absent, 1 = present)
- **Variable type:** Binary indicators (dummy variables)

### Variables

| Variable | Description |
|----------|-------------|
| `nau/vom` | Nausea/vomiting |
| `rash` | Skin rash |
| `abpainhist` | Abdominal pain (history) |
| `myal` | Myalgia |
| `athral` | Arthralgia |
| `retpain` | Retro-orbital pain |
| `tourniq+` | Positive tourniquet test |
| `pet` | Petechiae |
| `leukop` | Leukopenia |
| `abpainws` | Abdominal pain (warning sign) |
| `pvomws` | Persistent vomiting (warning sign) |
| `cfaws` | Clinical fluid accumulation (warning sign) |
| `bleed` | Bleeding |
| `letha` | Lethargy |
| `liverenws` | Liver enlargement >2 cm |
| `htopltws` | Increased hematocrit + decreased platelet count |
| `dehydspl` | Dehydration |
| `edema.sp` | Edema |
| `hypo` | Hypotension |
| `nppre` | Narrow pulse pressure <20 mmHg |
| `coldskin` | Cold clammy skin/cyanosis |
| `rwpspl` | Rapid and weak pulse |
| `scf` | Slow capillary filling |
| `respdist` | Respiratory distress |
| `severeb` | Severe bleeding |
| `neuro` | Impaired consciousness |
| `altast` | AST/ALT ≥ 1000 |
| `inpatient` | Inpatient or outpatient |

---

## Exploratory Data Analysis

Before clustering, a brief exploratory analysis was performed:

- **Missing values:** None found in the dataset
- **Dataset shape:** 622 rows × 29 columns (28 symptoms + 1 patient ID)
- **Patient ID column (`idpac`)** was dropped as it carries no clinical information
- **Variance check:** All variables have variance between 0 and 1, as expected for binary data where variance = `p * (1-p)` ≤ 0.25

**5 variables with lowest variance (least informative):**

| Variable | Variance |
|----------|----------|
| altast | 0.0127 |
| nppre | 0.0205 |
| tourniq+ | 0.0266 |
| coldskin | 0.0342 |
| neuro | 0.0401 |

These variables are rare symptoms — present in less than 5% of patients — which limits their contribution to the clustering.

---

## Methodology

### 1. Variance Analysis
Since all variables are binary, variance is calculated as `p * (1-p)`, where `p` is the proportion of 1s. Variables with very low variance carry little information for clustering.

**Variables with lowest variance (least informative):**
| Variable | Variance |
|----------|----------|
| altast | 0.0127 |
| nppre | 0.0205 |
| tourniq+ | 0.0266 |
| coldskin | 0.0342 |
| neuro | 0.0401 |

### 2. Hierarchical Clustering
- **Distance metric:** Hamming distance (appropriate for binary data)
- **Linkage method:** Average
- **Number of clusters:** k=3 (defined a priori based on WHO clinical classification)

### 3. Validation
- **Silhouette Score:** 0.364
- Silhouette scores range from -1 to 1:
  - **> 0.5** — well-defined clusters
  - **0.2 to 0.5** — reasonable structure
  - **< 0.2** — weak or overlapping clusters
- A score of 0.364 indicates a reasonable cluster structure, which is expected and acceptable for binary clinical data. The continuous nature of dengue severity and the strong class imbalance (597 vs 19 vs 6 patients) naturally limit the maximum achievable score.

---

## Results

### Cluster Distribution

| Cluster | N Patients | Clinical Interpretation |
|---------|-----------|------------------------|
| 1 | 597 (95.8%) | Mild Dengue |
| 3 | 19 (3.1%) | Dengue with Warning Signs |
| 2 | 6 (1.0%) | Severe Dengue (hemorrhagic/shock) |

### Symptom Profile by Cluster

| Symptom | Cluster 1 (Mild) | Cluster 2 (Severe) | Cluster 3 (Warning Signs) |
|---------|:---:|:---:|:---:|
| nau/vom | 0.47 | 0.83 | 0.84 |
| rash | 0.45 | 0.67 | 0.32 |
| abpainhist | 0.38 | 0.50 | 0.84 |
| myal | 0.73 | 0.33 | 0.47 |
| athral | 0.46 | 0.00 | 0.11 |
| retpain | 0.47 | 0.50 | 0.37 |
| tourniq+ | 0.03 | 0.33 | 0.00 |
| pet | 0.30 | 0.50 | 0.79 |
| leukop | 0.48 | 1.00 | 0.42 |
| abpainws | 0.38 | 0.17 | 0.74 |
| pvomws | 0.43 | 0.83 | 0.74 |
| cfaws | 0.15 | 0.50 | 0.89 |
| bleed | 0.26 | 1.00 | 0.58 |
| letha | 0.04 | 1.00 | 0.63 |
| liverenws | 0.12 | 0.50 | 0.58 |
| htopltws | 0.17 | 0.50 | 0.58 |
| dehydspl | 0.24 | 0.17 | 0.37 |
| edema.sp | 0.07 | 0.33 | 0.47 |
| hypo | 0.05 | 0.83 | 0.53 |
| nppre | 0.01 | 0.00 | 0.26 |
| coldskin | 0.02 | 0.17 | 0.63 |
| rwpspl | 0.02 | 0.33 | 0.89 |
| scf | 0.04 | 0.00 | 0.95 |
| respdist | 0.05 | 0.67 | 0.58 |
| severeb | 0.12 | 0.50 | 0.58 |
| neuro | 0.04 | 0.00 | 0.26 |
| altast | 0.01 | 0.17 | 0.16 |
| inpatient | 0.45 | 1.00 | 1.00 |

### Clinical Interpretation

**Cluster 1 — Mild Dengue (597 patients)**
- Predominantly general symptoms: myalgia (73%), nausea (47%), arthralgia (46%)
- Very few warning signs or severe manifestations
- 45% hospitalization rate

**Cluster 2 — Severe Dengue (6 patients)**
- 100% hospitalization, bleeding, lethargy and leukopenia
- High rates of hypotension (83%) and persistent vomiting (83%)
- Consistent with dengue hemorrhagic fever / dengue shock syndrome

**Cluster 3 — Dengue with Warning Signs (19 patients)**
- 100% hospitalization
- Slow capillary filling (95%), rapid/weak pulse (89%), abdominal pain (84%)
- Consistent with WHO warning signs classification

The cluster distribution and symptom profiles are fully consistent with the **WHO 2009 Dengue Classification**.

---

## Project Structure

```
├── dengue_project.ipynb   # Main notebook
├── dengue_A.xlsx          # Dataset
├── figures/               # Generated plots
│   ├── dendrogram.png
│   └── heatmap.png
├── requirements.txt
└── README.md
```

---

## References

- World Health Organization. (2009). *Dengue: Guidelines for Diagnosis, Treatment, Prevention and Control*. WHO Press.
- Müllner, D. (2011). Modern hierarchical, agglomerative clustering algorithms. *arXiv:1109.2378*.

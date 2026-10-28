# Blog 06 — Intergenerational Income Elasticity Using PSID

This repository contains the reproducible R and Python/SQL workflow for estimating intergenerational income elasticity (IGE) from the Panel Study of Income Dynamics (PSID). It links parents and adult children, estimates single-year and multi-year income elasticities, and compares the estimates on a common sample.

**The PSID microdata are not included.** Researchers must obtain the required files directly from PSID and comply with its [Conditions of Use](https://simba.isr.umich.edu/U/CondUse.aspx).

## Repository structure

The tree below shows the **local project structure after downloading the source data and running all four scripts**. Files marked *not uploaded* are intentionally absent from GitHub; the filenames are included to help researchers verify their local setup.

```text
RodgersYe_6400_Blog06/
├── figures/
│   ├── ige_single_vs_multiyear.png
│   ├── ige_single_only.png
│   └── ige_multiyear_only.png
├── data_raw/
│   ├── .gitkeep                       # Preserves the folder on GitHub
│   ├── PID23.txt                      # Downloaded locally; not uploaded
│   ├── PID23.do                       # Downloaded locally; not uploaded
│   ├── IND2023ER.txt                  # Downloaded locally; not uploaded
│   ├── IND2023ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2023ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2023ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2021ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2021ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2019ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2019ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2017ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2017ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2015ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2015ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2013ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2013ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2011ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2011ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2009ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2009ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2007ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2007ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2005ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2005ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2003ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2003ER.do                   # Downloaded locally; not uploaded
│   ├── FAM2001ER.txt                  # Downloaded locally; not uploaded
│   ├── FAM2001ER.do                   # Downloaded locally; not uploaded
│   ├── FAM1999ER.txt                  # Downloaded locally; not uploaded
│   └── FAM1999ER.do                   # Downloaded locally; not uploaded
├── data_processed/
│   ├── ige_singleyear_results.csv
│   ├── ige_single_year.csv           # Generated locally; not uploaded
│   ├── ige_multiyear_results.csv
│   ├── ige_multiyear_difference.csv
│   └── ige_multi_year.csv            # Generated locally; not uploaded
├── code/
│   ├── 04_multiyear_analyze_ige.R
│   ├── 03_multiyear_prepare_data.R
│   ├── 02_singleyear_analyze_ige.R
│   ├── 01_singleyear_prepare_data.R
│   └── .Rhistory                     # Generated locally; not uploaded (optional)
├── .Rproj.user/                      # Generated locally; not uploaded (optional)
├── .git/                             # Local Git metadata; not uploaded
├── RodgersYe_6400_Blog06.Rproj
├── README.md
├── .Rhistory                         # Generated locally; not uploaded (optional)
└── .gitignore
```

**File availability:** Only `.gitkeep` is committed inside `data_raw/`. The 30 PSID `.txt`/`.do` files must be downloaded into that folder locally. The two row-level CSVs are produced by scripts `01` and `03`, rather than distributed through GitHub. `.Rhistory` and `.Rproj.user/` are optional local RStudio files, and `.git/` is managed by Git when the repository is cloned or initialized.

## 1. Obtain the source data

Register with PSID and download the necessary files from its [official data portal](https://simba.isr.umich.edu/data/data.aspx). Place each fixed-width `.txt` file and its corresponding Stata `.do` **read-in layout** in `data_raw/`, using these exact filenames:

- `PID23.txt` and `PID23.do` — 2023 Parent Identification File.
- `IND2023ER.txt` and `IND2023ER.do` — 1968–2023 Cross-Year Individual File.
- `FAM{year}ER.txt` and `FAM{year}ER.do` for **every** year in: `1999, 2001, 2003, 2005, 2007, 2009, 2011, 2013, 2015, 2017, 2019, 2021, 2023`.

Use the data-layout `.do` files accompanying the `.txt` files, not separate value-label or `_formats.do` scripts. The preparation scripts require the 2023 Parent Identification and Cross-Year Individual files to have compatible release numbers. Do not rename the required source files.

The parental income observations refer to **1998–2010** (1999–2011 survey waves), and the child income observations refer to **2012–2022** (2013–2023 survey waves).

## 2. Set up the environment

1. Download or clone this repository and open `RodgersYe_6400_Blog06.Rproj` in RStudio. The scripts use `here::here()` to resolve paths from the project root.
2. Install the required R packages in the RStudio console:

   ```r
   install.packages(c("here", "reticulate", "sandwich"))
   ```

3. Ensure that the Python environment used by `reticulate` has `pandas` installed. If needed, install it into that environment:

   ```r
   reticulate::py_install("pandas", pip = TRUE)
   ```

Python's `sqlite3` and `pathlib` modules and R's `stats` and `graphics` packages are part of their respective standard installations; no separate SQLite server is needed.

## 3. Reproduce the analysis

With the project open and the required PSID files in `data_raw/`, run the four scripts **in this order** from the RStudio console:

```r
source(here::here("code", "01_singleyear_prepare_data.R"))
source(here::here("code", "02_singleyear_analyze_ige.R"))
source(here::here("code", "03_multiyear_prepare_data.R"))
source(here::here("code", "04_multiyear_analyze_ige.R"))
```

| Script | Purpose | Main outputs |
|---|---|---|
| `01_singleyear_prepare_data.R` | Match parents and children and construct single-year earnings observations. | `data_processed/ige_single_year.csv` |
| `02_singleyear_analyze_ige.R` | Estimate single-year IGE for all eligible children and by age group. | `data_processed/ige_singleyear_results.csv`, `figures/ige_single_only.png` |
| `03_multiyear_prepare_data.R` | Construct multi-year earnings measures for matched parents and children. | `data_processed/ige_multi_year.csv` |
| `04_multiyear_analyze_ige.R` | Compare single-year and multi-year IGE on the same sample, including uncertainty for their difference. | `data_processed/ige_multiyear_results.csv`, `data_processed/ige_multiyear_difference.csv`, `figures/ige_multiyear_only.png`, `figures/ige_single_vs_multiyear.png` |

**Research design in brief:** Parent income combines the identified parents' individual labor earnings; child income is the identified adult child's own labor earnings. The analysis restricts children to ages **35–49 in 2023** and requires positive earnings for the relevant log-income regressions. Multi-year measures average inflation-adjusted annual earnings (2022 dollars); the multi-year construction requires at least **three valid parent waves and three valid child waves**. Regression standard errors are clustered by the 1999 parental family, and the single-year-versus-multi-year coefficient difference is assessed with **499 family-cluster bootstrap draws**. See the scripts for all selection rules and variable definitions.

Running the preparation scripts generates the two omitted row-level CSVs locally. Running the analysis scripts then creates or updates the summary CSVs and figures listed above.

## Data availability and responsible use

- **`data_raw/`** contains no PSID data in the GitHub repository. Its `.gitkeep` file preserves the empty directory structure; users must populate it locally after obtaining access to PSID.
- **`data_processed/ige_single_year.csv` and `data_processed/ige_multi_year.csv`** contain derived, row-level PSID observations and must not be uploaded. Both are reproducible with scripts `01` and `03` after the source data have been obtained.
- The three aggregate summary CSVs and three figures are intended as shareable research outputs. Review them before publishing to ensure compliance with PSID requirements.

PSID's [Conditions of Use](https://simba.isr.umich.edu/U/CondUse.aspx) prohibit redistributing PSID public-use microdata or user-created data extracts to third parties and prohibit using AI/LLM tools in conjunction with PSID data. Each researcher who accesses the data must register and accept the terms individually. Follow PSID's required citation instructions, including the year the data were downloaded, when reporting the research.

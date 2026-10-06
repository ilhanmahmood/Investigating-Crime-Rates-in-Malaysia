# Malaysia Crime Data: Cleaning for Tableau

A reproducible notebook that prepares Malaysia's district-level crime statistics for an interactive Tableau dashboard (filterable by state, crime type and year range).

## Data source

**Crimes by District & Crime Type** (`crime_district`), compiled from the Police Reporting System of the Royal Malaysia Police (PDRM) and published by the Department of Statistics Malaysia on [data.gov.my](https://open.dosm.gov.my/data-catalogue/crime_district).

Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). The raw file in `data/raw/` is redistributed unchanged under that licence.

Coverage of the release used here: 2016–2023, 14 states, 156 police districts, 12 crime types.

## Repository structure

```
├── clean_crime_data.ipynb          # The cleaning notebook (start here)
├── data/
│   ├── raw/crime_district.csv      # Original file, unchanged
│   └── processed/
│       ├── crime_district_clean.csv   # Tableau-ready output
│       └── data_dictionary.csv        # Description of every column
├── requirements.txt
└── README.md
```

## How to run

```bash
git clone <this-repo-url>
cd <repo-folder>
pip install -r requirements.txt
jupyter lab clean_crime_data.ipynb
```

Run all cells from top to bottom. If `data/raw/crime_district.csv` is missing, the notebook downloads the latest version from data.gov.my.

## What the cleaning does

1. **Removes pre-summed rows** (`state = Malaysia`, `district = All`, `type = all`). The raw file mixes totals with detail rows, so summing it in Tableau would count the same crimes several times.
2. **Fixes an inconsistency** as a side effect: in the 2023 release, Serdang's (Selangor) property-crime totals for 2020 and 2021 exceed the sum of their own crime types. The detail rows agree with the official state and national totals, so they are treated as the source of truth.
3. **Merges renamed police districts** so each has one continuous trend line:
   Bandar Bharu → Bandar Baharu (Kedah), Cameron Highland → Cameron Highlands (Pahang), Sg. Buloh → Sungai Buloh (Selangor).
4. **Adds readable labels** for crime categories and types, keeping the original codes alongside.
5. **Adds `state_map_name`** with state names adjusted for Tableau's built-in map.
6. **Validates** that the cleaned file reproduces every official national total in the raw data.

## Reusing this on a newer release

All cleaning rules (renames, labels, map names) are in the **Setup** cell. The notebook stops with a clear error if a new release introduces unlabelled crime types, duplicate rows or totals that no longer reconcile. It also flags any new possible district renames for you to review.

## Caveats

- **What is being counted.** The catalogue describes the figures as crimes where a conviction was secured, but it also refers to them as reported crimes, and the totals match PDRM's index of reported crimes. This project uses the neutral term *recorded crimes*.
- **Putrajaya and Labuan** are not reported as separate states.
- **Police districts are not administrative districts**, so they can't be mapped with Tableau's built-in geography.
- **Raw counts are not rates.** States with more people will record more crimes. Population-adjusted rates are a planned next step.
- **Rape** is defined in Malaysian law as an offence against women, so this series can inform analysis of sexual crime against women. Other sexual offences are not included in this dataset.

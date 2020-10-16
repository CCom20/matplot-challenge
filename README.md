# Pharmaceuticals Analysis
The following analysis looks at the performance of difference drugs when treating tumors in mice. 

## Cleaning the Data
After combining the data, the Mouse IDs were searched for duplicates. Of the 249, one mouse was duplicated - mouse g989.
    mouse_duplicates_single = combined_study_df.loc[combined_study_df.duplicated(subset=["Mouse ID", "Timepoint"]), "Mouse ID"].unique()
Looking at all the duplicate mouse data, it became clear that the same Mouse ID has two measurements at each timepoint where other mice only have one measurement. 
    mouse_duplicate_g989 = combined_study_df.loc[combined_study_df["Mouse ID"] == "g989"]
Since we do not know the actual measurement for the various stats, we cannot consider the data reliable. Therefore, it is necessary to drop the duplicate mouse from the dataset completely.
    combined_study_df_cleaned = combined_study_df_cleaned.set_index("Mouse ID").drop(index="g989")
    combined_study_df_cleaned = combined_study_df_cleaned.reset_index()

## Summary Analysis Table
From the summary statistics table, Capomulin and Ramicane have the lowest stat values across the board
| Drug Regimen | Mean Tumor Vol | Median Tumor Vol | Variance of Tumor | Standard Dev of Tumor | Standard Error of Mean |
| ----------| -------------| ---------| --------- | -------- | -------- |
| Capomulin |	40.675741 |	41.557809 | 24.947764 | 4.994774 | 0.329346 |
| Ramicane | 40.216745 | 40.673236 | 23.486704 | 4.846308 |	0.320955 |
The favoriable stats for these drugs make them our first choices to look at. The graphs that follow look at Capomulin. 

The table above was captured in two ways. The first calculcates each statistic and inserts the value into a dictionary.

    drug_regimen = combined_study_df_cleaned.groupby("Drug Regimen")["Tumor Volume (mm3)"]

    mean_tumor_vol = drug_regimen.mean()
    median_tumor_vol = drug_regimen.median()
    var_tumor_vol = drug_regimen.var()
    std_tumor_vol = drug_regimen.std()
    sem_tumor_vol = drug_regimen.sem()

    drug_regimen_df = pd.DataFrame({"Mean Tumor Vol": mean_tumor_vol, 
                                    "Median Tumor Vol": median_tumor_vol, 
                                    "Variance of Tumor": var_tumor_vol, 
                                    "Standard Dev of Tumor": std_tumor_vol, 
                                    "Standard Error of Mean": sem_tumor_vol})

The second way the same information is camptured is through the `aggregate` function. 
    sing_drug_reg = combined_study_df_cleaned.groupby("Drug Regimen").agg({"Tumor Volume (mm3)":["mean", "median", "var", "std", "sem"]})

## Some Findings Illustrated by Graphs
- When looking at mouse b128, Capomulin proved effective, shrinking the tumor between 6-7 cubic millimeters. 
- Also, there is a strong correlation between weight and tumor size.
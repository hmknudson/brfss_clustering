# Clustering Behavioral Risk Factor Surveillance System Data

## 1. Introduction
The dataset in this capstone project is the Behavioral Risk Factor Surveillance System ([BRFSS](https://www.cdc.gov/brfss/index.html)) survey from the year 2018. This is a telephone survey that is conducted every year by the Center for Disease Control and Prevention (CDC) in all 50 US states, the District of Columbia, and 3 US territories. Typically, over 400,000 adult respondents participate each year, which makes it the largest continuously conducted health survey system in the world. The survey includes demographics-related questions (e.g., age, education level, etc.), health-related questions (e.g., asthma, cancer, dental health, etc.), and behavioral-related questions (e.g., drinking behaviors, HIV risks, etc.).

The 2018 [codebook](https://www.cdc.gov/brfss/annual_data/2018/pdf/codebook18_llcp-v2-508.pdf) was relied on extensively for this project and contains all questions that were asked, how many responses fell in the specified categories for each question, and detailed information regarding the codes the researchers chose for categorical variables, etc. For more information on the study and its protocols, see the BRFSS [documentation](https://www.cdc.gov/brfss/data_documentation/pdf/UserguideJune2013.pdf).

## 2. Research Question
How can BRFSS respondents be segmented based on their demographics, behaviors, and health outcomes?

## 3. Data

### About the data
The 2018 BRFSS dataset comes from an XPT file (that is, SAS transport format) found here, on the 2019 BRFSS webpage. There was a total of 437,436 observations (i.e., survey responses) and 275 columns (i.e., questions) in the dataset. Most of the data was categorical due to the nature of the survey and the way the researchers coded it, with the exception of 3 continuous variables - height, bmi, and weight.

### Cleaning the data
First, columns that had more than 20% of their data missing were removed. A large portion of these were simply gender-specific questions (such as information about PSA tests, gynecological exams, etc.), and were therefore only asked to about 50% of respondents in the first place. For this project, these types of variables weren't a necessity, so they were removed, so as not to deal with extensive amounts of null values. Several columns that were duplicates and re-codes of other columns were also removed, with a preference for columns that contained the largest amount of information - typically, columns with more categories were chosen over re-codes that condensed this information, because it was ideal for the clustering algorithms to see more variability in the data).

Second, for columns with fewer than 20% of their data missing, the missing values (recorded as nulls) were imputed with a code of 1000 to specify they were 'missing,' so this information could be analyzed along with the rest.

The three continuous variables all had under 10% of their data missing, and they were fairly normally disributed, so they were imputed with the means for each gender. That is, for male respondents missing a height, bmi, or weight, the variables' respective means for males were imputed, and likewise for female respondents.

The race variable only had 2% of its data missing, so it was imputed with the dataset's mode (which in this case was white).

The age variable (which was only recorded as categorical for some reason) also had only 2% of its data missing, so it was imputed with the median age category in the dataset.

Third, columns were re-coded so that they began with 0 rather than 1, which was needed for the clustering algorithms. Additionally, many columns were in "backwards" order - e.g., 0 means yes, and 1 means no - so these was re-coded so that 0 was alwas the negative case, to make later interpretation easier.

At the end of cleaning, there were still 437,436 observations, and 68 features remained, which was sufficient.

## 4. Methods

Here is a brief breakdown of the methods that will be used for clustering and attempting to describe the clusters.

### Descriptive statistics & feature selection
1. Run some more descriptive statistics on the dataset for further understanding of variable relationships

2. Get a correlation matrix for the variables and plot it as a heatmap to determine which features are multicollinear, which could affect the algorithms.

3. Run a variance inflation factor (VIF) on the variables to determine the magnitude of multicollinearity, which will better inform whether certain variables actually should be removed or not.

4. If variables have VIF value < 10, remove them

5. Define X, which is the set of variables that will be put into the clustering algorithms.

6. Standardize X so algorithms run better

### Clustering
1. Use DBSCAN as initial clusetering algorithm, because it's good with non-parametric data & non-symmetrical cluster shapes.

2. Use KNN to plot elbow graph to determine best value for DBSCAN's eps parameter

3. Start min_samples at 100 because the dataset is so large

4. Continue with a few different values for min_samples until the optimal window is found

5. Get silhouette scores for all DBSCAN iterations to find which is the best one

6. Plot the clusters from the best DBSCAN solution

7. Use K-Means as the next clustering algorithm, setting k to the number of clusters that DBSCAN determined to be optimal

8. Plot the K-Means solution & get silhouette score

9. Use K-Modes, because also good with categorical/non-parametric data

10. Plot K-Modes & get silhouette score

11. Compare the 3 clustering algorithms & pick the best one

### Interpretation
1. Take the clusters made from the best clustering algorithm, make them an array, and add them back into BRFSS dataframe (the cleaned version)

2. Plot the clusters against a couple of features, with the goal of finding out more about what the clusters mean, what types of data they contain, etc.

## 5. Conclusion

### Recapping the project
In sum, 3 different clustering algorithms were used to try to cluster the BRFSS data from 2018. The various clustering solutions were plotted and assessed based on silhouette score and visualizations. The clustering algorithm with the highest silhouette score and best visualization was K-Means. The clusters were added as an array back into the cleaned BRFSS dataframe, and they were plotted alongside some of the features to try to discern more about the cluster assignments.

### Limitations
Were I to do this again, I might take a random subsample of this dataset, as its largeness compared with the relative smallness of my current computing power meant that it took a VERY long time for some of the algorithms to run. Also, if I had the luxury of more time, it would have been nice to zoom in more on the elbow graph, as its possible the eps value may not have been the ideal one, but the code took too long to run to iterate over it agian and again. Additionally, categorical variables are not necessarily ideal to cluster with, but that was simply a limitation of choosing to work with the BRFSS dataset, which contains mostly categorical variables. This issue was mitigated as much as possible by ensuring all variables were numeric as well as choosing to work with those that did not condense information, but rather retained more variability. Dummies were purposely not used here, because the massive amount of data was already a strain on the computing power available to me; adding more features would have only made it worse to run.

### Conclusion & Implications
Ultimately, when the clusters were plotted alongside various features in the dataset, a few preliminary conclusions about them could be made. Namely, they likely have something to do with the different stages of life humans go through - youth, vs. middle age, vs. old age, etc. Additionally, it's clear there are probably other factors at play here as well, such as economic privilege, lifestyle, etc. More research would need to be done to learn more thoroughly about the clusters.

### Future Research Possibilities
It would be nice to look at more of the BRFSS feature alongside the clusters that were produced here, in order to get a better sense of exactly why the clusters were arranged as they were and to learn more about their meaning. Additionally, it may be a good idea to try some different clustering algorithms, such as HDBSCAN, on this dataset, as it seems the density of the data may have been prohibitive to the DBSCANs that were run here.

Finally, if only there were more time for further analysis, I would love to delve more into what each of these clusters contain, and really flesh out their meaning.


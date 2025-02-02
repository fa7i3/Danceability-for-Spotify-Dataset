# Danceability-of-Spotify-Songs

## Team Members:
* Faith: Github Repository and Dashboard
* Daniel: Machine Learning Model
* Diana: Database Integration

## Communication Protocols: 
* Slack for quick and easy communication among team members
* Zoom meetings on Tuesday, Thursdays and Saturdays
* Additional meetings on other days if necessary

## Project Overview

### Topic: Danceability of Spotify Songs from 1921 to 2020

### Reasons for Selected Topic

- **Mutual Interest** - Music is a popular topic and a common interest amongst the team. Although we like different genres we all listen to music on a daily basis and has become an important factor in our lives
- **Growing Industry** - The music industry is continuously growing and has become more accessible than ever with multiple streaming options. Spotify recorded 188 million Spotify premium subscribers in the second quarter of 2022 and has 60,000 songs being uploaded every day
- **Interested in Learning** - How the danceability of songs have changed over time and if a machine learning can predict the danceability of a song based on different factors

### Description of Dataset Source
* Dataset was downloaded from [Kaggle](https://www.kaggle.com/datasets/ektanegi/spotifydata-19212020) and captured the top 2000 songs on Spotify for each year from 1921 to 2020
* Columns include general song information and [song features](https://developer.spotify.com/documentation/web-api/reference/#/operations/get-audio-features) which contain audio feature information for each unique Spotify Id
* Data was collected from Spotify Web API 
* Dimension of Original Dataset: 169,909 rows by 19 columns

### Softwares Used
* Jupyter Notebook
* ERD (Entity Relationship Diagram) Tool
* pgAdmin
* Tableau Public
* Languages (Python, SQL)
* Dependencies (sqlalchemy, Pandas, TensorFlow, Scikit-learn)

### Questions we hope to answer
* Can machine learning models predict the danceability of Spotify songs using audio feature information?
* How has danceability changed throughout the years from 1921 to 2020?
* How danceable or energetic a song is?
* How various factors can predict the danceability of a song?

## Database

We plan to use a SQL database (PostgreSQL and pgAdmin) to store our data. 

Our dataset consists of two tables: 
- *[spotify_song_info.csv](Resources/spotify_song_info.csv)* which contains general information about each song 
- *[spotify_song_features.csv](Resources/spotify_song_features.csv)* which contains feature columns and the target column *danceability* that we plan to use for our machine learning model. 

Using pgAdmin two tables were created to store both datasets (*[query](Database/query.sql)*) and joined them to create our third table in order to store the *[merged_spotify_songs.csv](Resources/merged_spotify_songs.csv)*. 

Using Python Pandas Library in *[final_preprocessing_data .ipynb](Database/final_preprocessing_data.ipynb)*, the two tables were merged into one dataset, which was examined, cleaned, and sent to a PostgreSQL database via a connection string using SQLAlchemy into the merged_spotify_songs table previously created.

The Entity Relationship Diagram for the two tables is shown below:  
<img src="Images/ERD_spotify_database.png" width=472>

## Machine Learning Model

### Purpose
A machine learning model was created to make predictions on the danceability of spotify songs. The target, danceability, was converted into a classification problem during the database preprocessing phase. Therefore, a classification machine learning model will examine the features as the input (X) and attempt to predict the danceability as the output (y). The machine learning model code can be found here: *[machine_learning_models.ipynb](Machine_Learning_Model/machine_learning_models.ipynb)*. 

### Data Inspection
* determined the shape of the dataset
* verified the datatypes for each column
* checked for null values and duplicate values
* moved all non-numerical values to a second dataframe and kept all numerical values on the main dataframe

### Feature Engineering
* extracted *month* from the *release_date* column and dropping the *release_date* column
* removed outliers from the *duration_ms* column (removed top 5% and bottom 1%)

### Data Preprocessing
* encoded the *key* column with Scikit-learn's OneHotEncoder()
* encoded the *month* column with Scikit-learn's OneHotEncoder()
* scaled the *year*, *duration_ms*, *loudness*, and *tempo* columns with Scikit-learn's StandardScaler()

### Feature Selection
The following columns from the *[merged_spotify_songs.csv](Resources/merged_spotify_songs.csv)* dataset were selected to be features for the machine learning model:

| Feature | Description |
| :------ | :---------- |
| month | the month the track was released |
| year | the year the track was released |
| duration_ms | the length of the track in milliseconds |
| acousticness | confidence measure of whether the track is acoustic |
| danceability (target) | whether the track is suitable for dancing |
| energy | perceptual measure of intensity and activity |
| explicit | whether the track contains explicit language | 
| instrumentalness | predicts whether a track contains no vocals |
| key | key the track is in (i.e 0 = C, 1 = C#/D♭, 2 = D, 3 = D#/E♭, ... , 11 = B) |
| liveness | detects presence of audience |
| loudness | loudness of track in decibels (dB) |
| mode | modality of a track (1 = major,  0 = minor) |
| popularity | calculated by total number of plays and how recent plays are |
| speechiness | detects presence of spoken words |
| tempo | tempo of track in beats per minute (BPM) | 
| valence | describes the musical positiveness conveyed by a track | 

All the above audio features were selected because of their numerical values and strong probability for predicting danceability. Definitions for the above audio features were sourced from the *[Spotify Audio Feature Reference](https://developer.spotify.com/documentation/web-api/reference/#/operations/get-audio-features)*.

### How Data was Split into Training and Testing
In our machine learning model, we chose to split the data into 75% training and 25% testing. 

### Machine Learning Models - Choice, Benefits, and Limitations
The following supervised, classification machine learning models were incorporated in *[machine_learning_models.ipynb](Machine_Learning_Model/machine_learning_models.ipynb)*:

1. Logistic Regression
2. Decision Tree
3. **Balanced Random Forest Classifier**
4. Easy Ensemble AdaBoost Classifier
5. Deep Neural Network

From the multiple machine learning models that we selected, the Balanced Random Forest Classifier had the highest accuracy of 81.99%. Thus, the model we focused on was the Balanced Random Forest Classifier. The benefits and limitations of this model as well as the Easy Ensemble AdaBoost Classifier and the Deep Neural Network are summarized below: 

#### Balanced Random Forest Classifier
| Benefits | Limitations |
| :------- | :---------- |
| Runs very quickly compared to the Easy Ensemble AdaBoost Classifier and the Deep Neural Network | Uses more computational power and resources as the output is combining hundreds of trees |
| Is robust against overfitting and outliers | Requires more time to train as compared to a simple Decision Tree model |

#### Easy Ensemble AdaBoost Classifier
| Benefits | Limitations |
| :------- | :---------- |
| Robust against overfitting | Sensitive to noisy data and outliers |
| | Requires long time to train |

#### Deep Neural Network
| Benefits | Limitations |
| :------- | :---------- |
| Is flexible; can be applied for both regression and classification problems | Black-box nature; unable to know which input variable is significantly influencing the output | 
| Designed to continously learn and improve its results | Sensitive to overfitting and generalization | 

### Changes to the Machine Learning Model
To improve the accuracy of the machine learning model, the following changes were made to the original algorithm:
* kept the *year* and *duration_ms* columns in the feature dataframe
* created new *month* column and encoded it
* removed outliers from the *duration_ms* column
* used StandardScaler() to scale the data instead of manually scaling the columns
* increased the number of trees (*n_estimators*) for the model from 100 to 256
* the accuracy of the Balanced Random Forest Classifier model increased by approximately 1.50%

### Steps: Balanced Random Forest Classifier
The following steps summarize how we created the machine learning model (*[random_forest_classifier.ipynb](Machine_Learning_Model/random_forest_classifier.ipynb)*): 

1. Dropped non-numerical columns
2. Performed Feature Engineering (new *month* column and removed *duration_ms* outliers)
2. Encoded the *key* and *month* column with OneHotEncoder()
3. Split the data into input (X) and output (y) with danceability as the target feature
4. Split the Data into training and testing: 75%/25%
5. Scaled the input (X) data with StandardScaler()
6. Defined a model: model = BalancedRandomForestClassifier()
7. Trained the model: model.fit(X_train_scaled, y_train)
8. Made predictions: y_pred = model.predict(X_test)
9. Validated the model: confusion_matrix(), balanced_accuracy_score(), and classification_report()

### Results: Balanced Random Forest Classifier
The accuracy scores and classification report for our sample Random Forest Classifier model are shown below:
* model had an accuracy of 81.99% for predicting danceability
* achieved a precision of 0.87 and a recall of 0.82 for predicting danceability
* achieved an f1-score of 0.84 for predicting danceability
<img src="Images/random_forest_model_results.png" width="472">

### Final Accuracy Scores of each Machine Learning model
We mainly focused on tracking the accuracy score of each model. A summary of each model's accuracy score is shown below:
| Model | Accuracy Score |
| :---- | -------------- |
| Balanced Random Forest Classifier | 81.99% |
| Deep Neural Network | 79.80% |
| Easy Ensemble AdaBoost Classifier | 79.41% |
| Logistic Regression | 76.34% |
| Decision Tree | 74.07% |

### Conclusion
To conclude, the Balanced Random Forest Classifier addressed our machine learning questions and predicted the danceability of Spotify songs with an accuracy of 81.99%. During the machine learning optimization phase, it was essential to keep the previously dropped numerical columns (*year* and *duration_ms*) and to scale the data with StandardScaler. Changing the number of trees in the forest model from 100 to 256 also improved the accuracy of the model. In the next phase of this project, statistical analysis should be performed to discover patterns and trends in the dataset. Statistical tests, such as the paired t-test, could determine if there was a statistical difference for continous features (ex. energy, liveness, valance, etc.) between two years (ex. 1980 vs. 2020). 

## Dashboard
We plan to use Tableau Public to create the final dashboard for the project. Few reasons why Tableau will be used are:
* It is easy to use;
* It can easily handle large data;
* It is an effective tool used to quickly create data visualizations;
* It offers real-time analysis;
* It can transform data into an engaging story that can be easily understood by the audience.

The final dashboard will show the number of songs, how danceability has changed throughout the years, show some factors that affects it and machine learning. 

## Tableau Public
The dashboard and story contains the following information:
#### Number of songs in the dataset: 
According to the picture below, there are 169,909 songs in the spotify dataset.

![number of songs](https://user-images.githubusercontent.com/104453593/192681719-56d25d32-cfad-459d-a7e2-7444d42bea6a.PNG)

#### Danceability over the years: 
The line chart below shows that danceability has increased throughout the years (from 1921-2020)
* the x-axis represents the Year field;
* the y-axis represents the Danceability field; 
* The darker the green color, the higher the danceability.

![danceability over the years](https://user-images.githubusercontent.com/104453593/192681753-690933fb-c42d-4417-a7dd-377e4d7e3e20.PNG)

#### Factors affecting danceability: 
The line chart below shows that Energy has an effect on Danceability
* x-axis represents the Year field;
* y-axis on the left represents the Danceability field;
* y-axis on the right represents the Energy field;
* The legend shows that the green line represents Danceability and 
* the gold line represents Energy throughout the years. 
* The higher the Energy, the higher the danceability of the song. 

![danceability and energy over the years](https://user-images.githubusercontent.com/104453593/192929258-c83f8cbf-5b7b-4759-b6ba-2a39b4c378d0.PNG)

#### Predicted vs Actual Values for Machine Learning
The line chart shows the actual and predicted values over the years
* x-axis represents the Year field;
* y-axis on the left represents Y Actual;
* y-axis on the right represents Y Pred;
* The legend shows that the blue line represents Y Actual and 
* the orange line represents Y Pred. 
* The line graph shows that the Actual and Predicted values over the years are similar and accurate.

![Machine learning pic music](https://user-images.githubusercontent.com/104453593/194991436-8f80ca81-a0d2-4535-ad44-3fa6ec992db5.PNG)

## Links

### Link to Dashboard
[Tableau Dashboard](https://public.tableau.com/app/profile/faith.emenike/viz/SpotifyDanceabilityDATASETovertheyears/FinalDashboard?publish=yes)

### Link to Tableau Story
[Tableau Story](https://public.tableau.com/app/profile/faith.emenike/viz/SpotifyDanceabilityStoryovertheyears/SpotifyDatasetandfactorsaffectingDanceability?publish=yes)

### Link to Google Slides Presentation
[Presentation](https://docs.google.com/presentation/d/1SLyJo5VTJr1ISsJsDj2ul_97DUWQovaDkD5HBMjirlo/edit?usp=sharing)

## Questions Answered
* Machine learning models can successfully predict the danceability of the songs with an accuracy of 81.99%.
* From the Machine learning and dashboard, we can conclude that danceability has increased over the years and energy has an effect on danceability i.e The higher the Energy, the higher the danceability of the song.
* From the Tableau story, we can see that some factors like Energy, Valence, Mode and Tempo can predict the danceability of a song.

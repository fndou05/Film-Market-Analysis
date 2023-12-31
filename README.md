![img](./images/IMAX.jpg)

# IMDb Film Market Analysis

**Authors**: Franko Ndou and Anthony Brocco

## Overview

IMDb has an SQL database containing extensive movie data, along with two other CSV files that we have obtained. These resources will assist us in conducting a market data analysis to address complex business challenges. Our objective is to identify the top-performing films at the current box office and translate our findings into comprehensible data visualizations and recommendations.

![img](./images/oppy.gif)

## Business Problem

Universal Pictures is aiming to produce the next blockbuster film with a substantial budget. They intend to assemble the finest directors, actors, and implement optimal business strategies to not only create the greatest film of our generation but also to maximize return on investment (ROI). Our task is to conduct an in-depth market analysis using extensive datasets to assist Universal in reaching a conclusion on the most effective approach to achieve this ambitious goal.

![img](./images/inception.gif)

## Data

We are using multiple data sets, mainly the IMDb database as well as two other CSV files that can all be found [here](https://github.com/learn-co-curriculum/dsc-phase-2-project-v3/tree/main/zippedData)

## Methods

This project required us to explore a massive SQL database, dropping and cleaning rows and columns as we went, to be able to come up with visualizations and statistical tests to find out how to create the best possible movie. 

## Creating the Production Team

Assembling a top-notch production team is essential for creating a successful film. Identifying the best director and writer for the job is crucial. While actors play significant roles, directors often craft roles with specific actors in mind. Therefore, determining the most successful actor may not directly contribute to our production team's ability to make the best possible movie. The success of a film largely hinges on the artistic vision of the director and the script quality. Relying solely on statistics related to actors may not enhance our return on investment (ROI) and could potentially have a detrimental impact on the film's quality.

### Highest rated writers and directors

Highest rated directors:

![img](./images/dir_table.png)

Highest rated Writers:

![img](./images/writ_table.png)

It seemed like Christopher Nolan, David Fincher, and J.J Abrams we all critically aclaimed directors. Creating movies with high user ratings as well as popular movies with a massive amount of votes.

### Finding the average ROI of the selected directors

Nolan Table:
![img](./images/nolan_describe.png)
Fincher Table:
![img](./images/fincher_describe.png)
Abrams Table:
![img](./images/abrams_describe.png)

Clearly, all of these directors are very talented, but Christopher Nolan stands out with the highest average rating for his movies. Additionally, he has worked with the largest budgets and consistently achieved high gross earnings on average. While his ROI might not be as high as David Fincher's, his impressive average rating and extensive experience in handling extremely high budget films make him a strong candidate.

### Hypothesis Testing

Statistical analysis is used to assess the likelihood and confidence of these films outperforming those of other directors. This analysis provides valuable insights into the comparative performance of different directors.

```python
# Performing Nolan test
stats.ttest_1samp(c_nolan_films['ROI'], 1.74)

Ttest_1sampResult(statistic=3.5295998983514654, pvalue=0.038650297441573986)


# Performing Fincher test
stats.ttest_1samp(d_fincher_films['ROI'], 1.74)

Ttest_1sampResult(statistic=1.9116222577918325, pvalue=0.19608030549749136)

# Performing Abrams test
stats.ttest_1samp(j_abrams_films['ROI'], 1.74)

Ttest_1sampResult(statistic=0.7924863500783006, pvalue=0.5733739533852609)
```

Based on the sample set we have, there is a 3.8% chance that Christopher Nolan's films will not achieve an ROI of 1.74 (The industry mean for films with a budget of $50 million) or higher. This suggests a high likelihood (96.2%) that Christopher Nolan will indeed generate a higher average return on investment for us.

However, the same level of confidence cannot be expressed for the other directors. It appears more likely that these directors will perform similarly to or worse than the average ROI within the industry.

Given this analysis, it strongly recommends employing Christopher Nolan as the director and potentially as the writer for our film. If he wishes to collaborate any other writer, that would be acceptable, considering his significant success within the industry. This decision aligns with the data-driven evidence we have and is likely to yield a favorable ROI for Universal Pictures.

## Which Genres Have the Largest ROI?

Action movies have the highest ROI among different genres by a significant margin. This aligns perfectly with the goal of working with a director who excels in this genre.

```python
# Create a bar plot for average ROI by genre
plt.figure(figsize=(12, 6))
sns.barplot(data=genre_roi_avg, x='genre', y='ROI', palette='viridis')
plt.xticks(rotation=90)
plt.xlabel('Genre')
plt.ylabel('Average ROI')
plt.title('Average ROI by Genre')
plt.tight_layout()
plt.show()
```
![img](./images/avgroigenre.png)

## Best Time to Release Films

Wednesday is the best day to release our movie, with the highest ROI. Releasing during the warmer months, such as June, July, and May, is recommended as people are more likely to go to the movies during this period. This hypothesis likely supports the larger ROI's during those months.

```python
# Create bars for day of the week vs. domestic gross
sns.barplot(x='day_of_the_week', y='domestic_gross_in_mill', data=budgets)
sns.set(font_scale=0.75)
plt.ylabel('Domestic Gross per million')
plt.title('Domestic Gross on Given Day of the Week')
plt.show()

# Create bars for month of the year vs. domestic gross
sns.barplot(x='month_of_the_year', y='domestic_gross_in_mill', data=budgets)
sns.set(font_scale=0.75)
plt.xticks(rotation=45)
plt.ylabel('Domestic Gross per million')
plt.title('Domestic Gross by Month of the Year')
plt.show()
```

![img](./images/bestdayforfilm.png)
![img](./images/month.png)

## Finding the Right Musician for the Score

Selecting a talented musician with a track record of working on successful films is crucial for creating a captivating soundtrack that enhances the overall impact and success of our movie.

```python
# Merge two DataFrames 'merged_df' and 'people_and_movies_df' using an outer join
relevant_people_and_movies = pd.merge(merged_df, people_and_movies_df, how='outer', left_on='movie', right_on='original_title')

# Drop columns we don't need
relevant_people_and_movies = relevant_people_and_movies.drop(['release_date',
  'runtime_minutes', 'original_title', 'genre'], axis=1)

# Sort the DataFrame by 'ROI' in descending order
relevant_people_and_movies = relevant_people_and_movies.sort_values(by='ROI', ascending=False)

# Drop rows with missing 'primary_profession' values
relevant_people_and_movies = relevant_people_and_movies.dropna(subset=['primary_profession'])

# Filter the DataFrame based on primary profession
chosen_artists = relevant_people_and_movies[relevant_people_and_movies['primary_profession'].str.contains \
                                                  ('soundtrack|composer|music_department|sound_deparment')]
# Select relevant columns
chosen_artists = chosen_artists[['primary_name', 'primary_profession', 'ROI', 'averagerating', 'numvotes']]

# Drop duplicate rows, if any
chosen_artists = chosen_artists.drop_duplicates()

# Sort by 'average rating' in descending order 
chosen_artists = chosen_artists.sort_values(by='averagerating', ascending=False)

# Chose only 'successful' artists by setting the roi to 2 as well as the minimum rating to 7 and display final result
chosen_artists = chosen_artists[chosen_artists['ROI'] > 1.74]
chosen_artists = chosen_artists[chosen_artists['numvotes'] > 10000]
chosen_artists[chosen_artists['averagerating'] >= 7]
```

![img](./images/sound_team.png)

## Backup Directors

We've identified potential backup directors in case our preferred choice is unavailable.

```python
# Merge two DataFrames 'merged_df' and 'people_and_movies_df' using an outer join
relevant_people_and_movies = pd.merge(merged_df, people_and_movies_df, how='outer', left_on='movie', right_on='original_title')

# Drop columns we don't need
relevant_people_and_movies = relevant_people_and_movies.drop(['release_date',
  'runtime_minutes', 'original_title', 'genre'], axis=1)

# Sort the DataFrame by 'averagerating' in descending order (changed from 'ROI')
relevant_people_and_movies = relevant_people_and_movies.sort_values(by='averagerating', ascending=False)

# Drop rows with missing 'primary_profession' values
relevant_people_and_movies = relevant_people_and_movies.dropna(subset=['primary_profession'])

# Filter the DataFrame based on primary profession (director)
chosen_directors = relevant_people_and_movies[relevant_people_and_movies['primary_profession'].str.contains('director')]

# Select relevant columns
chosen_directors = chosen_directors[['primary_name', 'primary_profession', 'averagerating', 'numvotes']]

# Drop duplicate rows, if any
chosen_directors = chosen_directors.drop_duplicates()

# Sort by 'averagerating' in descending order (optional)
chosen_directors = chosen_directors.sort_values(by='averagerating', ascending=False)

# Filtering for above 7 rating
chosen_directors_filtered = chosen_directors[chosen_directors['averagerating'] >= 7.0]

# Display the directors with the highest ratings
chosen_directors_filtered.dropna().reset_index()
```

![img](./images/backup_dir.png)

## Conclusion

An EDA only allows us to look at the statistical data and come up with likely probabilities. There is no way to guarantee the performance of any individual director, actor, musician, or genre. However, with that being said, we feel comfortable creating business recommendations based on the likelihood of these events occurring, as well as basing them on the trends within the industry.

- Categorically, action films generate the largest return on investment by far, compared to any other movie genre. This is likely due to the mass popularity of action films. We should aim to create an action movie as it will have the largest target audience as well as generate the most ROI for our company.

- The best day to release our film is during Wednesday. Most films are actually released on Friday; however, Wednesday has the largest ROI by far. We should aim for the warmer months as well, such as June, July, and May. As those months tend to generate the most ROI as well.
    - This is likely because during the warmer months, people are willing to drive out to see movies and spend time, whereas during colder months, people will tend to stay home to avoid inclement weather.
    - Wednesday likely generates the highest ROI because it's in the middle of the week, which allows most people to view the film within the first week. However, there is no real way to prove this theory; all we know is that Wednesday generates the highest ROI on average.

- We should absolutely work with a talented director who is not only critically acclaimed but also has a reputation for generating a positive ROI. Through data exploration as well as visualizations, we've come to the conclusion that the current best option would be Christopher Nolan. We have also provided a table of potential backup directors who all meet our criteria, assuming Mr. Nolan is not available.
    - Mr. Nolan generates an above-average ROI compared to other high-budget films (1.74 Avg / 3.13 Nolan).
    - He has one of the highest IMDb user rating scores (8.8).
    - The majority of his movies fall within a 7.8-8.8 range of user ratings.
    - The probability of a Christopher Nolan film underperforming is close to 3.8%.

- The score of a movie is incredibly important as it often dictates the mood and ambiance of the film. With that being said, we have provided a list of potential musicians who fall within our selected criteria.
    - Sam Estes and Andrew Kawczynski are our two recommendations for soundtrack producers. They have collectively worked on incredible movies such as Inception, Planet of the Apes, Interstellar, Top Gun Maverick and The Dark Knight.
        
## Next Steps

To provide even more insight for Universal Pictures, these are the steps we could take

- We can build predictive models using machine learning techniques. We could use features like director, genre, release date, and budget to predict box office performance. This would require splitting our data into training and testing sets, selecting appropriate algorithms, and evaluating model performance.

- Import a database that has a detailed filmography of directors and studios, We lost a some data from cleaning due to the fact it was incredibly difficult to filter and maintain everything that was relevant. Some newer movies are not on this list either, and it would be important to analyze the current market rather than the market a few years ago.

- Compare how best-sellers in all film genres compete with each other. Action movies may have the highest ROI on average but we should look at the outliers for each genre and see how popular they became and how much they generated.

- Research how musical data influences the success of films. What musical genres have the highest impact on other film genres? What is the average length of a song in each genre, what variety of musical genres are within an OST? 

- Compare and contrast ROI and user rating relative to genre for directors. See how directors perform in each individual genre and compare it to the averages of other directors and genres.

## For More Information

Please review our full analysis in [our Jupyter Notebook](./code/Analysis_of_Movie_Data.ipynb) or our [presentation](./Film_Market_Analysis.pdf).

For any additional questions, please contact us:

**Franko Ndou & frankondou@gmail.com, Anthony Brocco & anthonybrocco98@gmail.com**

## Repository Structure

Describe the structure of your repository and its contents, for example:

```
├── code
│   ├── __init__.py
│   ├── Cleaning-Movie-Data.ipynb
│   └── Analysis_of_Movie_Data.ipynb
├── images
├── README.md
├── .gitignore
└── Film_Market_Analysis.pdf
```

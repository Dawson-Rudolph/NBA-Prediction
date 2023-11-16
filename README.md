# NBA-Prediction

## Description

  This is an NBA game & MVP prediction project. The main idea is to scrape a desired amount of data from basketballreference.com, clean the scraped data, turn it into a proper data frame and then use said data to train the game and MVP prediction models. After training and properly configuring the game predictor model we can then use it to predict the winners of previous and future NBA matchups. After doing the same for the MVP predictor model we can then predict previous and future MVPs. Lastly, we can analyze our prediction accuracy and make changes to our models and data to improve their ability to properly predict MVPs and winners of games.

## Web-Scrapping

  Web-Scrapping was the first and most crucial part of this project. Without having the proper type and amount of data, I would not be able to train and test the machine learning models correctly. Starting off with the packages for the scrapping, I used BeautifulSoup, Pandas, PlayWright, and Time. Each of these packages were crucial for the success of the data scrapping functions.

  
### *Get Data*

There are three main functions used to scrape basketballreference.com for game data (all found in the Get_Data.ipynb file): get_html(), scrape_season(), & scrape_game(). 

#### Scrape HTML

  Starting off with get_html, this function has four inputs: url, selector, sleep, & retries. The url is the given url for the web page we want to scrape, the selector is the specific part of the HTML of the page we want to grab, sleep and retires were used to ensure that the function would not cause me to be banned due to scrapping the web page too fast. The purpose of this function as I have hinted at before is to get the HTML of the web page that is currently being scraped. The function would loop attempting to grab the inner HTML from the page, if it could not succeed in doing so there is a time out error exception that would be printed out before having the function sleep and re-try to grab the HTML again. After successfully grabbing the HTML, the function would return said HTML.

#### Scrape Season

  The next function is scrape_season, this function has one input: season. This input is the given season that will be scrapped. The function first takes the inputted season and creates the proper url that will be inputted into the get_html function to grab all the necessary HTML on the page. After that, the function extracts all the proper standings pages from the list of links that were abstracted using Beautiful Soup. Next, the function will loop through each of the standings pages using the get_html function again to grab the schedules from each page. It will then use the path that was created at the top of the loop to write all the scrapped data to the standings directory to be used later.
  
#### Scrape Game
  
  The last function in the Get_Data file is scrape_game, this function takes one input: standings_file. This is the file that we just created in the previous function that holds all the game data from a particular season or set of seasons. This function follows a similiar principle to scrape_season. It starts off with opening the inputted file and uses Beautiful Soup to once again extract the proper links. It then creates an array of links of box scores from the previous links we grabbed. The function then loops through each of the box score links and uses the get_html function to grab all of the necessary data from the HTML and writes said data to the scores directory which had its path created at the top of the loop. The loop has catches for cases of HTML not being grabbed and a path already being created.


### *Parse Data*

  There are four main functions used to parse the extracted data from Get_Data (all found in the Get_Data.ipynb file): parse_html(), read_line_score(), read_stats(), read_season_info().

#### Parse HTML

  The first function in Parse Data is parse_html, this function takes one input: box_score. This input is the individual box score from the box_scores file that was created from using scrape_game. This function first reads the individual box score and assigns it to the html variable which is then used as the inoput for the BeautifulSoup function. We then use the new variable to decompose and select the proper portions of html from the box score to be used later.

#### Read Line Score

  The next function is read_line_score, which takes the output of the parse_html function as its input. It uses this input to create a pandas data frame labaled "line score". The data frame is then edited to add the column names "team" and "total", pointing at the first and last columns of the frame repsecitvely, to help properly label the data and be used for extraction. The function then sets the line score to be the two specified columns "team" and "total" and returns that data.

#### Read Stats

The following function is read_stats, that has three inputs: soup, team, & stat. Soup represents the output of the parse_html function, while team and stat correspond to the NBA team and specific stat that the function is reading. This function then creates a new data frame that consists of the data read from the soup input with the attributes being the inputted team and stat. This data fram is then manipulated to turn all the values to be numerically represented as is needed for our machine learning model to properly interpret the data. The data frame is then returned.

#### Read Season Info

  The last function is read_season_info, which has one input that is soup, which as stated before is the output of the parse_html function. This function first grabs the "nav" container of the parsed html and finds all hrefs from said container. The function then assigns the extracted season name from the href to the season variable and returns said season. 

### *Creating The NBA Games CSV*

  Following the creation of the Parse Data functions, I then put them to use to grab all of the box score data of every game from every season that we extracted in the Get Data file. To do this, I looped through each box score in box_scores (all the extracted box scores from the Scores Directory which was where all of our data was stored after extracting it in Get Data), first parsing each box score using the parse_html function. I then used that parsed data to call the read_line_score function to get the line score as well as all the teams. I then looped through each team in teams, getting both the basic and advanced stats for the team using the read_stats function. After obtaining all the stats I then created two pandas data frames that contained the statistical totals and maxes for the given team. I also turned all the column names to be all lowercase and added "_max" to the end of the max stats so that they were properly labeled. I then concatenated both sets of stats to create a new summary data frame. I then removed all duplicates and empty columns from the summary data frame before adding the given team summary to the overall summaries array. After filling summaries I concatenated it to summary, and then concatenated summary and line score to create a new data frame called game. I then performed some data cleaning where I changed certain values to have numeric representation like "home" and "away" being represented as 0 & 1. I also added labeled data about the opponent stats, which essentially created a mirror between data frames where you would see two sets of stats flipped around between a game. This would then be used to create a new data frame called full_game whichI edited to add the season, date, & winner columns to the frame. Lastly I appended the full_game data frame to the games array. This process would repeat for each team and all of their games. After every team had been accounted for, I created a new data fram called games_df which I would then turn to a CSV file called "nba_games.csv" which is included in this repository.

## Machine Learning

Description of the machine learning models

## Predicting NBA Games

Description of predictions of NBA games

## Predicting NBA MVP

Description of predictions of NBA MVPs

## Technologies

Description of the technologies used

## How-To

Description of how to use & implement/create this project

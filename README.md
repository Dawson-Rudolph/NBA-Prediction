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

  Machine learning models are computational algorithm or statistical model that are designed to learn patterns and make predictions or decisions without being explicitly programmed for a specific task. It is the core component of machine learning and artificial intelligence. A model is trained on a dataset, where it learns the underlying patterns and relationships in the data. This allows the model to generalize and make predictions on new, unseen data. The process involves adjusting the model's parameters based on its performance during training, with the goal of improving its ability to accurately predict outcomes. Machine learning models are widely used in various applications, such as image and speech recognition, natural language processing, recommendation systems, and more. They help contribute to the automation of complex tasks and decision-making processes.

### *Ridge Regression*

  Ridge Regression is a linear regression technique used in machine learning to address the issue of multicollinearity, which is when predictor variables in a model are highly correlated. Ridge Regression adds a regularization term to the ordinary least squares objective function, incorporating the sum of squared values of the coefficients multiplied by a regularization parameter. This additional term helps prevent overfitting by penalizing large coefficient values, which imposes a constraint on their magnitude. The regularization term encourages the model to find a balance between fitting the training data and keeping the coefficients small. Ridge Regression is particularly useful when dealing with datasets with a high degree of multicollinearity, enhancing the stability and generalization ability of the model.

  I used a Ridge Regression model in both the MVP predictor and game predictor. These models were imported using a popular python package called SKLearn which contains an abundance of machine learning models and tools. To use the model I had to use a sequential features selector which was decides the specific predictors that will help the model have the highest success rate when making decisions. I then created a back test function that allowed me to have a convenient function that could be used to train the model, predict games, and then output a pandas data frame containing all of the predictions. I created an almost identical back test function for the MVP predictor but since the goal was to properly pick the top five candidates there were some slight adjustments like calculating accuracy alongside producing a pandas data frame.

### *Random Forest*

  Random Forest belongs to the class of decision tree algorithms. It works by creating a multitude of decision trees during training and outputs the mode of the classes or the mean prediction of the individual trees. Each tree in the Random Forest is built using a random subset of the training data and a random subset of the features. This randomness helps to decorrelate the trees and reduce overfitting, making Random Forests large and accurate. The final prediction is then determined by combining the predictions of all the individual trees. Random Forests are known for their versatility, ease of use, and effectiveness in handling complex datasets. This mmakes them a popular choice in various machine learning applications.

  I only used a Random Forest model in the MVP predictor. I did this mainly for accuracy comparison to see if I would get a significant jump in accuracy compared to using a more linear model like Ridge Regression. There was not a huge growth in accuracy with using the Random Forest model, roughly a 2% growth. This model was much more time consuming to use, it would have been nice to test this model on the game predictor, but with having to train and predict on thousands of games it would have been a very tedious process.

### *Both Models*

  Regarding both models, the key for improvement was adjusting the amount of training as well as the types and number of predictors that were used. I generally used ~30 statistical categories for both the game predictor and the MVP predictor. It was neccessary to cut out certain categories that were being used due to their relevancy. For instance, with the MVP predictor, I removed categories like "PTS won", "PTS max", and "Team" (correlating to the opposing team being played) because they are more team statistics rather than just player statistics. I keep the team that the player played on within consideration due that having an effect on MVP considerations in realily. Adjusting the amount of years/seasons to train a model on before predicting was an important factor because you don't want to train a model on a large set of seasons due to their being trades, new players drafted, and other changes within teams. It is key to find a sweet spot where you can maximize your training without actually hurting your model in the process. This was the case for the MVP predictor as well because it is unfair to compare an MVP winner in the 1980s with an MVP winner in the 2010s due to the changes in player production, rules, and game style as a whole.

## Predicting NBA Games

Description of predictions of NBA games

## Predicting NBA MVP

Description of predictions of NBA MVPs

## Technologies

### *Python*

  I used Python for all of the scripting in this project, I chose the language due to it being the language I am most familiar with. Python is also extremely popular within data science and machine learning, as there are an abundance of packages and libraries for supporting development. I will go into further detail on the packages used after the following overview on Python as a whole:

  Python is a versatile and high-level programming language that is known for its readability and simplicity. the language was reated by Guido van Rossum in the late 1980s. It supports multiple programming paradigms, including procedural, object-oriented, and functional programming. Python's syntax emphasizes code readability, making it an ideal choice for beginners and experienced developers alike. The language has a large standard library and lots of contributions from third-party packages and frameworks that facilitate development across various domains, including web development, data science, artificial intelligence, and automation. Python's interpreted nature, dynamic typing, and automatic memory management contribute to its flexibility and rapid development capabilities, making it a go-to language for diverse projects.

#### Pandas

  I used Pandas in almost all of my scripts within this project, from data scraping and manipulation in the Parse_Data and Get_Data scripts to the editing of data frames in Predict_MVP and Predict_Games. This package was crucial for the manipulation, cleaning, visualization, accuisition of data within this project. The following is an overview of the library:

  Pandas is a powerful open-source data manipulation and analysis library for Python. It provides easy-to-use data structures, such as Series and DataFrame, that are designed to efficiently handle and manipulate structured data. Pandas is particularly well-suited for working with tabular data, making it a go-to tool for data cleaning, exploration, and analysis in various domains, including data science and machine learning. Due to its great functionality, Pandas allows users to perform tasks like filtering, sorting, grouping, merging, and reshaping data, as well as handling missing values. It integrates very well with other popular Python libraries like NumPy and Matplotlib, providing a comprehensive environment for data analysis and visualization. Overall, Pandas is an essential tool for anyone working with data in Python, offering a flexible and intuitive framework for data manipulation and analysis

#### Sci-Kit-Learn

  I used SkLearn in both my Predict_MVP and Predict_Games scripts. This library was crucial for the machine learning portion of this project due to the extensive amount of tools and mdoels it gives users access to. The main models I used from this library were: Ridge Regression and Random Forest. Alongside those models, I also used the library to help with feature selection, preprocessing, splitting of time series, and calculation of accuracy scores for the models. The following is an overview of the library:

  Scikit-learn, whcih is generally referred to as sklearn, is a comprehensive and widely-used machine learning library for Python. It was developed as an open-source project, scikit-learn provides a simple and efficient tool for data analysis and modeling. The library includes a large set of tools for various machine learning tasks such as classification, regression, clustering, dimensionality reduction, and more. Scikit-learn is built on NumPy, SciPy, and Matplotlib, leveraging their capabilities for efficient numerical operations, scientific computing, and data visualization. It has a great interface, which makes it accessible for both beginners and experienced practitioners. Scikit-learn supports a wide range of machine learning algorithms and provides tools for model selection, evaluation, and preprocessing. Its flexibility, ease of use, and extensive documentation have contributed to its popularity within the data science and machine learning communities.  

#### Beautiful Soup

  I used Beautiful Soup in both Parse_Data and Get_Data scripts. This library helped me in the web scrapping process where I had to scrpae data for games from the basketballreference.com. It allowed me to grab the information I needed and convert it into a readable format to be later analyzed. As my entire project was based on the collection of proper game data from many seasons, Beautiful Soup played a crucial role in its success. The following is an overview of the library:

  Beautiful Soup is a Python library created for web scraping purposes to pull the data out of HTML and XML files. It provides the ability to iterate, search, and modify the parse tree. Beautiful Soup automatically converts incoming documents to Unicode and outgoing documents to UTF-8. It sits on top of popular Python parsers like lxml and html5lib, which allows users to try out different parsing strategies or trade speed for flexibility. Due to its simple and intuitive API, Beautiful Soup helps to navigate and search the parse tree, filter the tags a user is interested in, and extract data from web pages, making it a valuable tool for web scraping tasks in Python.

### *Jupyter Notebooks*

  I used Jupyter Notebooks for all of the scripts for this project. Jupyter Notebooks offer a flexible development environment, especially for data science and machine learning related projects. I say this because you can run code line by line, with your variables being saved throughout the execution. It also pairs perfectly with Python and allowed full use of the language alongside all the libraries I discussed above. The following is an overview on Jupyter Notebooks:

  Jupyter Notebooks offer interactive, web-based computing environments that allow users to create and share documents containing live code, equations, visualizations, and text. The name "Jupyter" is a combination of the three programming languages it supports: Julia, Python, and R. These notebooks provide a versatile platform for data exploration, analysis, and visualization, making them popular among data scientists, researchers, and educators. Users can write and execute code in a modular, cell-based structure, allowing for step-by-step code execution and easy experimentation. The inclusion of markdown cells allows for the integration of formatted text, making it an ideal tool for creating data-driven narratives. Jupyter Notebooks support a wide range of programming languages through various kernels, making them a powerful tool for interdisciplinary collaboration and communication in fields such as data science, machine learning, and scientific research.

## How To Improve The Project

There are a few different ways that this project could be improved as well as different directions that it could go regarding expansion. 

I would first like to discuss how what is currently in the repository could be improved upon. The data scrapping and manipulation is pretty standard as it is simply grabbing all of the information and data that our models need to train and make their predictions, so I do not think there is much need for improvement there. 

I beleive that the major opportunity for improving the current state of the project is to work on improving the accuracy percentages for predictions. There are a couple of ideas I have to do so. First, I think that it would be useful to research and test additional machine learning models, since I only used Random Forest and Ridge Regression. There are many different models that could possibly help improve the percentages I got or maybe even train and make predictions in a shorter time period than my current models. On top of that I think that more in-depth data and stat analysis would also help improve the percentages. Taking a deeper dive into the different statistical categories for the games and players could provide some useful insight on what stats to continue to include, which stats to remove, which stats to add, and which stats should have a higher priority. Researching how MVP prioritize different aspects of players games, stats, team success, and all other factors would help give me some real world information to base my changes off. I think looking into what NBA experts think are the stats that effect winning the most could help me do the same for my game predictor. Lastly, I could play with the amount of data that I am training my models on, for example I used roughly 8800+ games for my game predictor model and was training the model on the previous two years before predicting the next year. I could change this to try by having even more games in my CSV, as well as expanding the number of years I am training the model on before making predictions. The same goes for my MVP predictor, training the model on more years may improve the prediction results.

Regarding taking this project to the next level, I think that there are a ton of different things that could be done. The first thing would be to implement this into a web-page with a user interface for selecting the number of years to train the model or the year to make predictions for. This would make using my code and the models a lot easier for people who are not familiar with jupyter notebooks as well as allow me to visualize the results in a nice way. Building off mentioning visualizing the results, I think that creating graphs or visualizations for the prediction results and percentages would be a great addition. Once again it would make the outputs much more user friendly and improve readability for said outputs. For more of a challenge I think that this project could be taken in the direction of creating a machine learning model specifically for predicting MVPs and winners of games. As I mentioned before, I used Ridge Regression and Random Forest models in this project, but I think that it would be interesting to attempt to create a machine learning model strictly for the purposes of this project. I think that this would allow a lot of customization and could result in significant improvement of prediction percentages.

Overall, there are many of different directions this project could go as well as areas for improving the current state.

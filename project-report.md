## 1. Introduction
Cricket is a popular sport that originated in England in the 16th century. The sport is played with a bat and a ball between two teams of eleven players each. Depending on the game format, each match has a specified number of innings, two innings for One Day International(ODI) matches. Each team takes a turn in either striking or fielding during each inning as determined by a coin toss. The striking team (a.k.a batting team) scores runs by striking the ball, and the fielding (a.k.a bowling team) tries to prevent the scoring team. In cricket terminology, a batsman refers to a player with expertise in batting (or scoring runs), whereas a bowler has expertise in preventing runs. Therefore, the player selection needs careful selection between batsmen and bowlers to improve the team's strength and match the opposition.

The motivation behind the topic selection is the continuing decline of the ranking of the Sri Lankan cricket team in all formats of cricket. One of the accusations is the poor player selection performed by the team selection panel. Accusations include lack of understanding of the player strengths, not selecting best performing players, and not matching opposition team strategies.

During the project, I address these issues through a strategic dashboard capable of assisting player selection for a cricket team.
The dashboard assists the user in the following aspects.
1. To gain an understanding of the team strengths and weaknesses of major cricket teams.
2. Identify players contributing to the strengths or players that are capable of countering the weaknesses.
3. Compare the strengths or weaknesses against a chosen opposition.
4. Formulate a strategy against the chosen opposition.

For this purpose, the visualizations that I propose plan to encapsulate information at the team and player level. To ensure time validity, the visualizations include time selection.

## 2. Data
### Data Acquisition
The initial idea of the project was to use the processed dataset posted on the Data-world.
After a closer inspection of the data, I realized that the files offered do not provide any form of time aggregated data. (E.g. Annual scores of each player of each team)
For instance, team statistics and player statistics are aggregate statistics during a broad timespan (20th century and 21st century).
Since the playing career of an average cricket player ranges from 10 - 15 years, it appeared hard to find any interesting relationship or a visualization between team strengths and players.
As a result, I had to discard building the dashboard using the Data-world dataset.

The alternative was to acquire the data from scratch from ESPN-Cricinfo.
By starting from scratch, I can control the size of the dataset and the attributes in the dataset.
For the project, I limited the timeframe from 2006 - 2021 (15 years).
As per the plan, the dashboard required data from three different categories as below.

<Category Table>

The ESPN-Cricinfo interface provides easy access to the mentioned information through a web interface.
However, there was no option for downloading the data in the CSV format requiring scraping the web pages.

For scraping, I used a python notebook on Google-Colab and used the `pandas` library for scraping.
Since the web pages containing data were in `<table>` tags, the scraping process was easier than expected.
However, I had to modify the scraping code to accommodate paginations.
Since I am using three different types of statistics, I had to repeat this step three times.

The data about the batsman and bowlers were available on two different pages (sections) in ESPN-Cricinfo.
As a result, I had only to manage pagination to obtain that information.
However, in team aggregated annual batting and bowling scores, the data were available on two separate web pages.
Therefore I had to scrape two sets of web pages with paging and combine them into a single file.


### Data Pre-Processing
#### Team Statistics
The first pre-processing step for the team aggregated information was the merge of batting and bowling statistics.
For this, I performed a join using the year and the team column.
As the next step, I eliminated non-major cricket-playing nations from the dataset since the plan is to compare major teams.

list: 'Australia', 'Pakistan', 'India', 'New Zealand', 'Sri Lanka',
       'South Africa', 'England', 'West Indies', 'Bangladesh', 'Kenya',
       'Zimbabwe'

Attributes used : Average, Runs Per Over (RPO), Highest Score (HS), Least Score (LS)

Then I performed normalization to improve the comparability of the data attributes that I plan to include in the dashboard. Here, I use min-max normalization. In this manner, the normalized values represent attainment to the best figures over the period selected.
However, due to the opposing nature of batting and bowling, higher normalized values in bowling are a representation of a bad performance.
In order to preserve the consistency, I deducted the normalized scores from 1.

#### Batsman Statistics
The scraped data on batsman did not contain a field for the team attribute; instead, the team was present with the player's name (E.g., Player(Country) ). For this purpose, I had to do processing to extract the country name and add it to a column. Then based on the country name, I filtered out the players of the major cricket-playing nations.

Another major issue was different characters in the fields due to a cricket convention. '*' character was along with the scores representing that the player was not out (still in wicket) at the end of the inning.

#### Bowler Statistics
Similar to the batsman statistics, bowler statistics required the country column to be appended and filtered based on the country. Additionally, there were missing values for some bowling figures represented by a character "-", which I replaced with zero since it was the sensible value to use.

### In-Dashboard Processing
In the dashboard, there are multiple data processing happening based on the time selection. The first is the time selection-based player filtering and averaging player statistics (batting and bowling). Even though it is easier to use simple averaging to get aggregate figures, simple averaging will not provide meaningful information. For instance, a player having single a great inning in a single year can become highlighted over a player with high consistency. As a result, I use the weighted average of the statistic with the number of innings the player has played for the player statistics.

### Data Description

#### Team Data

| Feature               | Description|
|--                     |--|
| Team                  | Team name |
| WL                    | Win/Loss rate  |
| Batting Average*       | Average runs of players **by team**  |
| BattingRPO*            | Runs Per Over **by team**  |
| Batting Highest*       | Highest score recorded **by team**  |
| Batting Lowest*        | Lowest score recorded **by team**  |
| Bowling Average*       | Bowling average of players (Runs per wicket) **against team** |
| BowlingRPO*            | Runs Per Over **against team** |
| BowlingHighest*        | Highest score recorded **against team** |
| BowlingLowest*         | Lowest score recorded **against team** |

*Normalzed values used

#### Bowling Data

| Feature       | Description |
|--             |--|
| Player        | Name of Player  |
| Innings       | Number of innings  |
| Aveage        | Average runs  |
| Wickets       | Average number of wickts per inning  |
| Maidens       | Number of maidens per inning  |
| Boundaries    | Number of boundaries per inning  |

#### Batting Data

| Feature       | Description |
|--             |--|
| Player        | Name of Player  |
| Innings       | Number of innings  |
| Aveage        | Average runs  |
| Boundaries    | Number of boundaries per inning  |
| SR            | Strike rate of batsman   |
| Ducks         | Number of no score wickets  |

*Note that the values are computed using weighted averaging across the selected time frame*

## 3. Visualizations

Describe your visualization. Explain any interactive elements you employed. Address how the visualization addresses the questions you raised in the Introduction.

Before developing the visualizations, I first broke down the requirements into three forms as
1. Team performance comparison
2. Team strength
3. Player breakdown

Based on the section breakdown, I started by creating visual elements for each form of requirement.

In addition to the visual elements, the requirements also include selecting countries, selecting time ranges, and changing the opposition teams.
For this purpose, I included three control elements in the dashboard.
1. Selection of team - Drop down containing the list of teams in the dataset
2. Selection of time window - A range slider with upper and lower bounds based on the maximum and minimum year in the dataset
3. Selection of opposition - Drop down selection containing the list of teams except for the selected team

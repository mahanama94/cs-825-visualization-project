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


### Team Performance
I used Win/Loss ratio as the representative value for the team's overall performance during a specific year.
As the benchmark for comparison, I used the average Win/Loss ratio of all the teams. To represent this information in visualization, I used a simple bar chart with a line overlay.
The bars represent the Win/Loss ratio of the selected team, and the overlaying line represents the average.

The chart changes based on the team selection and the time window selection.
Upon selecting a different team, the bars of the chart changes based on the Win/Loss ratio of the selected team.
This enables the user to grasp an idea about the team's overall performance against other major cricket-playing teams.

### Team Strength
For visualizing team strengths, I used a radial chart with both batting and bowling characteristics.
The information in the chart gets updated upon changes to the selection of the team or the time window.

In addition to a visualization on the team selected, I added another instance of the same chart for the opposition team selection. The opposition strength visualization changes based on the opposition team selection and the time window selection. As a result, the user can continuously compare the changes to team strength over different time frames.

### Player Performance
The player performance section provides information on the players and their contribution to the overall strength of the team reflected through a set of selected metrics. For player performance analysis for batsmen, I have used total runs, boundaries, ducks, and strike rate. Metrics for bowlers include maidens, runs, boundaries, and wickets.

## 4. Design Decisions


### Team Performance
For visualizing the overall team performance, I used a bar chart where the height of the bar represents the Win/Loss ratio of the selected team in a specific year.
Then I used color encoding in the bar chart to represent the years in the selection.
In order to enable us to compare the Win/Loss ratio with the average Win/Loss ratio, I added a line chart layer to represent the average Win/Loss ratio.
Since the Win/Loss ratio can have values on either side of the average Win/Loss ratio, the use of a bar chart can become problematic.
For instance, if the average Win/Loss ratio (the layer above) is higher, then the bars of the layer above can cover the bars in the layer below.

### Team Strength

I used a radial chart with eight sectors similar to the wagon-wheel charts used in cricket to represent the team strength.
Similar to the wagon-wheel charts representing the strengths of a batsman in different regions of the cricket field, the team strength radial chart represents the team strength assessed through normalized metrics.
Like the batsman wagon-wheel charts, I used fill-opacity encoding channel to represent the strength; higher fill opacity for the strengths and lower fill opacity for weaknesses.
An important point to note here is the data preprocessing I performed to ensure that higher values represent strengths despite the metric being related to batting or bowling.


|![](wagon-wheel-batsman.png)|
|:--:|
| Wagon-wheel chart representing batsman strengths. Note the color encoding channel used to represent the strongest area on the field. Source: ESPN-Cricinfo  |   

Another design decision to consider was the placement of the radials.
The normalized metrics used to represent the team strength has two forms of relationships among them.
Firstly, the metrics can be divided into batting (offense) and bowling (defense).
Secondly, each metric has a counter metric in the other category; the counter metric of batting RPO is the bowling RPO.
I used these two forms of relationships for the placement of the radials.
Firstly I divided the radial chart vertically into two main sectors and assigned them batting and bowling.
Then I placed the metrics in an opposing manner in the radial chart.

|![](team-strength.jpg)|
|:--:|
|Team strength visualization. Sectors on left side for batting, right side for bowling. Metrics M1 and M2 represents teams strength assessed through Lowest Score (LS). M1: Batting LS, M2: Bowling LS. |

The users' familiarity with wagon-wheel charts will allow the users to easily decode the strengths and weaknesses.

### Player Performance

I used a set of repeating bar charts for each player type with the length of bars in the x-axis representing the performance.
For batsmen performance for the number of innings, batting runs, number of boundaries, number of ducks, and strike rate.
For bowlers, number of innings, number of maidens, bowling runs, number of boundaries, and number of wickets.
The selection of the player performance metrics was purely on speculation of a possible existence of a relationship between player performance metrics and team strength metrics.
An important point to note here is that the player performance metrics are calculated based on the time frame selection.

|![](batsmen-stats.png)|
|:--:|
| Repeating bar charts for top five batsmen in a team. |

### Layout

The next critical decision was the decision on the layout of the visual elements.
I decided to use either side of the team strength radial chart to place player performance charts, with the batsmen charts being on the side corresponding to the batting strengths of the team.
In this manner, I expect the user to identify the key players contributing to different team strengths quickly.
In an ideal scenario, the bar charts can be arranged such that player performance charts follow the order in the team strength radial chart as highlighted in the conceptual design.

|![](dashboard-concept.png)|
|:--:|
| Conceptual design of the dashboard highlighting possible association of metrics with bar charts. M1 can be associated with bar chart B1, M2 with B2.|


For the team Win/Loss summary illustration placement, I chose the space above the team and player performance charts.

|![](dashboard-components.jpg)|
|:--:|
| Finalized dashboard design. (1) Win/Loss ratio visualization. (2) Team strength with comparison. (3-a) Top five bowler performance. (3-b) Top five batsmen performance. |

### Control Elements


I used a separate input component outside the dashboard to perform the election of the team.
Even though I had planned to use a slider on the Win/Loss illustration to select the time frame, I used a component outside the dashboard due to time constraints and unforeseen technical challenges.
For this, I imported a range input with a lower bound.
As the control for the opposition team selection, I used a drop-down selection containing all teams other than the selected team.



## 5. Development Process

For the development, I followed the bottom-up approach, starting from data acquisition for individual elements.
Then for each dataset gathered, I performed off-dashboard processing (preprocessing) to obtain the finalized CSV files.
Then I used the CSV files as the data sources and created the individual visualization components.
Before developing a visual element, I went through the existing visualization techniques in cricket on ESPN-Cricinfo, as a part of making the visual elements more welcoming to a user.
As the final step, I merged the individual components to form the dashboard.

The major downside to this approach was that It takes an entire process to realize the issues with the integration of components.
For instance, the possible incompatibility between merging `d3` visualizations with `vega-lite`, and `vega` with `vega-lite` had a significant impact on the overall project.
As a result of the incompatibilities, I had to develop the same visualization component in `d3`, `vega`, and using `vega-lite`.
This me to discard the initial idea of providing a comparison of the strengths through a single visual element.
Instead of it I had to use two wagon-wheel-like visualizations.
As a result of the integration issue, I was not able to properly test or enhance any visualizations in the dashboard.

The development of the components was the most time-consuming since I had to develop the same visualization several times due to the integration issues.
Following is a rough estimation of the time spent on each activity.

Activity  | Time (Hours)   
--|---:|
Data exploration (Data-world)                       |  1 |  
Data exploration (ESPN-Cricinfo)                    |  2 |  
Data acquisition, pre-processing (Python - Google-Colab)   |  6 |  
Analyzing existing visualization elements (ESPN-Cricinfo)  |  1 |  
Developing visualization components (Includes failed attempts / discarded components)                               |  12 |  
In-dashboard processing                             |  6 |
Integrations (Includes failed attempts)             |  8 |  
Testing & Improvements                              |  2 |  
Documentation                                       |  9 |  
**Total**                                           |  **43** |  


## Links
* Data-world cricket dataset: [https://data.world/cclayford/cricinfo-statsguru-data](https://data.world/cclayford/cricinfo-statsguru-data)
* ESPN-Cricinfo stats: [https://stats.espncricinfo.com/ci/engine/stats/index.html](https://stats.espncricinfo.com/ci/engine/stats/index.html)
* ESPN-Cricinfo: [https://www.espncricinfo.com/](https://www.espncricinfo.com/)

## References

- Range Slider: [https://observablehq.com/@mootari/range-slider](https://observablehq.com/@mootari/range-slider)
- Vega-lite API docs: [https://vega.github.io/vega-lite-api/](https://vega.github.io/vega-lite-api/)
- Vega-lite JS API docs: [https://vega.github.io/vega-lite-api/api/](https://vega.github.io/vega-lite-api/api/)
- Vega Radar Chart: [https://vega.github.io/vega/examples/radar-chart/](https://vega.github.io/vega/examples/radar-chart/)
- Vega-lite Radial Chart: [https://vega.github.io/vega-lite/examples/arc_radial.html](https://vega.github.io/vega-lite/examples/arc_radial.html)

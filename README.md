# NBA 2018-2019 Database

This is a continuation of **COE 848** final project to construct a database system and perform basic SQL queries on the database.

The initial version contained data for 10 teams in the NBA, and the analysis was performed based on that. However, this new current version has been improved to automate the process of collecting NBA data for a particular season according to the previously constructed ER diagram.

![er-diagram!](/nba-2020-database-system/images/er-diagram.png "er-diagram")

The 5 tables to be constructed in our database are the Teams, Teams Stats, Players, Players Stats, and Coaches tables. Feel free to make any changes to the **nba-web-scraping.ipynb** file to modify what data you want to extract. The data obtained for this project is from **https://www.basketball-reference.com/**. 

## Usage

Clone this repository and install the dependencies listed in the **requirements.txt** file. Create a Postgresql database in your local machine and edit the environment variables shown in step 6 of the **nba-web-scraping.ipynb** file. Additionally, you can specify the year in which you want to collect NBA data for at the very start of the script where **year** is specified. 

```bash
pip3 install -r requirements.txt
```
Sample queries on the database:

What are the names of players that averages 25 or more points per game?
```sql
SELECT players.name, players_stats.points
from players
JOIN players_stats ON players.name = players_stats.name
WHERE players_stats.points >= 25.0;
```
![query1!](/nba-2020-database-system/images/1.PNG "query1")

*What are the team names and points-per-game from which have the highest points-per-game (PPG) per team?*
```sql
SELECT p.team, MAX(ps.points) as PointsPG
FROM players_stats as ps
JOIN players as p ON ps.name = p.name
GROUP BY p.team;
```
![query2!](/nba-2020-database-system/images/2.PNG "query2")

*What are the names of the teams and their 3-point field goal averages, in which the teams averaged 15 or more 3-point field goals per game?*
```sql
SELECT teams_stats.name, teams_stats.three_point_field_goals AS ThreeFPG
FROM teams_stats
WHERE teams_stats.three_point_field_goals >= 15.0;
```
![query3!](/nba-2020-database-system/images/3.PNG "query3")

What are the names of the top 10 players that averaged the most points per game during the season?
```sql
SELECT ps.name, ps.points
FROM players_stats AS ps
ORDER BY ps.points DESC
LIMIT 10;
```
![query4!](/nba-2020-database-system/images/4.PNG "query4")

What is the name and average age of the youngest team in the league (in terms of their players)?
```sql
SELECT players.team AS team, AVG(AGE(players.date_of_birth)) AS avg_age
FROM players
GROUP BY players.team
ORDER BY avg_age ASC
LIMIT 1;
```
![query5!](/nba-2020-database-system/images/5.PNG "query5")

What is the name of the head coach and their winning percentage, in which the coach has the best NBA winning percentage?
```sql
SELECT teams.name, ROUND(
	wins::NUMERIC / (wins::NUMERIC + losses::NUMERIC), 4
) AS win_pct
FROM teams
ORDER BY win_pct DESC
LIMIT 1;
```
![query6!](/nba-2020-database-system/images/6.PNG "query6")

## Things to work on in the future
The current version of the application:
- collects data enough to construct our database outlined in the ER diagram which is quite a simple database
- some parts of the script takes a long time to execute when scraping the data and constructing data frames with the data
- can only collect data for a particular season although collecting for a range of seasons is a very minor tweak if any one chooses to follow this approach
- add columns specific for primary/foreign key ids for a more robust database system
- currently have to create your own database and configure the .env variables to work. In the future, I will look to move this to a cloud database or a database run in a docker container with more strict permissions on what operations you can perform with the database.
- perhaps use data from this simplified database to plot the results of our queries to get a visual understanding of what the queries return.

A future version should look to tackle all of these inconveniences.
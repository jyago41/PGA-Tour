# Final Project


Introduction:

For this project, I am scraping all golf statistical data from the PGA
tour dating all the way back to 2007. The reason for choosing the 2007
year as my start date is because that is the year the Fedex point
system, awards golfers based on their perfromance through PGA Tour
events through the season, was first established. My strong interest and
passion for golf drives the motivation for this project. I desire to
increase my knowledge for my own personal and career benefit. Working a
career in the military and hopefully business, lots of deals and
networking is done on the golf course.

My goal from this project is to scrape the golf data from ESPN,
capturing important stats including, season earnings, rounds played,
cuts made, total number of wins, driving distance, average round score,
greens in regulation, putts, etc. and run linear regressions, as well
as, Random Forests models in order to help predict the total earnings of
a player in that given season and the total number of tournament wins
they will have.

Scraping: I am scraping 15 seasons of the PGA Tour statistics from ESPN.
Each season contains a split table layout, the left table contains
player name and rank, while the other table contains all of the stat
columns. These stats include total earnings, FedEx cup points, number of
events played, number of rounds played, number of cuts made, top 10
finishes, total number of wins for that season, scoring average per
round, driving distance (yards), driving accuracy, greens in regulation,
putts per hole, save percentage, and birdies per round. I looped through
each year, request the page, parse the tables seperately, and combine
them row by row into one row per player per season. Some years were not
able to be captured, unfortunately because they were not in HTML.

``` python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import time
import numpy as np


headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36'
}

#Start Date 2007 due to start of Fedex points

years = list(range(2007, 2025))

df_out = []

for i in range(len(years)):
    time.sleep(np.random.uniform(1.5, 3.0, 1)[0])

    url = f'https://www.espn.com/golf/stats/player/_/season/{years[i]}/limit/500'

    response = requests.get(url, headers=headers)

    soup = BeautifulSoup(response.content, 'html.parser')

    tables = soup.find_all('table')

    if len(tables) < 2:
        continue

    left_rows  = tables[0].select('tr')[1:]
    right_rows = tables[1].select('tr')

    stat_cols = [th.get_text(strip=True) for th in right_rows[0].select('th, td')]

    for j in range(len(left_rows)):
        left_cells  = left_rows[j].select('td')
        right_cells = right_rows[j + 1].select('td')

        if len(left_cells) < 2:
            continue

        row = {'season': years[i]}
        row['rank']   = left_cells[0].get_text(strip=True)
        row['player'] = left_cells[1].get_text(strip=True)

        for k in range(len(stat_cols)):
            row[stat_cols[k]] = right_cells[k].get_text(strip=True) if k < len(right_cells) else np.nan

        df_out.append(row)

df_out = pd.concat([pd.DataFrame([row]) for row in df_out])
df_out.reset_index(drop=True, inplace=True)
df_out
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>

<table class="dataframe" data-quarto-postprocess="true" data-border="1">
<thead>
<tr style="text-align: right;">
<th data-quarto-table-cell-role="th"></th>
<th data-quarto-table-cell-role="th">season</th>
<th data-quarto-table-cell-role="th">rank</th>
<th data-quarto-table-cell-role="th">player</th>
<th data-quarto-table-cell-role="th">EARNINGS</th>
<th data-quarto-table-cell-role="th">CUP</th>
<th data-quarto-table-cell-role="th">EVNTS</th>
<th data-quarto-table-cell-role="th">RNDS</th>
<th data-quarto-table-cell-role="th">CUTS</th>
<th data-quarto-table-cell-role="th">TOP10</th>
<th data-quarto-table-cell-role="th">WINS</th>
<th data-quarto-table-cell-role="th">SCORE</th>
<th data-quarto-table-cell-role="th">DDIS</th>
<th data-quarto-table-cell-role="th">DACC</th>
<th data-quarto-table-cell-role="th">GIR</th>
<th data-quarto-table-cell-role="th">PUTTS</th>
<th data-quarto-table-cell-role="th">SAND</th>
<th data-quarto-table-cell-role="th">BIRDS</th>
</tr>
</thead>
<tbody>
<tr>
<td data-quarto-table-cell-role="th">0</td>
<td>2007</td>
<td>1</td>
<td>Tiger Woods</td>
<td>$10,867,052</td>
<td>123033</td>
<td>16</td>
<td>61</td>
<td>16</td>
<td>12</td>
<td>7</td>
<td>69.2</td>
<td>302.4</td>
<td>59.8</td>
<td>69.9</td>
<td>1.733</td>
<td>52.0</td>
<td>4.066</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">1</td>
<td>2007</td>
<td>2</td>
<td>Phil Mickelson</td>
<td>$5,819,988</td>
<td>109358</td>
<td>22</td>
<td>73</td>
<td>16</td>
<td>7</td>
<td>3</td>
<td>69.3</td>
<td>298.1</td>
<td>56.9</td>
<td>63.2</td>
<td>1.751</td>
<td>50.9</td>
<td>3.644</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">2</td>
<td>2007</td>
<td>3</td>
<td>Vijay Singh</td>
<td>$4,728,377</td>
<td>101064</td>
<td>27</td>
<td>101</td>
<td>25</td>
<td>7</td>
<td>2</td>
<td>70.4</td>
<td>293.8</td>
<td>59.7</td>
<td>65.6</td>
<td>1.768</td>
<td>53.3</td>
<td>3.624</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">3</td>
<td>2007</td>
<td>4</td>
<td>Steve Stricker</td>
<td>$4,663,077</td>
<td>110455</td>
<td>23</td>
<td>80</td>
<td>19</td>
<td>9</td>
<td>1</td>
<td>70.2</td>
<td>283.7</td>
<td>63.7</td>
<td>65.8</td>
<td>1.743</td>
<td>47.5</td>
<td>3.613</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">4</td>
<td>2007</td>
<td>5</td>
<td>K.J. Choi</td>
<td>$4,587,859</td>
<td>103765</td>
<td>25</td>
<td>88</td>
<td>20</td>
<td>7</td>
<td>2</td>
<td>68.8</td>
<td>284.1</td>
<td>64.7</td>
<td>64.5</td>
<td>1.778</td>
<td>58.4</td>
<td>3.489</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">5799</td>
<td>2024</td>
<td>241</td>
<td>Kyle Stanley</td>
<td>$8,800</td>
<td>3</td>
<td>2</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.000</td>
<td>0.0</td>
<td>0.000</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">5800</td>
<td>2024</td>
<td>242</td>
<td>Anders Albertson</td>
<td>$8,760</td>
<td>3</td>
<td>6</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.000</td>
<td>0.0</td>
<td>0.000</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">5801</td>
<td>2024</td>
<td>243</td>
<td>Ryan Armour</td>
<td>$8,360</td>
<td>2</td>
<td>3</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.000</td>
<td>0.0</td>
<td>0.000</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">5802</td>
<td>2024</td>
<td>243</td>
<td>Brian Stuard</td>
<td>$8,360</td>
<td>2</td>
<td>2</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.000</td>
<td>0.0</td>
<td>0.000</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">5803</td>
<td>2024</td>
<td>245</td>
<td>Chris Stroud</td>
<td>$8,200</td>
<td>2</td>
<td>1</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.000</td>
<td>0.0</td>
<td>0.000</td>
</tr>
</tbody>
</table>

<p>5804 rows × 17 columns</p>
</div>

Clean Data

``` python
cols_to_convert = ['rank', 'EVNTS', 'RNDS', 'CUTS', 'TOP10', 'WINS', 'SCORE',
                   'DDIS', 'DACC', 'GIR', 'PUTTS', 'SAND', 'BIRDS', 'CUP']

for col in cols_to_convert:
    if col in df_out.columns:
        df_out[col] = pd.to_numeric(df_out[col].astype(str).str.replace(',', ''), errors='coerce')
```

Visualization

Before I begin modeling, I want to understand the shape of the data
visually. I explore several different types of visualizations
highlighting the average earnings, average score, top 10 players, top
players with most wins, and relationships between driving accuracy and
driving distance. These visualizations help me gain a better
understanding for the data.

This visual shows how much the PGA Tour prize money has grown
signifcantly over the past 20 years. This line shows how average player
earnings increased approximately over 2 million within this timeline,
showing how much the game has grown.

``` python
#Visual 1: Average Earnings by Season 
import seaborn as sns
import matplotlib.pyplot as plt

df_out['EARNINGS'] = df_out['EARNINGS'].astype(str).str.replace('[$,]', '', regex=True)
df_out['EARNINGS'] = pd.to_numeric(df_out['EARNINGS'], errors='coerce')

earnings_by_season = df_out.groupby('season')['EARNINGS'].mean().reset_index()

sns.lineplot(data=earnings_by_season, x='season', y='EARNINGS')
plt.title('Average Player Earnings by Season')
plt.xlabel('Season')
plt.ylabel('Average Earnings ($)')
plt.show()
```

![](PGA%20Tour_files/figure-markdown_strict/cell-4-output-1.png)

This plot line shows whether the tour has gotten more or less
competitive. It seems like over the years, players have gotten better in
terms of scoring. The average scoring average across all PGA Tour
players was relatively stable between 2007 and 2016, fluctuating between
roughly 71.2 and 71.6 strokes per round with no clear directional trend.
Starting around 2017 there is a clear and sustained decline, dropping
from 71.4 down to 70.2 by 2024, nearly a full stroke lower per round
across the entire field in just seven years. This is well documented in
professional golf and is driven by better equipment, launch monitor
technology, and players approaching the game more like elite athletes.
For the modeling section this trend is worth noting, a scoring average
of 71.0 in 2010 represents a different level of performance relative to
the field than a 71.0 in 2023.

``` python
#Visual 2: Average Score over Time

import seaborn as sns
import matplotlib.pyplot as plt

df_clean = df_out[df_out['SCORE'] > 0].copy()

score_by_season = df_clean.groupby('season')['SCORE'].mean().reset_index()

sns.lineplot(data=score_by_season, x='season', y='SCORE')
plt.title('Average Player Score by Season')
plt.xlabel('Season')
plt.ylabel('Average Score')
plt.show()
```

![](PGA%20Tour_files/figure-markdown_strict/cell-5-output-1.png)

For this visual, I want to see the overall top players within this time
period. Being a golf fan, I already know the big names. However, this
graph just validates my knowledge and shows just how dominant Tiger
Woods has been the past 20 years. If this data went back even further,
it would add to the narrative that Tiger is even more dominant than this
graph shows.

``` python
#Visual 3: Top 10 Players by Career Wins

import seaborn as sns
import matplotlib.pyplot as plt

career_wins = df_out.groupby('player')['WINS'].sum().reset_index()
career_wins = career_wins.sort_values('WINS', ascending=False).head(10).reset_index(drop=True)


sns.barplot(data=career_wins, x='WINS', y='player')
plt.title('Top 10 Players by Career Wins (2007-2024)')
plt.xlabel('Total Wins')
plt.ylabel('Player')
plt.show()
```

![](PGA%20Tour_files/figure-markdown_strict/cell-6-output-1.png)

This correlation heatmap highlights the importance the statistics that
are being evaluated are correlated with each other. For example, driving
distance, driving accuracy, greens in regulation, number of putts, and
save percentages correspond to each other. They all go hand in hand with
each other, ultimately affecting one another. As a golfer, this all
makes sense, the less times you hit the fairway on a drive, will most
likely lead to hitting an approach shot on the green, which will most
likely lead to being farther away from the hole when you putt.

It is also important to point out that number of rounds and cuts made
have a strong correlation with all of the individual player statistics.
The better one player plays, the more likely they are to play more
rounds and make more number of cuts.

``` python
#Visual 4: Correlation Heatmap

import seaborn as sns
import matplotlib.pyplot as plt


numeric_cols = ['EARNINGS', 'EVNTS', 'RNDS', 'CUTS', 'TOP10', 'WINS',
                'SCORE', 'DDIS', 'DACC', 'GIR', 'PUTTS', 'SAND', 'BIRDS']

numeric_cols = [col for col in numeric_cols if col in df_out.columns]

corr_matrix = df_out[numeric_cols].corr()

sns.heatmap(corr_matrix, cmap='coolwarm', center=0)
plt.title('Correlation Heatmap of PGA Tour Stats')
plt.tight_layout()
plt.show()
```

![](PGA%20Tour_files/figure-markdown_strict/cell-7-output-1.png)

This scatterplot shows the relationship between greens in regulation
percentage and average score. The negative trend is clear, players who
hit more greens in regulation consistently shoot lower scores. This
makes intuitive sense because hitting a green in regulation gives a
player two putts to make par, meaning more GIR opportunities directly
translates to more birdie chances and fewer bogeys. The cluster is
densest between 63% and 72% GIR, which represents the typical range for
a full-time PGA Tour player. The few outliers on the lower left with low
GIR and low scores are likely players with exceptional short games who
compensate for missing greens by getting up and down consistently.

``` python
#Visual 5: GIR vs Average Score

import seaborn as sns
import matplotlib.pyplot as plt

df_gir = df_out[(df_out['GIR'] > 50) & (df_out['SCORE'] > 50)].copy()


sns.scatterplot(data=df_gir, x='GIR', y='SCORE', alpha=0.4)
plt.title('Greens in Regulation vs Average Score')
plt.xlabel('Greens in Regulation (%)')
plt.ylabel('Average Score')
plt.show()
```

![](PGA%20Tour_files/figure-markdown_strict/cell-8-output-1.png)

This scatterplot confirms one of the most well known tradeoffs in
professional golf, the longer a player hits the ball, the less accurate
they tend to be off the tee. The negative relationship is clear across
the full range of the data, with players averaging 260-270 yards
typically hitting 65-75% of fairways, while players averaging 310-325
yards drop closer to 50-55% accuracy. This tradeoff exists because
hitting the ball harder and further requires a more aggressive swing
that is harder to control. In recent years players like Bryson
DeChambeau have pushed the distance extreme further, accepting lower
accuracy in exchange for shorter approach shots into greens.

``` python
#Visual 6: Driving Accuracy vs Driving Distance

import seaborn as sns
import matplotlib.pyplot as plt

df_drive = df_out[(df_out['DDIS'] > 200) & (df_out['DACC'] > 10)].copy()

sns.scatterplot(data=df_drive, x='DDIS', y='DACC', alpha=0.4)
plt.title('Driving Distance vs Driving Accuracy')
plt.xlabel('Average Driving Distance (yards)')
plt.ylabel('Driving Accuracy (%)')
plt.show()
```

![](PGA%20Tour_files/figure-markdown_strict/cell-9-output-1.png)

I create two rate-based features, cuts made rate and top 10 rate, which
normalize performance relative to how many events a player entered. A
player who made 15 cuts in 20 events is more impressive than one who
made 15 cuts in 30 events, and these rates capture that. I also apply a
log transformation to earnings because of the right skew we saw in the
distribution. Log transforming a skewed target variable helps models
perform better.

``` python
df_copy = df_out.copy()

df_copy['cuts_made_rate'] = df_copy['CUTS'] / df_copy['EVNTS']
df_copy['top10_rate']     = df_copy['TOP10'] / df_copy['EVNTS']
df_copy['log_earnings']   = np.log1p(df_copy['EARNINGS'])


df_copy
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>

<table class="dataframe" data-quarto-postprocess="true" data-border="1">
<thead>
<tr style="text-align: right;">
<th data-quarto-table-cell-role="th"></th>
<th data-quarto-table-cell-role="th">season</th>
<th data-quarto-table-cell-role="th">rank</th>
<th data-quarto-table-cell-role="th">player</th>
<th data-quarto-table-cell-role="th">EARNINGS</th>
<th data-quarto-table-cell-role="th">CUP</th>
<th data-quarto-table-cell-role="th">EVNTS</th>
<th data-quarto-table-cell-role="th">RNDS</th>
<th data-quarto-table-cell-role="th">CUTS</th>
<th data-quarto-table-cell-role="th">TOP10</th>
<th data-quarto-table-cell-role="th">WINS</th>
<th data-quarto-table-cell-role="th">SCORE</th>
<th data-quarto-table-cell-role="th">DDIS</th>
<th data-quarto-table-cell-role="th">DACC</th>
<th data-quarto-table-cell-role="th">GIR</th>
<th data-quarto-table-cell-role="th">PUTTS</th>
<th data-quarto-table-cell-role="th">SAND</th>
<th data-quarto-table-cell-role="th">BIRDS</th>
<th data-quarto-table-cell-role="th">cuts_made_rate</th>
<th data-quarto-table-cell-role="th">top10_rate</th>
<th data-quarto-table-cell-role="th">log_earnings</th>
</tr>
</thead>
<tbody>
<tr>
<td data-quarto-table-cell-role="th">0</td>
<td>2007</td>
<td>1</td>
<td>Tiger Woods</td>
<td>10867052</td>
<td>123033</td>
<td>16</td>
<td>61</td>
<td>16</td>
<td>12</td>
<td>7</td>
<td>69.2</td>
<td>302.4</td>
<td>59.8</td>
<td>69.9</td>
<td>1.733</td>
<td>52.0</td>
<td>4.066</td>
<td>1.000000</td>
<td>0.750000</td>
<td>16.201246</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">1</td>
<td>2007</td>
<td>2</td>
<td>Phil Mickelson</td>
<td>5819988</td>
<td>109358</td>
<td>22</td>
<td>73</td>
<td>16</td>
<td>7</td>
<td>3</td>
<td>69.3</td>
<td>298.1</td>
<td>56.9</td>
<td>63.2</td>
<td>1.751</td>
<td>50.9</td>
<td>3.644</td>
<td>0.727273</td>
<td>0.318182</td>
<td>15.576809</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">2</td>
<td>2007</td>
<td>3</td>
<td>Vijay Singh</td>
<td>4728377</td>
<td>101064</td>
<td>27</td>
<td>101</td>
<td>25</td>
<td>7</td>
<td>2</td>
<td>70.4</td>
<td>293.8</td>
<td>59.7</td>
<td>65.6</td>
<td>1.768</td>
<td>53.3</td>
<td>3.624</td>
<td>0.925926</td>
<td>0.259259</td>
<td>15.369093</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">3</td>
<td>2007</td>
<td>4</td>
<td>Steve Stricker</td>
<td>4663077</td>
<td>110455</td>
<td>23</td>
<td>80</td>
<td>19</td>
<td>9</td>
<td>1</td>
<td>70.2</td>
<td>283.7</td>
<td>63.7</td>
<td>65.8</td>
<td>1.743</td>
<td>47.5</td>
<td>3.613</td>
<td>0.826087</td>
<td>0.391304</td>
<td>15.355186</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">4</td>
<td>2007</td>
<td>5</td>
<td>K.J. Choi</td>
<td>4587859</td>
<td>103765</td>
<td>25</td>
<td>88</td>
<td>20</td>
<td>7</td>
<td>2</td>
<td>68.8</td>
<td>284.1</td>
<td>64.7</td>
<td>64.5</td>
<td>1.778</td>
<td>58.4</td>
<td>3.489</td>
<td>0.800000</td>
<td>0.280000</td>
<td>15.338924</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
<td>...</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">5799</td>
<td>2024</td>
<td>241</td>
<td>Kyle Stanley</td>
<td>8800</td>
<td>3</td>
<td>2</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.000</td>
<td>0.0</td>
<td>0.000</td>
<td>0.500000</td>
<td>0.000000</td>
<td>9.082621</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">5800</td>
<td>2024</td>
<td>242</td>
<td>Anders Albertson</td>
<td>8760</td>
<td>3</td>
<td>6</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.000</td>
<td>0.0</td>
<td>0.000</td>
<td>0.166667</td>
<td>0.000000</td>
<td>9.078065</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">5801</td>
<td>2024</td>
<td>243</td>
<td>Ryan Armour</td>
<td>8360</td>
<td>2</td>
<td>3</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.000</td>
<td>0.0</td>
<td>0.000</td>
<td>0.333333</td>
<td>0.000000</td>
<td>9.031333</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">5802</td>
<td>2024</td>
<td>243</td>
<td>Brian Stuard</td>
<td>8360</td>
<td>2</td>
<td>2</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.000</td>
<td>0.0</td>
<td>0.000</td>
<td>0.500000</td>
<td>0.000000</td>
<td>9.031333</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">5803</td>
<td>2024</td>
<td>245</td>
<td>Chris Stroud</td>
<td>8200</td>
<td>2</td>
<td>1</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.0</td>
<td>0.000</td>
<td>0.0</td>
<td>0.000</td>
<td>1.000000</td>
<td>0.000000</td>
<td>9.012011</td>
</tr>
</tbody>
</table>

<p>5804 rows × 20 columns</p>
</div>

Linear Regression Model

This linear regression model for predicting season earnings produced a
r-squared value around 0.66-0.68. This means that the 12 variables
explains on average 66-68% of the variation in player earnings. The
strongest predictor by far was the percentage that the player finished
in the top 10 of tournaments they played in. Putts per round was the
most negative predictor, reflecting that the worse a player is at
putting, the stronger it is associated with lower earnings. Overall, the
regression results were not conclusive enough, suggesting that a model
like Random Forest will produce better results.

``` python
#Linear Regression for Earnings 

import statsmodels.formula.api as smf

lr_earnings = smf.ols(
    formula='EARNINGS ~ EVNTS + RNDS + CUP + cuts_made_rate + top10_rate + SCORE + DDIS + DACC + GIR + PUTTS + SAND + BIRDS',
    data=df_copy
).fit()

lr_earnings.summary()
```

<table class="simpletable" data-quarto-postprocess="true">
<caption>OLS Regression Results</caption>
<tbody>
<tr>
<td data-quarto-table-cell-role="th">Dep. Variable:</td>
<td>EARNINGS</td>
<td data-quarto-table-cell-role="th">R-squared:</td>
<td>0.669</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Model:</td>
<td>OLS</td>
<td data-quarto-table-cell-role="th">Adj. R-squared:</td>
<td>0.668</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Method:</td>
<td>Least Squares</td>
<td data-quarto-table-cell-role="th">F-statistic:</td>
<td>945.3</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Date:</td>
<td>Fri, 27 Feb 2026</td>
<td data-quarto-table-cell-role="th">Prob (F-statistic):</td>
<td>0.00</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Time:</td>
<td>22:50:19</td>
<td data-quarto-table-cell-role="th">Log-Likelihood:</td>
<td>-85085.</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">No. Observations:</td>
<td>5622</td>
<td data-quarto-table-cell-role="th">AIC:</td>
<td>1.702e+05</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Df Residuals:</td>
<td>5609</td>
<td data-quarto-table-cell-role="th">BIC:</td>
<td>1.703e+05</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Df Model:</td>
<td>12</td>
<td data-quarto-table-cell-role="th"></td>
<td></td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Covariance Type:</td>
<td>nonrobust</td>
<td data-quarto-table-cell-role="th"></td>
<td></td>
</tr>
</tbody>
</table>

<table class="simpletable" data-quarto-postprocess="true">
<tbody>
<tr>
<td></td>
<td data-quarto-table-cell-role="th">coef</td>
<td data-quarto-table-cell-role="th">std err</td>
<td data-quarto-table-cell-role="th">t</td>
<td data-quarto-table-cell-role="th">P&gt;|t|</td>
<td data-quarto-table-cell-role="th">[0.025</td>
<td data-quarto-table-cell-role="th">0.975]</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Intercept</td>
<td>4.787e+04</td>
<td>9.12e+04</td>
<td>0.525</td>
<td>0.600</td>
<td>-1.31e+05</td>
<td>2.27e+05</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">EVNTS</td>
<td>-3.071e+04</td>
<td>6703.604</td>
<td>-4.580</td>
<td>0.000</td>
<td>-4.38e+04</td>
<td>-1.76e+04</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">RNDS</td>
<td>2.32e+04</td>
<td>2105.640</td>
<td>11.018</td>
<td>0.000</td>
<td>1.91e+04</td>
<td>2.73e+04</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">CUP</td>
<td>0.2274</td>
<td>0.619</td>
<td>0.368</td>
<td>0.713</td>
<td>-0.985</td>
<td>1.440</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">cuts_made_rate</td>
<td>5.128e+05</td>
<td>6.9e+04</td>
<td>7.426</td>
<td>0.000</td>
<td>3.77e+05</td>
<td>6.48e+05</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">top10_rate</td>
<td>5.515e+06</td>
<td>1.2e+05</td>
<td>46.143</td>
<td>0.000</td>
<td>5.28e+06</td>
<td>5.75e+06</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">SCORE</td>
<td>-6208.5091</td>
<td>1361.667</td>
<td>-4.559</td>
<td>0.000</td>
<td>-8877.903</td>
<td>-3539.115</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">DDIS</td>
<td>5.29e+04</td>
<td>2163.005</td>
<td>24.457</td>
<td>0.000</td>
<td>4.87e+04</td>
<td>5.71e+04</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">DACC</td>
<td>4.449e+04</td>
<td>4150.696</td>
<td>10.718</td>
<td>0.000</td>
<td>3.64e+04</td>
<td>5.26e+04</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">GIR</td>
<td>2.028e+04</td>
<td>6010.479</td>
<td>3.374</td>
<td>0.001</td>
<td>8498.943</td>
<td>3.21e+04</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">PUTTS</td>
<td>-1.166e+07</td>
<td>3.84e+05</td>
<td>-30.338</td>
<td>0.000</td>
<td>-1.24e+07</td>
<td>-1.09e+07</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">SAND</td>
<td>2.758e+04</td>
<td>2541.537</td>
<td>10.852</td>
<td>0.000</td>
<td>2.26e+04</td>
<td>3.26e+04</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">BIRDS</td>
<td>-5.618e+04</td>
<td>2.39e+04</td>
<td>-2.350</td>
<td>0.019</td>
<td>-1.03e+05</td>
<td>-9306.181</td>
</tr>
</tbody>
</table>

<table class="simpletable" data-quarto-postprocess="true">
<tbody>
<tr>
<td data-quarto-table-cell-role="th">Omnibus:</td>
<td>5742.242</td>
<td data-quarto-table-cell-role="th">Durbin-Watson:</td>
<td>0.937</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Prob(Omnibus):</td>
<td>0.000</td>
<td data-quarto-table-cell-role="th">Jarque-Bera (JB):</td>
<td>1575884.256</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Skew:</td>
<td>4.467</td>
<td data-quarto-table-cell-role="th">Prob(JB):</td>
<td>0.00</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Kurtosis:</td>
<td>84.532</td>
<td data-quarto-table-cell-role="th">Cond. No.</td>
<td>6.94e+05</td>
</tr>
</tbody>
</table>

<br/><br/>Notes:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 6.94e+05. This might indicate that there are<br/>strong multicollinearity or other numerical problems.

This linear regression model for predicting season wins produced a
r-squared value of around 0.26-0.28. This means that the 12 variables
explains on average 26-28% of the variation in players wins. The
strongest predictor by far was the amount of FedEx cup points a player
has. This is very intuitive, proving that the more times one wins,it
correlates to more FedEx points. Overall, the regression results were
worse than the linear regression for prediciting a players earnings.
This supports my decision to pivot to another model, one that captures
non-linear relationships.

``` python
#Linear Regression for Wins

import statsmodels.formula.api as smf

lr_wins = smf.ols(
    formula='WINS ~ EVNTS + RNDS + CUP + cuts_made_rate  + top10_rate + SCORE + DDIS + DACC + GIR + PUTTS + SAND + BIRDS',
    data=df_copy
).fit()

lr_wins.summary()
```

<table class="simpletable" data-quarto-postprocess="true">
<caption>OLS Regression Results</caption>
<tbody>
<tr>
<td data-quarto-table-cell-role="th">Dep. Variable:</td>
<td>WINS</td>
<td data-quarto-table-cell-role="th">R-squared:</td>
<td>0.266</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Model:</td>
<td>OLS</td>
<td data-quarto-table-cell-role="th">Adj. R-squared:</td>
<td>0.264</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Method:</td>
<td>Least Squares</td>
<td data-quarto-table-cell-role="th">F-statistic:</td>
<td>169.0</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Date:</td>
<td>Fri, 27 Feb 2026</td>
<td data-quarto-table-cell-role="th">Prob (F-statistic):</td>
<td>0.00</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Time:</td>
<td>22:50:20</td>
<td data-quarto-table-cell-role="th">Log-Likelihood:</td>
<td>-3107.7</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">No. Observations:</td>
<td>5622</td>
<td data-quarto-table-cell-role="th">AIC:</td>
<td>6241.</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Df Residuals:</td>
<td>5609</td>
<td data-quarto-table-cell-role="th">BIC:</td>
<td>6328.</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Df Model:</td>
<td>12</td>
<td data-quarto-table-cell-role="th"></td>
<td></td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Covariance Type:</td>
<td>nonrobust</td>
<td data-quarto-table-cell-role="th"></td>
<td></td>
</tr>
</tbody>
</table>

<table class="simpletable" data-quarto-postprocess="true">
<tbody>
<tr>
<td></td>
<td data-quarto-table-cell-role="th">coef</td>
<td data-quarto-table-cell-role="th">std err</td>
<td data-quarto-table-cell-role="th">t</td>
<td data-quarto-table-cell-role="th">P&gt;|t|</td>
<td data-quarto-table-cell-role="th">[0.025</td>
<td data-quarto-table-cell-role="th">0.975]</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Intercept</td>
<td>-0.0184</td>
<td>0.042</td>
<td>-0.435</td>
<td>0.664</td>
<td>-0.102</td>
<td>0.065</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">EVNTS</td>
<td>-0.0047</td>
<td>0.003</td>
<td>-1.493</td>
<td>0.136</td>
<td>-0.011</td>
<td>0.001</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">RNDS</td>
<td>0.0022</td>
<td>0.001</td>
<td>2.268</td>
<td>0.023</td>
<td>0.000</td>
<td>0.004</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">CUP</td>
<td>1.069e-06</td>
<td>2.88e-07</td>
<td>3.718</td>
<td>0.000</td>
<td>5.05e-07</td>
<td>1.63e-06</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">cuts_made_rate</td>
<td>0.0621</td>
<td>0.032</td>
<td>1.936</td>
<td>0.053</td>
<td>-0.001</td>
<td>0.125</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">top10_rate</td>
<td>1.5212</td>
<td>0.056</td>
<td>27.378</td>
<td>0.000</td>
<td>1.412</td>
<td>1.630</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">SCORE</td>
<td>-0.0008</td>
<td>0.001</td>
<td>-1.307</td>
<td>0.191</td>
<td>-0.002</td>
<td>0.000</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">DDIS</td>
<td>0.0099</td>
<td>0.001</td>
<td>9.843</td>
<td>0.000</td>
<td>0.008</td>
<td>0.012</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">DACC</td>
<td>0.0064</td>
<td>0.002</td>
<td>3.333</td>
<td>0.001</td>
<td>0.003</td>
<td>0.010</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">GIR</td>
<td>-0.0017</td>
<td>0.003</td>
<td>-0.621</td>
<td>0.535</td>
<td>-0.007</td>
<td>0.004</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">PUTTS</td>
<td>-1.8392</td>
<td>0.179</td>
<td>-10.296</td>
<td>0.000</td>
<td>-2.189</td>
<td>-1.489</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">SAND</td>
<td>0.0026</td>
<td>0.001</td>
<td>2.205</td>
<td>0.027</td>
<td>0.000</td>
<td>0.005</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">BIRDS</td>
<td>-0.0046</td>
<td>0.011</td>
<td>-0.414</td>
<td>0.679</td>
<td>-0.026</td>
<td>0.017</td>
</tr>
</tbody>
</table>

<table class="simpletable" data-quarto-postprocess="true">
<tbody>
<tr>
<td data-quarto-table-cell-role="th">Omnibus:</td>
<td>4509.792</td>
<td data-quarto-table-cell-role="th">Durbin-Watson:</td>
<td>1.405</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Prob(Omnibus):</td>
<td>0.000</td>
<td data-quarto-table-cell-role="th">Jarque-Bera (JB):</td>
<td>211001.944</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Skew:</td>
<td>3.475</td>
<td data-quarto-table-cell-role="th">Prob(JB):</td>
<td>0.00</td>
</tr>
<tr>
<td data-quarto-table-cell-role="th">Kurtosis:</td>
<td>32.197</td>
<td data-quarto-table-cell-role="th">Cond. No.</td>
<td>6.94e+05</td>
</tr>
</tbody>
</table>

<br/><br/>Notes:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 6.94e+05. This might indicate that there are<br/>strong multicollinearity or other numerical problems.

Random Forests

In order to run a Random Forests model, a time-based train/test split is
used, training on seasons prior to 2023 and testing on 2023 and 2024.

``` python
#Preparing Data
df_model = df_copy.copy()
df_model = df_model.dropna(subset=['EVNTS', 'RNDS', 'CUP', 'cuts_made_rate', 'top10_rate','SCORE', 'DDIS', 'DACC', 'GIR', 'PUTTS','SAND', 'BIRDS', 'EARNINGS', 'WINS']).copy()
df_model['season'] = df_model['season'].astype(int)

#Time based split
train_df = df_model[df_model['season'] < 2020].copy()
test_df  = df_model[df_model['season'] >= 2020].copy()

FEATURES = ['EVNTS', 'RNDS', 'CUP', 'cuts_made_rate', 'top10_rate',
            'SCORE', 'DDIS', 'DACC', 'GIR', 'PUTTS', 'SAND', 'BIRDS']

X_train = train_df[FEATURES].values  
X_test  = test_df[FEATURES].values   

#Earnings targets
y_train_earn = train_df['EARNINGS'].values  
y_test_earn  = test_df['EARNINGS'].values   

#Wins targets
y_train_wins = train_df['WINS'].values  
y_test_wins  = test_df['WINS'].values   
```

Random Forests

The earnings model produces a r-squared of around 0.69 with an MAE of
approximately $610K. This Random Forest model explains approximately 69%
of the variation of earnings within the data. Compared to the linear
regression baseline, the Random Forest model performed similarly. Even
though it is not a significant increase, I trust the results of this
model more due to the ability to capture the non-linear relationships
and it overall higher complexity.

``` python
#Random Forest Earnings
#%pip install --upgrade scikit-learn
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_absolute_error, r2_score


#Fit Random Forest model
rf_earn = RandomForestRegressor(n_estimators=300, 
                                 random_state=42)   
rf_earn.fit(X_train, y_train_earn) 

#Predict and evaluate
y_pred_rf_earn = rf_earn.predict(X_test) 

mae_rf_earn  = mean_absolute_error(y_test_earn, y_pred_rf_earn)
r2_rf_earn   = r2_score(y_test_earn, y_pred_rf_earn) 


print(mae_rf_earn)
print(r2_rf_earn)
```

    604958.6282663494
    0.6918819223817221

This Random Forests model that predicts the number of wins achieved a
r-squared value of around 0.3, which means it explains approximately 30%
of variation of number of wins. This model produces an increase in
r-squared value, suggesting that the Random Forest model better captures
the nonlinear relationship between variables in the data.

``` python
#Random Forests Wins
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_absolute_error, r2_score


#Fit Random Forest model
rf_wins = RandomForestRegressor(n_estimators=300,  
                                 random_state=42)   
rf_wins.fit(X_train, y_train_wins) 

#Predict and evaluate
y_pred_rf_wins = rf_wins.predict(X_test)

mae_rf_wins  = mean_absolute_error(y_test_wins, y_pred_rf_wins)
r2_rf_wins   = r2_score(y_test_wins, y_pred_rf_wins) 


print(mae_rf_wins)
print(r2_rf_wins)
```

    0.26765389082462254
    0.31824127666501734

Feature Importance

The feature importance results from the Random Forest earnings model
highlight the top 10 rate as the most influential predictor by far.
These results align closely with the results produced by the linear
regression results. At second, was the number of FedEx cup points. This
can be validated because the more Fedex points a player accumulates,
most likely correlates to a more successful season, which will
ultimately lead to more money earned. Rounds played was the third most
influential predictor, showing that the more number of rounds played
will lead to more earnings.

``` python
#Extract feature importance Earnings
importance_earn = pd.DataFrame({
    'feature':    FEATURES,                     
    'importance': rf_earn.feature_importances_  
}).sort_values('importance', ascending=False).reset_index(drop=True)


sns.barplot(data=importance_earn, x='importance', y='feature') 
plt.title('Random Forest Feature Importance - Earnings')     
plt.xlabel('Importance')                                      
plt.ylabel('Feature')                                         
plt.show()                                              
```

![](PGA%20Tour_files/figure-markdown_strict/cell-16-output-1.png)

The feature importance results from the Random Forest wins model
reinforce the Random Forest earnings model above with the top 10 rate as
the dominant predictor. The second predictor is FedEx cup points,
reflecting that the points system rewards consistent high finishes which
are closely aligned to winning. Unlike the earnings model, the wins
model spreads importance more evenly across ball-striking stats, like
birdies, driving distance, driving accuracy, etc.

``` python
#Extract feature importance Wins
importance_wins = pd.DataFrame({
    'feature':    FEATURES,                    
    'importance': rf_wins.feature_importances_  
}).sort_values('importance', ascending=False).reset_index(drop=True)


sns.barplot(data=importance_wins, x='importance', y='feature') 
plt.title('Random Forest Feature Importance - Wins')  
plt.xlabel('Importance')                             
plt.ylabel('Feature')                                       
plt.show()  
```

![](PGA%20Tour_files/figure-markdown_strict/cell-17-output-1.png)

Prediction

The Random Forest model projects that Scottie Scheffler will lead the
tour with total earnings and total amount of wins in the 2026 season.
This is consistent based on the results of the 2024-2025 season. The
rest of the top 10 seems to pass the eye test, as all the golfers have
the capability and potential to lead the tour in wins and total earnings
this year.

``` python
df_2025 = df_copy[df_copy['season'] == 2024].copy()
df_2025['cuts_made_rate'] = df_2025['CUTS'] / df_2025['EVNTS']
df_2025['top10_rate'] = df_2025['TOP10'] / df_2025['EVNTS']


df_2026 = df_2025[FEATURES + ['player']].dropna().reset_index(drop=True)

# Generate predictions using Random Forest
df_2026['predicted_earnings_2026'] = rf_earn.predict(df_2026[FEATURES].values)
df_2026['predicted_wins_2026'] = rf_wins.predict(df_2026[FEATURES].values)

df_2026_out = (df_2026[['player', 'predicted_earnings_2026', 'predicted_wins_2026']].sort_values('predicted_earnings_2026', ascending=False).reset_index(drop=True))

print(df_2026_out.head(10))
```

                  player  predicted_earnings_2026  predicted_wins_2026
    0  Scottie Scheffler             9.585389e+06             5.513333
    1  Xander Schauffele             9.153573e+06             4.540000
    2          Sam Burns             7.235249e+06             2.343333
    3    Collin Morikawa             7.128247e+06             2.100000
    4      Wyndham Clark             6.854002e+06             2.680000
    5       Ludvig Åberg             6.653509e+06             1.920000
    6       Rory McIlroy             5.867475e+06             1.516667
    7    Sahith Theegala             5.504874e+06             1.603333
    8    Taylor Pendrith             5.153202e+06             1.513333
    9   Hideki Matsuyama             4.759599e+06             1.356667

Conclusion:

The goal of this project is to use data science to better understand
what seperates the best players on the PGA Tour from everyone else. By
using different methods, linear regression and Random Forests, I built
these models to predict two key outcomes for the PGA Tour. These
outcomes consisted of season earnings and tournament wins, two big
categories that every golfer strive to be at the top of.

Golf is more than just a sport. It is a “language” that is broken into
many different aspects of the world, whether that is business, military,
and most important entertainment. The world of golf has dramatically
evolved over the past handful of years, mostly due to COVID-19. Many
people had the chance the fall in love with the game and it has steadily
become more popular since then. As someone that loves the game and is
someone pursuing a career in both military and potentially business, I
understand firsthand that some of the most important relationships and
deals happen on a golf course. That personal connection is what
motivated this project.

# IPL Data Analysis 2008 to 2025 





### Q1: Total Sixes in the season? 



Dax formula : - 



Total 6's = 

CALCULATE(COUNTROWS(ball\_by\_ball\_data), ball\_by\_ball\_data\[batter\_runs] = 6, 

&nbsp;           KEEPFILTERS(VALUE(ipl\_matches\_data\[season])))







### Q2: Total Fours in the season?



Dax formula: - 



Total 4's = 

CALCULATE(COUNTROWS(ball\_by\_ball\_data), ball\_by\_ball\_data\[batter\_runs] = 4, 

&nbsp;           KEEPFILTERS(VALUE(ipl\_matches\_data\[season])))





### Q3: Total Matches in the season?



Dax formula: - 



Total Matches = CALCULATE(DISTINCTCOUNT(ipl\_matches\_data\[match\_id]))





### Q4: Total Teams in the season?



Dax formula: - 



Total Teams = CALCULATE(DISTINCTCOUNT(ipl\_matches\_data\[team1]))





### Q5: Total Centuries in the season?



Dax formula: - 



Centuries = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season]) 

VAR SeasonData = FILTER(ball\_by\_ball\_data,

&nbsp;                       RELATED(ipl\_matches\_data\[season])= SelectedSeason)

VAR BattetRuns = 

&nbsp;               SUMMARIZE(SeasonData,ball\_by\_ball\_data\[match\_id],

&nbsp;                       ball\_by\_ball\_data\[batter], "TotalRuns", SUM(ball\_by\_ball\_data\[batter\_runs]))

VAR CenturyCount = FILTER(BattetRuns, \[TotalRuns] >=100)

RETURN COUNTROWS(CenturyCount)





### Q6: Total Half Centuries in the season?



Dax formula: - 



Half Centuries = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season]) 

VAR SeasonData = FILTER(ball\_by\_ball\_data,

&nbsp;                       RELATED(ipl\_matches\_data\[season])= SelectedSeason)

VAR BattetRuns = 

&nbsp;               SUMMARIZE(SeasonData,ball\_by\_ball\_data\[match\_id],

&nbsp;                       ball\_by\_ball\_data\[batter], "TotalRuns", SUM(ball\_by\_ball\_data\[batter\_runs]))

VAR CenturyCount = FILTER(BattetRuns, \[TotalRuns] >=50 \&\& \[TotalRuns]<100)

RETURN COUNTROWS(CenturyCount)





### Q7: Total Venues in the season?



Dax formula: - 



Total Venues = 

CALCULATE(DISTINCTCOUNT(ipl\_matches\_data\[venue]))







## Orange Cap Stats



### Q8: Orange Cap winner Name



Dax formula: - 





Orange Cap Holder = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonDataOnly = 

&nbsp;                   FILTER(ball\_by\_ball\_data,RELATED(ipl\_matches\_data\[season]) = SelectedSeason)

VAR RunSummary = 

&nbsp;               SUMMARIZE(SeasonDataOnly,ball\_by\_ball\_data\[batter], "Total Runs", SUM(ball\_by\_ball\_data\[batter\_runs]))

VAR MaxRuns = MAXX(RunSummary, \[Total Runs])

VAR TopScorer = 

&nbsp;               CALCULATETABLE(VALUES(ball\_by\_ball\_data\[batter]), FILTER(RunSummary, \[Total Runs] = MaxRuns))

RETURN MAXX(TopScorer,ball\_by\_ball\_data\[batter])





### Q9: Orange Cap winner Total Runs Scored



Dax formula: - 





Orange Cap Run = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonDataOnly = 

&nbsp;                   FILTER(ball\_by\_ball\_data,RELATED(ipl\_matches\_data\[season]) = SelectedSeason)

VAR RunSummary = 

&nbsp;               SUMMARIZE(SeasonDataOnly,ball\_by\_ball\_data\[batter], "Total Runs", SUM(ball\_by\_ball\_data\[batter\_runs]))

VAR MaxRuns = MAXX(RunSummary, \[Total Runs])

RETURN MaxRuns



### Q10: Orange Cap winner Team Name



Dax formula: - 





Orange Cap Team Name = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonDataOnly = 

&nbsp;                   FILTER(ball\_by\_ball\_data,RELATED(ipl\_matches\_data\[season]) = SelectedSeason)

VAR RunSummary = 

&nbsp;               SUMMARIZE(SeasonDataOnly,ball\_by\_ball\_data\[batter], "Total Runs", SUM(ball\_by\_ball\_data\[batter\_runs]))

VAR MaxRuns = MAXX(RunSummary, \[Total Runs])

VAR TopScorer = 

&nbsp;               CALCULATETABLE(VALUES(ball\_by\_ball\_data\[batter]), FILTER(RunSummary, \[Total Runs] = MaxRuns))

VAR FullTeamName =

&nbsp;               CALCULATE(MAX(ball\_by\_ball\_data\[team\_batting]), FILTER(SeasonDataOnly,

&nbsp;               ball\_by\_ball\_data\[batter] = MAXX(TopScorer, ball\_by\_ball\_data\[batter])))

RETURN 

FullTeamName



### Q11: Orange Cap winner Player Image (dynamically rendered)



Dax formula: - 





Orange Cap Image = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonDataOnly = 

&nbsp;                   FILTER(ball\_by\_ball\_data,RELATED(ipl\_matches\_data\[season]) = SelectedSeason)

VAR RunSummary = 

&nbsp;               SUMMARIZE(SeasonDataOnly,ball\_by\_ball\_data\[batter], "Total Runs", SUM(ball\_by\_ball\_data\[batter\_runs]))

VAR MaxRuns = MAXX(RunSummary, \[Total Runs])

VAR TopScorer = 

&nbsp;               CALCULATETABLE(VALUES(ball\_by\_ball\_data\[batter]), FILTER(RunSummary, \[Total Runs] = MaxRuns))

RETURN

LOOKUPVALUE('players-data-updated'\[player\_image], 'players-data-updated'\[player\_name], MAXX(TopScorer, ball\_by\_ball\_data\[batter]))





## Purple Cap Stats





### Q12: Purple Cap winner Name



Dax formula: - 





PurpleCapHolder = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

--Filter: wickets in selected seaason, exclude non-bowler dismissals

VAR SeasonWickets = 

&nbsp;   FILTER(

&nbsp;       ball\_by\_ball\_data,

&nbsp;       RELATED(ipl\_matches\_data\[season]) = SelectedSeason \&\&

&nbsp;       ball\_by\_ball\_data\[is\_wicket] = TRUE() \&\&

&nbsp;       NOT ball\_by\_ball\_data\[wicket\_kind] IN { "run out", "retired hurt", "obstructing the field", "retired out"}

&nbsp;   )

--Summarize bowler and count wickets

VAR WicketSummary = 

&nbsp;   SUMMARIZE(

&nbsp;       SeasonWickets,

&nbsp;       ball\_by\_ball\_data\[bowler],

&nbsp;       "WicketCount", COUNTROWS(

&nbsp;           FILTER(SeasonWickets, ball\_by\_ball\_data\[bowler] = EARLIER(ball\_by\_ball\_data\[bowler]))

&nbsp;       )

&nbsp;   )

--Find highest wicket count 

VAR MaxWickets = MAXX(WicketSummary, \[WicketCount])

--Get the bowler(s) with that wicket count

VAR TopBowler = 

&nbsp;   CALCULATETABLE(

&nbsp;       VALUES(ball\_by\_ball\_data\[bowler]),

&nbsp;       FILTER(WicketSummary, \[WicketCount] = MaxWickets)





### Q13: Purple Cap winner Total Wickets



Dax formula: - 



PurpleCapWicketCount = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

-- Step 1: Filter valid bowler wickets in selected season

VAR SeasonWickets =

&nbsp;   FILTER(

&nbsp;       ball\_by\_ball\_data,

&nbsp;       RELATED(ipl\_matches\_data\[season]) = SelectedSeason \&\&

&nbsp;       ball\_by\_ball\_data\[is\_wicket] = TRUE() \&\&

&nbsp;       NOT ball\_by\_ball\_data\[wicket\_kind] IN {"run out", "retired hurt", "retired out", "obstructing the field"}

&nbsp;   )

-- Step 2: Summarize wickets per bowler

VAR WicketSummary =

&nbsp;   SUMMARIZE(

&nbsp;       SeasonWickets,

&nbsp;       ball\_by\_ball\_data\[bowler],

&nbsp;       "WicketCount", COUNTROWS(

&nbsp;           FILTER(

&nbsp;               SeasonWickets,

&nbsp;               ball\_by\_ball\_data\[bowler] = EARLIER(ball\_by\_ball\_data\[bowler])

&nbsp;           )

&nbsp;       )

&nbsp;   )

-- Step 3: Get the highest wicket count

VAR MaxWickets = MAXX(WicketSummary, \[WicketCount])

RETURN

&nbsp;   MaxWickets





### Q14: Purple Cap winner Team Name



Dax formula: - 



PurpleCapTeam = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

-- Step 1: Filter valid wickets for the selected season

VAR SeasonWickets =

&nbsp;   FILTER(

&nbsp;       ball\_by\_ball\_data,

&nbsp;       RELATED(ipl\_matches\_data\[season]) = SelectedSeason \&\&

&nbsp;       ball\_by\_ball\_data\[is\_wicket] = TRUE() \&\&

&nbsp;       NOT ball\_by\_ball\_data\[wicket\_kind] IN {

&nbsp;           "run out", 

&nbsp;           "retired hurt", 

&nbsp;           "retired out", 

&nbsp;           "obstructing the field"

&nbsp;       }

&nbsp;   )

-- Step 2: Summarize bowler and team, count wickets per bowler

VAR WicketSummary =

&nbsp;   ADDCOLUMNS(

&nbsp;       SUMMARIZE(

&nbsp;           SeasonWickets,

&nbsp;           ball\_by\_ball\_data\[bowler],

&nbsp;           ball\_by\_ball\_data\[team\_bowling]

&nbsp;       ),

&nbsp;       "WicketCount",

&nbsp;       COUNTROWS(

&nbsp;           FILTER(

&nbsp;               SeasonWickets,

&nbsp;               ball\_by\_ball\_data\[bowler] = EARLIER(ball\_by\_ball\_data\[bowler])





### Q15: Purple Cap winner Player Image (dynamically rendered)



Dax formula: - 



PurpleCapImage = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

-- Step 1: Filter valid bowler wickets for the selected season

VAR SeasonWickets =

&nbsp;   FILTER(

&nbsp;       ball\_by\_ball\_data,

&nbsp;       RELATED(ipl\_matches\_data\[season]) = SelectedSeason \&\& 

&nbsp;       ball\_by\_ball\_data\[is\_wicket] = TRUE() \&\& 

&nbsp;       NOT ball\_by\_ball\_data\[wicket\_kind] IN {

&nbsp;            "run out",

&nbsp;           "retired hurt",

&nbsp;           "retired out",

&nbsp;           "obstructing the field"

&nbsp;       }

&nbsp;   )

-- Step 2: Summarize bowler-wise wicket count

VAR WicketSummary =

&nbsp;   SUMMARIZE(

&nbsp;       SeasonWickets,

&nbsp;       ball\_by\_ball\_data\[bowler],

&nbsp;       "WicketCount", COUNTROWS(

&nbsp;            FILTER(

&nbsp;               SeasonWickets,

&nbsp;               ball\_by\_ball\_data\[bowler] = EARLIER(ball\_by\_ball\_data\[bowler])

&nbsp;           )





## Total Fours in Season





### Q16: Player with Most Fours in the Season



Dax formula: - 



TOP Fours Player Name = 

VAR SeletedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonFours = FILTER(ball\_by\_ball\_data, RELATED(ipl\_matches\_data\[season]) = SeletedSeason \&\&

&nbsp;                       ball\_by\_ball\_data\[batter\_runs] = 4 )

VAR FourSummary = SUMMARIZE(SeasonFours,ball\_by\_ball\_data\[batter], "FoursCount",

&nbsp;                           COUNTROWS(FILTER(SeasonFours, ball\_by\_ball\_data\[batter] = EARLIER(ball\_by\_ball\_data\[batter]))))

VAR MaxFours = MAXX(FourSummary, \[FoursCount])

VAR TopFoursPlayer = CALCULATETABLE(VALUES(ball\_by\_ball\_data\[batter]),

&nbsp;                                   FILTER(FourSummary, \[FoursCount] = MaxFours))

RETURN MAXX(TopFoursPlayer, ball\_by\_ball\_data\[batter])



### Q17: Total Fours by Player



Dax formula: - 



TOP Fours Count = 

VAR SeletedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonFours = FILTER(ball\_by\_ball\_data, RELATED(ipl\_matches\_data\[season]) = SeletedSeason \&\&

&nbsp;                       ball\_by\_ball\_data\[batter\_runs] = 4 )

VAR FourSummary = SUMMARIZE(SeasonFours,ball\_by\_ball\_data\[batter], "FoursCount",

&nbsp;                           COUNTROWS(FILTER(SeasonFours, ball\_by\_ball\_data\[batter] = EARLIER(ball\_by\_ball\_data\[batter]))))

VAR MaxFours = MAXX(FourSummary, \[FoursCount])

RETURN MaxFours



### Q18: Team Name the player represented



Dax formula: - 



TOP Fours Player Team Name = 

VAR SeletedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonFours = FILTER(ball\_by\_ball\_data, RELATED(ipl\_matches\_data\[season]) = SeletedSeason \&\&

&nbsp;                       ball\_by\_ball\_data\[batter\_runs] = 4 )

VAR FourSummary = SUMMARIZE(SeasonFours,ball\_by\_ball\_data\[batter], "FoursCount",

&nbsp;                           COUNTROWS(FILTER(SeasonFours, ball\_by\_ball\_data\[batter] = EARLIER(ball\_by\_ball\_data\[batter]))))

VAR MaxFours = MAXX(FourSummary, \[FoursCount])

VAR TopFoursPlayer = CALCULATETABLE(VALUES(ball\_by\_ball\_data\[batter]),

&nbsp;                                   FILTER(FourSummary, \[FoursCount] = MaxFours))

VAR BatterTeam =

&nbsp;               CALCULATE(MAX(ball\_by\_ball\_data\[team\_batting]),

&nbsp;               FILTER(SeasonFours, ball\_by\_ball\_data\[batter] = MAXX(TopFoursPlayer, ball\_by\_ball\_data\[batter])))

RETURN BatterTeam



### Q19: Player Image (dynamically rendered)



Dax formula: - 



TOP Fours Player Image = 

VAR SeletedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonFours = FILTER(ball\_by\_ball\_data, RELATED(ipl\_matches\_data\[season]) = SeletedSeason \&\&

&nbsp;                       ball\_by\_ball\_data\[batter\_runs] = 4 )

VAR FourSummary = SUMMARIZE(SeasonFours,ball\_by\_ball\_data\[batter], "FoursCount",

&nbsp;                           COUNTROWS(FILTER(SeasonFours, ball\_by\_ball\_data\[batter] = EARLIER(ball\_by\_ball\_data\[batter]))))

VAR MaxFours = MAXX(FourSummary, \[FoursCount])

VAR TopFoursPlayer = CALCULATETABLE(VALUES(ball\_by\_ball\_data\[batter]),

&nbsp;                                   FILTER(FourSummary, \[FoursCount] = MaxFours))

RETURN 

&nbsp;   LOOKUPVALUE(

&nbsp;       'players-data-updated'\[player\_image],

&nbsp;       'players-data-updated'\[player\_name], MAXX(TopFoursPlayer, ball\_by\_ball\_data\[batter])

&nbsp;   )



## Total Sixes in Season



### Q20: Player with Most Sixes in the Season



Dax formula: - 



TOP Sixs Player Name = 

VAR SeletedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonSixs = FILTER(ball\_by\_ball\_data, RELATED(ipl\_matches\_data\[season]) = SeletedSeason \&\&

&nbsp;                       ball\_by\_ball\_data\[batter\_runs] = 6 )

VAR SixSummary = SUMMARIZE(SeasonSixs,ball\_by\_ball\_data\[batter], "SixsCount",

&nbsp;                           COUNTROWS(FILTER(SeasonSixs, ball\_by\_ball\_data\[batter] = EARLIER(ball\_by\_ball\_data\[batter]))))

VAR MaxSixs = MAXX(SixSummary, \[SixsCount])

VAR TopSixsPlayer = CALCULATETABLE(VALUES(ball\_by\_ball\_data\[batter]),

&nbsp;                                   FILTER(SixSummary, \[SixsCount] = MaxSixs))

RETURN MAXX(TopSixsPlayer, ball\_by\_ball\_data\[batter])



### Q21: Total Sixes by Player



Dax formula: - 



TOP Sixs Count = 

VAR SeletedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonSixs = FILTER(ball\_by\_ball\_data, RELATED(ipl\_matches\_data\[season]) = SeletedSeason \&\&

&nbsp;                       ball\_by\_ball\_data\[batter\_runs] = 6 )

VAR SixSummary = SUMMARIZE(SeasonSixs,ball\_by\_ball\_data\[batter], "SixsCount",

&nbsp;                           COUNTROWS(FILTER(SeasonSixs, ball\_by\_ball\_data\[batter] = EARLIER(ball\_by\_ball\_data\[batter]))))

VAR MaxSixs = MAXX(SixSummary, \[SixsCount])

RETURN MaxSixs



### Q22: Team Name the player represented



Dax formula: - 



TOP Sixs Player Team Name = 

VAR SeletedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonSixs = FILTER(ball\_by\_ball\_data, RELATED(ipl\_matches\_data\[season]) = SeletedSeason \&\&

&nbsp;                       ball\_by\_ball\_data\[batter\_runs] = 6 )

VAR SixSummary = SUMMARIZE(SeasonSixs,ball\_by\_ball\_data\[batter], "SixsCount",

&nbsp;                           COUNTROWS(FILTER(SeasonSixs, ball\_by\_ball\_data\[batter] = EARLIER(ball\_by\_ball\_data\[batter]))))

VAR MaxSixs = MAXX(SixSummary, \[SixsCount])

VAR TopSixsPlayer = CALCULATETABLE(VALUES(ball\_by\_ball\_data\[batter]),

&nbsp;                                   FILTER(SixSummary, \[SixsCount] = MaxSixs))

VAR BatterTeam =

&nbsp;               CALCULATE(MAX(ball\_by\_ball\_data\[team\_batting]),

&nbsp;               FILTER(SeasonSixs, ball\_by\_ball\_data\[batter] = MAXX(TopSixsPlayer, ball\_by\_ball\_data\[batter])))

RETURN BatterTeam



### Q23: Player Image (dynamically rendered)



Dax formula: - 



TOP Sixs Player Image = 

VAR SeletedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR SeasonSixs = FILTER(ball\_by\_ball\_data, RELATED(ipl\_matches\_data\[season]) = SeletedSeason \&\&

&nbsp;                       ball\_by\_ball\_data\[batter\_runs] = 6 )

VAR SixSummary = SUMMARIZE(SeasonSixs,ball\_by\_ball\_data\[batter], "SixsCount",

&nbsp;                           COUNTROWS(FILTER(SeasonSixs, ball\_by\_ball\_data\[batter] = EARLIER(ball\_by\_ball\_data\[batter]))))

VAR MaxSixs = MAXX(SixSummary, \[SixsCount])

VAR TopSixsPlayer = CALCULATETABLE(VALUES(ball\_by\_ball\_data\[batter]),

&nbsp;                                   FILTER(SixSummary, \[SixsCount] = MaxSixs))

RETURN 

&nbsp;   LOOKUPVALUE(

&nbsp;       'players-data-updated'\[player\_image],

&nbsp;       'players-data-updated'\[player\_name], MAXX(TopSixsPlayer, ball\_by\_ball\_data\[batter])

&nbsp;   )



## Points Table



### Q24: Matches Played: Total matches played by the team in the selected season



Dax formula: - 



Matches Played = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR Team1Matches = 

&nbsp;                   CALCULATE(COUNTROWS(ipl\_matches\_data),

&nbsp;                   USERELATIONSHIP(ipl\_matches\_data\[team1], teams\_data\[team\_name]),

&nbsp;                   ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                   ipl\_matches\_data\[match\_type] = "T20")

VAR Team2Matches = 

&nbsp;                   CALCULATE(COUNTROWS(ipl\_matches\_data),

&nbsp;                   USERELATIONSHIP(ipl\_matches\_data\[team2], teams\_data\[team\_name]),

&nbsp;                   ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                   ipl\_matches\_data\[match\_type] = "T20")

RETURN Team1Matches + Team2Matches



### Q25: Won: Number of matches won



Dax formula: - 



Matches Won = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR CurrentTeam = SELECTEDVALUE(teams\_data\[team\_name])

RETURN

CALCULATE(COUNTROWS(ipl\_matches\_data),

&nbsp;         ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;         ipl\_matches\_data\[match\_winner] = CurrentTeam,

&nbsp;         ipl\_matches\_data\[match\_type] = "T20")





### Q26: Lost: Number of matches lost



Dax formula: - 



Matches Lost = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR Team1LostMatches = 

&nbsp;                   CALCULATE(COUNTROWS(ipl\_matches\_data),

&nbsp;                   USERELATIONSHIP(ipl\_matches\_data\[team1], teams\_data\[team\_name]),

&nbsp;                   ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                   ipl\_matches\_data\[match\_type] = "T20",

&nbsp;                   NOT ISBLANK(ipl\_matches\_data\[match\_winner]),

&nbsp;                   ipl\_matches\_data\[match\_winner] <> ipl\_matches\_data\[team1]

&nbsp;                   )

VAR Team2LostMatches = 

&nbsp;                   CALCULATE(COUNTROWS(ipl\_matches\_data),

&nbsp;                   USERELATIONSHIP(ipl\_matches\_data\[team2], teams\_data\[team\_name]),

&nbsp;                   ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                   ipl\_matches\_data\[match\_type] = "T20",

&nbsp;                   NOT ISBLANK(ipl\_matches\_data\[match\_winner]),

&nbsp;                   ipl\_matches\_data\[match\_winner] <> ipl\_matches\_data\[team2]

&nbsp;                   )

RETURN Team1LostMatches + Team2LostMatches



### Q27: No Result: Matches with no result (e.g., rain-affected)



Dax formula: - 



No Result Played = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR Team1Matches = 

&nbsp;                   CALCULATE(COUNTROWS(ipl\_matches\_data),

&nbsp;                   USERELATIONSHIP(ipl\_matches\_data\[team1], teams\_data\[team\_name]),

&nbsp;                   ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                   ipl\_matches\_data\[match\_type] = "T20",

&nbsp;                   ipl\_matches\_data\[result] = "no result")

VAR Team2Matches = 

&nbsp;                   CALCULATE(COUNTROWS(ipl\_matches\_data),

&nbsp;                   USERELATIONSHIP(ipl\_matches\_data\[team2], teams\_data\[team\_name]),

&nbsp;                   ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                   ipl\_matches\_data\[match\_type] = "T20",

&nbsp;                   ipl\_matches\_data\[result] = "no result")

RETURN Team1Matches + Team2Matches



### Q28: Tie: Matches that ended in a tie



Dax formula: - 



Tie Played = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])

VAR Team1Matches = 

&nbsp;                   CALCULATE(COUNTROWS(ipl\_matches\_data),

&nbsp;                   USERELATIONSHIP(ipl\_matches\_data\[team1], teams\_data\[team\_name]),

&nbsp;                   ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                   ipl\_matches\_data\[match\_type] = "T20",

&nbsp;                   ipl\_matches\_data\[result] = "tie")

VAR Team2Matches = 

&nbsp;                   CALCULATE(COUNTROWS(ipl\_matches\_data),

&nbsp;                   USERELATIONSHIP(ipl\_matches\_data\[team2], teams\_data\[team\_name]),

&nbsp;                   ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                   ipl\_matches\_data\[match\_type] = "T20",

&nbsp;                   ipl\_matches\_data\[result] = "tie")

RETURN Team1Matches + Team2Matches





### Q29: Total Points: Calculated using standard IPL points rules:Total Points 



Dax formula: - 



Total Points = 

VAR Win = \[Matches Won]

VAR NR = \[No Result Played]

RETURN (Win\*2) + NR



## The Winner Team of the selected season along with its dynamically displayed team logo



### Q30: Season Winner Team Name



Dax formula: - 



Season Winner = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])       --2025

VAR FinalMatchDate = CALCULATE(MAX(ipl\_matches\_data\[match\_date]),

&nbsp;                               ipl\_matches\_data\[season] = SelectedSeason)    --Max Date = 3 Jun

VAR FinalMatchWinner = CALCULATE(MAX(ipl\_matches\_data\[match\_winner]),       --Winner Team

&nbsp;                               ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                               ipl\_matches\_data\[match\_date] = FinalMatchDate)

RETURN FinalMatchWinner



### Q31: Season Winner Team Logo



Dax formula: - 



Season Winner Logo = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])       --2025

VAR FinalMatchDate = CALCULATE(MAX(ipl\_matches\_data\[match\_date]),

&nbsp;                               ipl\_matches\_data\[season] = SelectedSeason)    --Max Date = 3 Jun

VAR FinalMatchWinner = CALCULATE(MAX(ipl\_matches\_data\[match\_winner]),       --Winner Team

&nbsp;                               ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                               ipl\_matches\_data\[match\_date] = FinalMatchDate)

RETURN

LOOKUPVALUE(

&nbsp;   teams\_data\[image\_url],

&nbsp;   teams\_data\[team\_name], FinalMatchWinner)





## The Runner-Up Team of the selected seasen along with its dynamically displayed team logo



### 32: Runner-Up Team Name



Dax formula: - 



Runner Up = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])       --2025

VAR FinalMatchDate = CALCULATE(MAX(ipl\_matches\_data\[match\_date]),

&nbsp;                               ipl\_matches\_data\[season] = SelectedSeason)    --Max Date = 3 Jun

VAR FinalMatchWinner = CALCULATE(MAX(ipl\_matches\_data\[match\_winner]),       --Winner Team

&nbsp;                               ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                               ipl\_matches\_data\[match\_date] = FinalMatchDate)

VAR Team1 = CALCULATE(MAX(ipl\_matches\_data\[team1]), 

&nbsp;                                ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                                ipl\_matches\_data\[match\_date] = FinalMatchDate)

var Team2 = CALCULATE(MAX(ipl\_matches\_data\[team2]),

&nbsp;                                ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                                ipl\_matches\_data\[match\_date] = FinalMatchDate)

RETURN

IF(FinalMatchWinner=Team1, Team2,Team1)



### 33: Runner-Up Team Logo



Dax formula: - 



Runner Up Team Logo = 

VAR SelectedSeason = SELECTEDVALUE(ipl\_matches\_data\[season])       --2025

VAR FinalMatchDate = CALCULATE(MAX(ipl\_matches\_data\[match\_date]),

&nbsp;                               ipl\_matches\_data\[season] = SelectedSeason)    --Max Date = 3 Jun

VAR FinalMatchWinner = CALCULATE(MAX(ipl\_matches\_data\[match\_winner]),       --Winner Team

&nbsp;                               ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                               ipl\_matches\_data\[match\_date] = FinalMatchDate)

VAR Team1 = CALCULATE(MAX(ipl\_matches\_data\[team1]), 

&nbsp;                                ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                                ipl\_matches\_data\[match\_date] = FinalMatchDate)

var Team2 = CALCULATE(MAX(ipl\_matches\_data\[team2]),

&nbsp;                                ipl\_matches\_data\[season] = SelectedSeason,

&nbsp;                                ipl\_matches\_data\[match\_date] = FinalMatchDate)

RETURN

LOOKUPVALUE(teams\_data\[image\_url], teams\_data\[team\_name], \[Runner Up])




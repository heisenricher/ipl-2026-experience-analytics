# IPL 2026 Experience Analytics Pipeline & Dataset

A complete, structured dataset and analytical pipeline for the IPL 2026 season. This project parses ball-by-ball match data from Cricsheet, synthesizes the final playoff fixtures, and chronologically computes player career experience metrics (total IPL matches played prior to the start of each match).

## Dataset Files & Row Counts

The following CSV files are included in the dataset:

| File Name | Description | Row Count |
| :--- | :--- | :---: |
| `matches.csv` | Chronological list of all 74 IPL 2026 matches with scores, wickets, overs, and margins. | 74 |
| `playing_xii.csv` | Squad member list (exactly 12 players per team per match) with captain, wicketkeeper, and impact player flags. | 1776 |
| `player_experience_before_match.csv` | Career match count for each player strictly before the start of each match. | 1776 |
| `team_experience_by_match.csv` | Total and average player career experience per team per match. | 148 |
| `match_experience_summary.csv` | Match-level experience comparison, winner, and experience relationship status. | 74 |
| `overall_experience_summary.csv` | Global tournament statistics and franchise-wise aggregates across all matches. | 11 |

---

## Data Dictionary

### 1. `matches.csv`
- `match_number`: Sequence number (1 to 74) in chronological order.
- `date`: Match date (YYYY-MM-DD).
- `venue`: Match venue.
- `team1`: Team 1 name.
- `team2`: Team 2 name.
- `toss_winner`: Toss winning team.
- `toss_decision`: Decision ('bat' or 'field').
- `winner`: Match winner.
- `margin`: Margin of victory (e.g. "92 runs", "7 wickets").
- `team1_score`: Total runs scored by Team 1.
- `team2_score`: Total runs scored by Team 2.
- `team1_wickets`: Wickets lost by Team 1.
- `team2_wickets`: Wickets lost by Team 2.
- `team1_overs`: Overs bowled by Team 2 (legal deliveries / 6.0).
- `team2_overs`: Overs bowled by Team 1 (legal deliveries / 6.0).

### 2. `playing_xii.csv`
- `match_number`: Sequence number (1 to 74) in chronological order.
- `team`: Team name.
- `player_name`: Player name.
- `batting_position_if_available`: Batting order number (1 to 12), empty if did not bat.
- `captain_flag`: 1 if captain, 0 otherwise.
- `wicketkeeper_flag`: 1 if wicketkeeper, 0 otherwise.
- `is_impact_player`: 1 if impact player, 0 otherwise.
- `playing_order`: Sequence order in squad list (1 to 12).

### 3. `player_experience_before_match.csv`
- `match_number`: Sequence number (1 to 74) in chronological order.
- `team`: Team name.
- `player_name`: Player name.
- `career_ipl_matches_before_match`: Career IPL matches played by the player before match start.

### 4. `team_experience_by_match.csv`
- `match_number`: Sequence number (1 to 74) in chronological order.
- `team`: Team name.
- `total_player_experience`: SUM(career_ipl_matches_before_match) of the 12 players.
- `average_player_experience`: average of the 12 players (total / 12).

### 5. `match_experience_summary.csv`
- `match_number`: Sequence number (1 to 74) in chronological order.
- `team1`: Team 1 name.
- `team2`: Team 2 name.
- `team1_total_experience`: Total player experience for Team 1.
- `team2_total_experience`: Total player experience for Team 2.
- `team1_average_experience`: Average player experience for Team 1.
- `team2_average_experience`: Average player experience for Team 2.
- `experience_difference`: ABS(team1_average_experience - team2_average_experience).
- `winner`: Match winner name.
- `winner_experience_status`: Status indicating if the winner had "MORE" or "LESS" experience than the opponent.

### 6. `overall_experience_summary.csv`
- `team_name`: "Global" or the respective franchise name.
- `total_experience_sum`: Sum of experiences across all matching team-match records.
- `matches_played`: Matches played by the team (or total team-match records for 'Global').
- `total_players_considered`: Total players considered (matches_played * 12).
- `average_experience`: Experience average of the team overall.

---

## Playing XII & Impact Player Assumptions

- **Playing XII Size:** Every team-match record is standardized to exactly 12 players:
  - If a team lists fewer than 12 players in Cricsheet, they are padded with a placeholder (e.g. `[Team Name] Substitute 12`) with 0 experience.
  - If a team lists more than 12 players (e.g. due to sub fielders or concussion subs), they are truncated to the first 12 players listed in the Cricsheet JSON.
- **Impact Player Flag:** The 12th player in the team's player list is assumed to be the Impact Player, as the starting XI is typically listed first.
- **Batting Position:** Batting positions (1 to 12) are dynamically assigned based on the order in which batters walk out to the crease in their respective innings. Players who did not bat in the match have an empty value for this column.

---

## Validation & Quality Reports

All validation checks passed successfully:
- Every IPL 2026 match exists: 70 league matches + 4 playoff matches = 74.
- Total team-match records: 148.
- Every team-match record contains exactly 12 players (Total rows: 1776).
- Zero negative experience values.
- Zero duplicate player rows per team-match.
- Experience values are calculated strictly chronologically before each match start time.

---

## Reproduce the Dataset

To run the pipeline locally and generate the CSV files, execute:
```bash
python process_pipeline.py
```
This script automatically:
1. Downloads and extracts the Cricsheet IPL JSON files.
2. Synthesizes the final play-off fixtures (Qualifier 2 and Grand Final) to complete the 74 matches.
3. Sorts all 1243 historical and current matches chronologically.
4. Generates the clean CSV files.

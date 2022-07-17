# NBA-Stats-and-Score
import json
from requests import get
from pprint import PrettyPrinter

BASE_URL = "https://data.nba.net"
ALL_JSON = "/prod/v1/today.json"

printer = PrettyPrinter()


def get_links():
    data = get(BASE_URL + ALL_JSON).json()
    # printer.pprint(data)

    # extracting the links dictionary:
    links = data["links"]
    # printer.pprint(links)
    # extracting currentScoreboard link from the "links" dictionary:
    return links


def get_scoreboard():
    scoreboard = get_links()["currentScoreboard"]
    games = get(BASE_URL + scoreboard).json()["games"]
    # printer.pprint(games.keys())  #keys() is only used for dictionaries & list objs doesn't have any attribute keys()
    for game in games:
        our_team = game["hTeam"]  # printer.pprint(game.keys())
        away_team = game["vTeam"]  # break
        clock = game["clock"]
        period = game["period"]

        print("---------------------------------------------------")
        print(f"{our_team['triCode']} vs {away_team['triCode']}")
        print(f"{our_team['score']} vs {away_team['score']}")
        print(f" {clock} - Period: {period['current']}")


def get_stats():
    stats = get_links()["leagueTeamStatsLeaders"]
    teams = get(BASE_URL + stats).json()["league"]["standard"]["regularSeason"][
        "teams"]  # printer.pprint(teams)#here teams is the list
    # printer.pprint(teams[0].keys())  # here teams is the list
    teams = list(filter(lambda x: x["name"] != "Team", teams))
    teams.sort(key=lambda x: x['ppg'][
        'rank'])  # also write it as: teams.sort(key=lambda x:int(x['ppg']['rank'])), now it will be print on the basis of integer values like of ppg and rank
    for i, team in enumerate(teams):
        name = team["name"]
        nickname = team["nickname"]
        ppg = team["ppg"]['avg']
        print(f"{i+1}.{name} - {nickname} - {ppg}")


get_stats()
# get_scoreboard()


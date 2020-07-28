## Dotcom scoreboards


What do we need:

[Basketball app catalogue entry](https://conf.willhillatlas.com/display/TRAD/Dot-com+Scoreboard+Basketball)


[Git Repo for BBall DotCom](https://git.nonprod.williamhill.plc/trading_development/sports/dotcom-scoreboards)


Try and find XSD for tennis. Rob wants this but not essential. If there is another way that works Rob is happy :-)


1. Grab full state from MorphModelResponse

2. Filter for tennis, 

3. Transform and send to scoreboard via AMQ.
	
			{
			    "whId":"b34030fd-2a7e-4f43-b584-45e7b72040e3",
			    "correlationId":"correlationId",
			    "timestamp":1583826413589,
			    "category":"tennis",
			    "modelName":"CAERUS",
			    "sourceId":"00522b3b-ab27-49a5-9914-0a834817d3ab",
			    "modelResponse":{
			        "eventTradingStatus":"ACTIVE",
			        "eventTradingPhase":"IN_PLAY",
			        "metadata":{
			            "eventId":"b34030fd-2a7e-4f43-b584-45e7b72040e3",
			            "homeTeam":{
			                "name":"Andy Murray",
			                "score":{
			                    "currentGame":0,
			                    "sets":[
			                        2,
			                        6,
			                        0
			                    ]
			                },
			                "isServing":true,
			                "secondService":true
			            },
			            "awayTeam":{
			                "name":"Rafael Nadal",
			                "score":{
			                    "currentGame":30,
			                    "sets":[
			                        6,
			                        0,
			                        0
			                    ]
			                },
			                "isServing":false,
			                "secondService":false
			            },
			            "match":{
			                "service":{
			                    "aces":{
			                        "type":"ACES",
			                        "homeTeam":0,
			                        "awayTeam":0
			                    },
			                    "doubleFaults":{
			                        "type":"DOUBLE_FAULTS",
			                        "homeTeam":0,
			                        "awayTeam":0
			                    },
			                    "firstServePercentage":{
			                        "type":"FIRST_SERVE_PERCENTAGE",
			                        "homeTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        },
			                        "awayTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        }
			                    },
			                    "firstServicePointsWon":{
			                        "type":"FIRST_SERVICE_POINTS_WON",
			                        "homeTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        },
			                        "awayTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        }
			                    },
			                    "secondServePointsWon":{
			                        "type":"SECOND_SERVICE_POINTS_WON",
			                        "homeTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        },
			                        "awayTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        }
			                    },
			                    "breakPointsSaved":{
			                        "type":"BREAK_POINTS_SAVED",
			                        "homeTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        },
			                        "awayTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        }
			                    },
			                    "serviceGamesPlayed":{
			                        "type":"SERVICE_GAMES_PLAYED",
			                        "homeTeam":0,
			                        "awayTeam":0
			                    }
			                },
			                "return":{
			                    "firstReturnsWon":{
			                        "type":"FIRST_RETURNS_WON",
			                        "homeTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        },
			                        "awayTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        }
			                    },
			                    "secondReturnsWon":{
			                        "type":"SECOND_RETURNS_WON",
			                        "homeTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        },
			                        "awayTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        }
			                    },
			                    "breakPointsWon":{
			                        "type":"BREAK_POINTS_WON",
			                        "homeTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        },
			                        "awayTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        }
			                    },
			                    "returnGamesPlayed":{
			                        "type":"RETURN_GAMES_PLAYED",
			                        "homeTeam":0,
			                        "awayTeam":0
			                    }
			                },
			                "points":{
			                    "totalServicePointsWon":{
			                        "type":"TOTAL_SERVICE_POINTS_WON",
			                        "homeTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        },
			                        "awayTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        }
			                    },
			                    "totalReturnPointsWon":{
			                        "type":"TOTAL_RETURN_POINTS_WON",
			                        "homeTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        },
			                        "awayTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        }
			                    },
			                    "totalPointsWon":{
			                        "type":"TOTAL_POINTS_WON",
			                        "homeTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        },
			                        "awayTeam":{
			                            "enumerator":1,
			                            "denominator":2
			                        }
			                    }
			                }
			            },
			            "sets":[
			                {
			                    "gameHistory":[
			                        {
			                            "homeTeam":"G",
			                            "awayTeam":40
			                        },
			                        {
			                            "homeTeam":"G",
			                            "awayTeam":40
			                        },
			                        {
			                            "homeTeam":"G",
			                            "awayTeam":40
			                        },
			                        {
			                            "homeTeam":"G",
			                            "awayTeam":40
			                        },
			                        {
			                            "homeTeam":"G",
			                            "awayTeam":40
			                        },
			                        {
			                            "homeTeam":"G",
			                            "awayTeam":40
			                        }
			                    ],
			                    "games":{
			                        "homeTeam":6,
			                        "awayTeam":0
			                    },
			                    "service":{
			                        "aces":{
			                            "type":"ACES",
			                            "homeTeam":0,
			                            "awayTeam":0
			                        },
			                        "doubleFaults":{
			                            "type":"DOUBLE_FAULTS",
			                            "homeTeam":0,
			                            "awayTeam":0
			                        },
			                        "firstServePercentage":{
			                            "type":"FIRST_SERVE_PERCENTAGE",
			                            "homeTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            },
			                            "awayTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            }
			                        },
			                        "firstServicePointsWon":{
			                            "type":"FIRST_SERVICE_POINTS_WON",
			                            "homeTeam":{
			                                "enumerator":1,
			                                "denominator":8
			                            },
			                            "awayTeam":{
			                                "enumerator":2,
			                                "denominator":6
			                            }
			                        },
			                        "secondServePointsWon":{
			                            "type":"SECOND_SERVICE_POINTS_WON",
			                            "homeTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            },
			                            "awayTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            }
			                        },
			                        "breakPointsSaved":{
			                            "type":"BREAK_POINTS_SAVED",
			                            "homeTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            },
			                            "awayTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            }
			                        },
			                        "serviceGamesPlayed":{
			                            "type":"SERVICE_GAMES_PLAYED",
			                            "homeTeam":0,
			                            "awayTeam":0
			                        }
			                    },
			                    "return":{
			                        "firstReturnsWon":{
			                            "type":"FIRST_RETURNS_WON",
			                            "homeTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            },
			                            "awayTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            }
			                        },
			                        "secondReturnsWon":{
			                            "type":"SECOND_RETURNS_WON",
			                            "homeTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            },
			                            "awayTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            }
			                        },
			                        "breakPointsWon":{
			                            "type":"BREAK_POINTS_WON",
			                            "homeTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            },
			                            "awayTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            }
			                        },
			                        "returnGamesPlayed":{
			                            "type":"RETURN_GAMES_PLAYED",
			                            "homeTeam":0,
			                            "awayTeam":0
			                        }
			                    },
			                    "points":{
			                        "totalServicePointsWon":{
			                            "type":"TOTAL_SERVICE_POINTS_WON",
			                            "homeTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            },
			                            "awayTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            }
			                        },
			                        "totalReturnPointsWon":{
			                            "type":"TOTAL_RETURN_POINTS_WON",
			                            "homeTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            },
			                            "awayTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            }
			                        },
			                        "totalPointsWon":{
			                            "type":"TOTAL_POINTS_WON",
			                            "homeTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            },
			                            "awayTeam":{
			                                "enumerator":1,
			                                "denominator":2
			                            }
			                        }
			                    }
			                }
			            ]
			        }
			    }
			}
		 



What to do for transformation:

	
	if(response.getCategory().equals("tennis") && response.getModelResponse().getEventTradingStatus().equals("IN_PLAY")){
	
	    transformationService.transform(stateMessage)
	    
	}



To transform first message:


## ns4:tennisMessage 

`timestamp = stateMessage.getTimestamp() returned: 1583826413589 required: 2020-03-01T00:36:06.290Z`

## ns3:playersOnCourt

Bet Radar Info it seems I don't have acess to.

`src = Dont know where this comes from. Can hardcode for now but as soon as we get another feed provider we need to know - feed info not in model response. I need to check with Laur if this is required too.`

`timestamp = has later timestamp, where does this come from?`

`matchRef = where does this come from?`

`id = where does this come from?` 

## Match

`phase = stateMessage.getModelResponse().getEventTradingStatus() returns IN_PLAY required = inPlay` 
Can use something like: `If previous char is not "_" char.toLowerCase();`

`name = stateMessage.getModelResponse().getMetadata().getHomeTeam().getName() + "vs" + stateMessage.getModelResponse().getMetadata().getHomeTeam().getName()`

`setsToBePlayed = stateMessage.getModelResponse().getMetadata().getHomeTeam().getScore().getSets().length`

`id = This is same as matchRef above and therefore is probably a BR ID that I don't have but maybe a Caerus/OB Id.... Look at BBall`

## competitorList

### competitor

`id = "p1" This can be hardcoded if we always use homeTeam for "p1" and awayTeam for "p2"`

##### participantDetails

`name = stateMessage.getModelResponse().getMetadata().getHomeTeam().getName() or stateMessage.getModelResponse().getMetadata().getAwayTeam().getName()`

`id = "p1" This can be hardcoded if we always use homeTeam for "p1" and awayTeam for "p2"` - Same id as above.


## counterScore

### 2 x instances of counterCompetitorScore

All of which seems can be hardcoded.

`score = 0 - Never seen this particular score take any other value so can be hardcoded by the look of things.`

`ns2:competitorRef = "p1" or "p2"`

`id = "BOGUS_ID"`


## statistics

Aggregated stats for the whole of this match.

### competitorStatistics

`ns2:competitorRef "p1" or "p2"` Need to work out how this works....

`id = "BOGUS_ID"`

##### numberOfAces 

`value = stateMessage.getModelResponse().getMetadata().getMatch().getService().getAces().getHomeTeam() ..... and AwayTeam()`


This folows for:

##### numberOfDoubleFaults

`stateMessage.getModelResponse().getMetadata().getMatch().getService().getDoubleFaults().getHomeTeam() ... and AwayTeam()`


**THE BELOW IS AN UNKNOWN AS TO WETHER IT WILL WORK**

Service points is shown as a percentage so assuming it does with these values what I expect were good ..... but I can not be sure at the minute.

##### numberOfServicePoints

`stateMessage.getModelResponse().getMetadata().getMatch().getPoints().getTotalServicePointsWon().getHomeTeam().getDenominator()`

##### numberOfServicePointsWon 

`stateMessage.getModelResponse().getMetadata().getMatch().getPoints().getTotalServicePointsWon().getHomeTeam().getEnumerator()`


##### numberOfBreakPoints
A break point occurs when two conditions are met:

1. One player is one point away from a win
2. Their opponent is serving

These are not all zeros in examples but I do not get this count in MMR - I can calculate probably....

##### numberOfBreakPointsWon
`stateMessage.getModelResponse().getMetadata().getMatch().getReturn().getBreakPointsWon().getHomeTeam().getDenominator()`

##### totalPoints
`stateMessage.getModelResponse().getMetadata().getMatch().getPoints().getTotalPointsWon().getHomeTeam().getDenominator()`

##### totalPointsWon

`stateMessage.getModelResponse().getMetadata().getMatch().getPoints().getTotalPointsWon().getHomeTeam().getEnumerator()`

## Set

### counterScore - Repeat of above except.

getSets(). which returns a list of set objects.



### statistics - Repeat of above except.


The Below all seem to be ignored in current implementation unless I've never seen a match where this has happened...... Also doesn't show on scoreboards. Can we stop sending if ignored so it does not clutter up data.

numberOfGamePoints
numberOfGamePointsSaved

numberOfSetPoints
numberOfSetPointsSaved

numberOfMatchPoints
numberOfMatchPointsSaved

### game

#### Server

#### gameScore


### From Whit-A code

This is where to start for the Oxi Com service:


https://git.nonprod.williamhill.plc/trading_development/legacy/caerus-sports/blob/master/core/trading-services/commentary-service/commentary-core/src/main/java/com/williamhill/caerus/commentary/service/TennisCommentaryService.java


### What I need that I dont have:


### Game State

This is the stuff that I think Dan seemed happy to change/supply:

1. The total points that have been played this match. - Currently just get a fraction of the total points won for each player so I'd need to keep track of the toal points if this isn't received as impossible to calculate from previous games due to unknown numbers of advantages.

2. Who was serving in each game of the game history - As I'm wanting to do a full state update everytime this information will not exist for previous games unless I cache/store it. If this is ALWAYS strictly alternating i could calculate from the first server in each match. and then not required?

3. Also - does the set array @ `.modelResponse.metadata.awayTeam.sets` have size == numberOfSetsToBePlayedInThisMatch. I've been assuming this so far but be good to know now, otherwise I need that too.

4. Tiebreaks? Not really something I need more of - but could do with knowing what will show in the game state for a tiebreak, I send this after 12 games in the game state which simply seems to have acknowledged that there was a tiebreak, who won and how many games it took to break the tie.

            <tiebreak id="BOGUS_ID">
                <winner ns2:competitorRef="p2"/>
                <counterScore>
                    <counterCompetitorScore score="6" ns2:competitorRef="p1" id="BOGUS_ID"/>
                    <counterCompetitorScore score="8" ns2:competitorRef="p2" id="BOGUS_ID"/>
                </counterScore>
            </tiebreak>


### Incidents

The stuff that I believe dan doesn't really want to have anyhting to do with - Potentially we don't need this if we can derive the information required form a single WH incident type.

Bet Radar Info that is in the XML: Message Type(poitnWon, fault, playersOnCourt and playersWarmingUp), Message Details (previousPointProperties, nextPointProperties), Feed Provider Name(BetRadar - as Dan said hard to see this being important, but maybe it is a switch for translation of event types), MessageID. Maybe this can be substituted with some other game state that I can translate - ie: give me WH incidents I can translate back.Need to speak to Laur and see if he thinks they are even bothered about feed provider name/id etc.

Examples of the rewulting XML:

pointWon

    <ns3:pointWon ns2:competitorRef="p2" source="BetRadar" timestamp="2020-03-01T02:10:12.934Z" matchRef="16884762" id="112663869">
        <ns3:previousPointProperties isMatchPointWon="true" isSetPointWon="true" isGamePointWon="true" isTiebreakWon="true"/>
        <ns3:previousPointProperties isMatchPointWon="true" isSetPointWon="true" isGamePointWon="true" isTiebreakWon="true"/>
    </ns3:pointWon>
	    
fault

	<ns3:fault ns2:competitorRef="p2" source="BetRadar" timestamp="2020-03-01T13:05:51.057Z" matchRef="16884755" id="112671132"/>

playersOnCourt

	<ns3:playersOnCourt source="BetRadar" timestamp="2020-03-01T00:36:06.693Z" matchRef="16884762" id="112663262"/>

playersWarmingUp

	<ns3:playersWarmingUp source="BetRadar" timestamp="2020-03-01T00:36:06.979Z" matchRef="16884762" id="112663264"/>


- I don't know why these messages are needed but its what's in the XML, therefore working on the basis that we just need to send the same as before we need them. 
- Is it worth trying to hit the scoreboards in PP with XML to see what they are willing to live without?


I think I'm going to struggle to say this list is complete wihtout working out how the rest of the app works, as I've only got to the first message type so there maybe more work needed yet as the unknowns come out.



### What's left to think about:

   How do I use this lot to control the game.....

	<ns3:previousPointProperties isMatchPointWon="true" isSetPointWon="true" isGamePointWon="true" isBreakPointWon="true" isDoubleFault="true" isTiebreakWon="true" isAce="true"/>
	<ns3:previousPointProperties isAce="true"/>
	
	<ns3:nextPointProperties isMatchPoint="true" isSetPoint="true" isGamePoint="true" isBreakPoint="true" isTiebreak="true"/>
	<ns3:nextPointProperties isTiebreak="true"/>
	
	<ns3:fault ns2:competitorRef="p2" source="BetRadar" timestamp="2020-03-01T13:05:51.057Z" matchRef="16884755" id="112671132"/>


### BetRadar/EnetPulse - For future reference.

    <ns3:playersWarmingUp source="BetRadar" timestamp="2020-03-01T12:46:49.657Z" matchRef="16884755" id="112670840"/>
    <match phase="inPlay" name="Jack Draper vs Igor Sijsling" setsToBePlayed="3" id="16884755">


    <ns3:playersWarmingUp source="Enetpulse" timestamp="2020-03-01T13:02:30.829Z" matchRef="16890827" id="112671088"/>
    <match phase="preMatch" name="Irina Bara vs Isabella Shinikova" setsToBePlayed="3" id="16890827">
    


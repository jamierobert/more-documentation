## JOLT


###### High Level View
When incoming messages come in from the feed provider the message ids parsed to get the:


- Feed Provider
- The message category - As given by the provider(obviously !)
- The messgae type - as above.

We then construct the key from this information and use this key to retrieve the correct Jolt mapping from the Cache. If the message type and category are defined by int and String use the int for matching for performance.


Once we have the mapping we perform the Transform.

###### Transform Overview

Jolt transforms have to be performed in order, the next transformatition is reliant on the previous transforamtion being performed - Therefore when debugging it is important to Workout where your up to - generate the input accordingly then isolate you step - Like stepping over in the IDE.


We also need to delete the outer { and the joltspec attribute before using WH Transformations in the [Jolt REPL] (https://jolt-demo.appspot.com/#inception)

Mapping are reliant on brackets and white space doesn't matter - in fact mapopings are minified before been put on the Kafka Topic.

We can comment out in JOLT same as Java `/* */`

##Jolt Operators

A list of all the operators and there meaning on each side:

`*` - Works as a wildcard and is only available on the left hand side.
`&` -


| Operator | Left                         | Right                        |
|----------|------------------------------|------------------------------|
|          | What we have (Input)         | Where we want to put it      |
| #        | Use String literal           |                              |
| &        |                              | Pull individual + traverese  |
| @        |                              | Pull whole chunks + traverse |
| $        | Pull whole chunks + traverse |                              |
| *        | Wild card                    |                              |


##Jolt Operations

A jolt mapping will always specify an `operation` and a `spec`. The `operation` is what it is doing the `spec` is how it is done.

Within a mapping we always have a left and right side - They correspond to what we have and where we want it to go.


##### Code Injection

- `operation:fully.qualified.class.name` - Signifies java code called by a Jolt mapping.

Below is an example call:

		{
		      "operation": "com.williamhill.trading.platform.transform.convert.ConvertTransform",
		      "spec": {
		        "INGRESS_TIMESTAMP": "timenow"
		      }


##### Default

The default operation adds a default value wherever specified:

	{
	      "operation": "default",
	      "spec": {
	        "payload": {
	          "side": "none",
	          "extrainfo": "dummy"
	        }
	      }
	    }

The above walks down the tree looking for the `side` and `extrainfo` attributes - if they do exist then this does nothing - If the attribute does not exist it will add it so:

	{
	  "headers": {
	    "correlationId": "1769698c-ec9e-442e-8227-4cf3bcc597d2",
	    "incidentTypeId": "WilliamHillDeltaIncident",
	    "provider": "bet_radar_livescout",
	    "category": "Basketball",
	    "sourceId": "BR_14321745",
	    "matchId": "14321745"
	  },
	  "payload": {
	    "inningscore": []
	  }
	}
	

Would become:

	{
	  "headers" : {
	    "correlationId" : "1769698c-ec9e-442e-8227-4cf3bcc597d2",
	    "incidentTypeId" : "WilliamHillDeltaIncident",
	    "provider" : "bet_radar_livescout",
	    "category" : "Basketball",
	    "sourceId" : "BR_14321745",
	    "matchId" : "14321745"
	  },
	  "payload" : {
	    "inningscore" : [ ],
	    "side" : "none",
	    "extrainfo" : "dummy"
	  }
	}


##### modify-overwrite-beta / modify-default-beta

This operation allows us to perform some simple logic. Where the operation is pure - such that it disposes of the old value and returns a new value.

###### String operations

- toString
- subString
- leftPad
- rightPad
- trim
- split

###### Null Operations

- squashNulls
- recursivelySquashNulls

###### Math Operations

- toLong
- toDouble
- toInt
- sum
- subtract
- divide
- intsubtract, doublesubtract and longsubtract - I imagine these exist for other types too.


The below example converts stime, extrainfo, id and correctto to strings.

	{
	      "operation": "modify-overwrite-beta",
	      "spec": {
	        "payload": {
	          "stime": "=toString",
	          "extrainfo": "=toString",
	          "id": "=toString",
	          "correctedto": "=toString"
	        }
	      }
	 }
	 
Putting what we have covered so far together we can assign a number to extrainfo as the default value and convert it to a String:


	[
	  {
	    "operation": "default",
	    "spec": {
	      "payload": {
	        "side": "none",
	        "extrainfo": 1253647
	      }
	    }
	  },
	
	  {
	    "operation": "modify-overwrite-beta",
	    "spec": {
	      "payload": {
	        "extrainfo": "=toString"
	      }
	    }
	  }
	]

This will take input:

	{
	  "headers": {
	    "correlationId": "1769698c-ec9e-442e-8227-4cf3bcc597d2",
	    "incidentTypeId": "WilliamHillDeltaIncident",
	    "provider": "bet_radar_livescout",
	    "category": "Basketball",
	    "sourceId": "BR_14321745",
	    "matchId": "14321745"
	  },
	  "payload": {
	    "inningscore": []
	  }
	}

And output:

	{
	  "headers" : {
	    "correlationId" : "1769698c-ec9e-442e-8227-4cf3bcc597d2",
	    "incidentTypeId" : "WilliamHillDeltaIncident",
	    "provider" : "bet_radar_livescout",
	    "category" : "Basketball",
	    "sourceId" : "BR_14321745",
	    "matchId" : "14321745"
	  },
	  "payload" : {
	    "inningscore" : [ ],
	    "extrainfo" : "1253647",
	    "side" : "none"
	  }
	}


#### Shift

The shift command is the most commonly used command - by creating hierarchical data structures with our transformations we can use this to create an if statement effect. When we shift data - only data that is satisfied by a match is shifted. All other attributes are removed by default.

Simple Examples:




Input:

	{
	  "clients": {
	    "Acme": {
	      "clientId": "Acme",
	      "index": 1
	    },
	    "AXE": {
	      "clientId": "AXE",
	      "index": 0
	    }
	  },
	  "vip-clients": {
	    "WilliamHill": {
	      "clientId": "WILLS",
	      "index": 2
	    }
	  }
	}

Mapping:

	[
	  {
	    "operation": "shift",
	    "spec": {
	      "clients": {
	        "*": "clients.&"
	      },
	      "vip-clients": {
	        "*": "clients.&"
	      }
	    }
	  }
	
	]


Output:

	{
	  "clients" : {
	    "Acme" : {
	      "clientId" : "ACME",
	      "index" : 1
	    },
	    "AXE" : {
	      "clientId" : "AXE",
	      "index" : 0
	    },
	    "WilliamHill" : {
	      "clientId" : "WILLS",
	      "index" : 2
	    }
	  }
	}


Mapping:

	{
	  "operation": "shift",
	  "spec": {
	    "clients": {
	      "*": "clients-&"
	    }
	  }
	}

Output:

	{
	  "clients-Acme" : {
	    "clientId" : "ACME",
	    "index" : 1
	  },
	  "clients-AXE" : {
	    "clientId" : "AXE",
	    "index" : 0
	  },
	  "clients-WilliamHill" : {
	    "clientId" : "WILLS",
	    "index" : 2
	  }
	}

Mapping:

	  {
	    "operation": "shift",
	    "spec": {
	      "clients-*": {
	        "*": "clients-&"
	      }
	    }
	  }
Output:

	{
	  "clients-clientId" : [ "ACME", "AXE", "WILLS" ],
	  "clients-index" : [ 1, 0, 2 ]
	}


Full Mapping

	[
	  {
	    "operation": "shift",
	    "spec": {
	      "clients": {
	        "*": "clients.&"
	      },
	      "data": {
	        "*": "clients.data.&"
	      }
	    }
	  },
	  {
	    "operation": "shift",
	    "spec": {
	      "clients": {
	        "*": "clients-&"
	      }
	    }
	  },
	
	  {
	    "operation": "shift",
	    "spec": {
	      "clients-*": {
	        "*": "clients-&"
	      }
	    }
	  }
	]


The above exmaples show the basic concepts - there is no need for 3 transforms - the same operation can be performaed in one mapping.

	[
	  {
	    "operation": "shift",
	    "spec": {
	      "*": {
	        "*": {
	          "*": "clients-&"
	        }
	      }
	    }
	  }
	]
 
 
 

WH Example:

The below mapping performs a shift:

*Note* - The `#` operator is used to allow us to use a string literal on the L.H.S - and store it in headers.category of the output.

	[
	  {
	    "operation": "shift",
	    "spec": {
	      "headers": {
	        "matchId": "headers.feedId",
	        "#Example": "headers.category"
	      }
	    }
	  }
	]
	

 

Where given:

	{
	  "headers": {
	    "correlationId": "1769698c-ec9e-442e-8227-4cf3bcc597d2",
	    "incidentTypeId": "WilliamHillDeltaIncident",
	    "provider": "bet_radar_livescout",
	    "category": "Basketball",
	    "sourceId": "BR_14321745",
	    "matchId": "14321745"
	  },
	  "payload": {
	    "inningscore": [],
	    "type": 1044,
	    "stime": 1524540173113,
	    "side": "away",
	    "mtime": "14:23",
	    "info": "Event deleted : Foul (originally entered at 03:22:49 UTC)",
	    "id": 1431622046,
	    "extrainfo": 1431622034,
	    "matchscore": "29:34",
	    "remainingtimeperiod": "09:37",
	    "periodnumber": 2
	  }
	}
	
We return:
	
	{
	  "headers" : {
	    "category" : "Example",
	    "feedId" : "14321745"
	  }
	}

Lets say we want to perform this shift bu we wan to keep all of the originall attributes. Then we can do,


	[
	  {
	    "operation": "shift",
	    "spec": {
	      "headers": {
	        "matchId": "headers.feedId",
	        "#Example": "headers.category"
	      },
	      "*": "headers.&"
	    }
	  }
	]
	
*NOTE* - The `*` attribute is used as a wildcard.
	
This outputs:

*NOTE* - The payload is now nested inside the headers attribute. 

	{
	  "headers" : {
	    "category" : "Example",
	    "feedId" : "14321745",
	    "payload" : {
	      "inningscore" : [ ],
	      "type" : 1044,
	      "stime" : 1524540173113,
	      "side" : "away",
	      "mtime" : "14:23",
	      "info" : "Event deleted : Foul (originally entered at 03:22:49 UTC)",
	      "id" : 1431622046,
	      "extrainfo" : 1431622034,
	      "matchscore" : "29:34",
	      "remainingtimeperiod" : "09:37",
	      "periodnumber" : 2
	    }
	  }
	}

If we would like to keep the flattened structure we need to match on the payload attribute and replace everything in that attribute with it's self ie:

	[
	  {
	    "operation": "shift",
	    "spec": {
	      "headers": {
	        "matchId": "headers.feedId",
	        "#Example": "headers.category"
	      },
	      "*": "headers.&",
	      "payload": {
	        "*": "payload.&"
	      }
	    }
	  }
	]
	
Now we have:

	{
	  "headers" : {
	    "category" : "Example",
	    "feedId" : "14321745"
	  },
	  "payload" : {
	    "inningscore" : [ ],
	    "type" : 1044,
	    "stime" : 1524540173113,
	    "side" : "away",
	    "mtime" : "14:23",
	    "info" : "Event deleted : Foul (originally entered at 03:22:49 UTC)",
	    "id" : 1431622046,
	    "extrainfo" : 1431622034,
	    "matchscore" : "29:34",
	    "remainingtimeperiod" : "09:37",
	    "periodnumber" : 2
	  }
	}


As a more complete example:

	[
	  {
	    "operation": "default",
	    "spec": {
	      "payload": {
	        "side": "none",
	        "extrainfo": "dummy"
	      }
	    }
	    },
	  {
	    "operation": "modify-overwrite-beta",
	    "spec": {
	      "payload": {
	        "stime": "=toString",
	        "extrainfo": "=toString",
	        "id": "=toString",
	        "correctedto": "=toString"
	      }
	    }
	    },
	  {
	    "operation": "shift",
	    "spec": {
	      "headers": {
	        "matchId": "headers.feedId",
	        "category": {
	          "Basketball": {
	            "#basketball": "headers.category"
	          }
	        },
	        "*": "headers.&"
	      },
	      "payload": {
	        "extrainfo": {
	          "@1": "payload.@1"
	        },
	        "stime": "updateTimestamp"
	      },
	      "#1": "INGRESS_TIMESTAMP"
	    }
	  }
	]

Will output:

	{
	  "INGRESS_TIMESTAMP" : "1",
	  "headers" : {
	    "correlationId" : "1769698c-ec9e-442e-8227-4cf3bcc597d2",
	    "incidentTypeId" : "WilliamHillDeltaIncident",
	    "provider" : "bet_radar_livescout",
	    "category" : "basketball",
	    "sourceId" : "BR_14321745",
	    "feedId" : "14321745"
	  },
	  "payload" : {
	    "1431622034" : {
	      "inningscore" : [ ],
	      "type" : 1044,
	      "stime" : "1524540173113",
	      "side" : "away",
	      "mtime" : "14:23",
	      "info" : "Event deleted : Foul (originally entered at 03:22:49 UTC)",
	      "id" : "1431622046",
	      "extrainfo" : "1431622034",
	      "matchscore" : "29:34",
	      "remainingtimeperiod" : "09:37",
	      "periodnumber" : 2
	    }
	  },
	  "updateTimestamp" : "1524540173113"
	}

Here we have an alternative way to include all of the properties under `payload` - in the:

	"extrainfo": {
		          "@1": "payload.@1"
    },

This says when you get to extrainfo go up 2 levels and grab the whole chunk and put it in the payload attribute. *Note* that we go up again !

Removing just this small snippet returns:

	{
	  "INGRESS_TIMESTAMP" : "1",
	  "headers" : {
	    "correlationId" : "1769698c-ec9e-442e-8227-4cf3bcc597d2",
	    "incidentTypeId" : "WilliamHillDeltaIncident",
	    "provider" : "bet_radar_livescout",
	    "category" : "basketball",
	    "sourceId" : "BR_14321745",
	    "feedId" : "14321745"
	  },
	  "updateTimestamp" : "1524540173113"
	}

We can also see here that we have added an `INGRESS_TIMESTAMP` - 


## Jolt Deployments

If we need to deploy Jolt mappings we need to:

Look at [Helens DOCS on the project](https://git.nonprod.williamhill.plc/trading_development/feeds/feed-standardiser-jolt-mappings/tree/feature/TRDBK-689)

The Jolt mappings are stored on a Kafka topic that is essentially used as a lookup table for mappings.

On a high level:

1. Download the project containing the new jolt mappings.
2. Run `mvn clean package`
3. Run the `JoltMappingFileCreator` in `/test`
4. We can use `-t` when running the creator to specify topics we require creating for.
5. We can use `-c` when running the creator to specify the sport that we need to produce mappings for.
6. Once the mapping files are created transfer them to the relevant jumphost - using the tmp folder.
7. Then move to `/opt/kafka/bin`
8. Run:
		
		./kafka-console-producer.sh --broker-list pp1xkaf02brk001.pp1.williamhill.plc:9092 
		--property parse.key=true --property key.separator=";" 
		--topic WilliamHill-fixtureMapping < /tmp/WilliamHill-fixtureMapping.txt
		
9. The above line will need to be edited to include: 
      * The correct broker for the environment.
      * The topic that you are targetting.
      * The name of the file that contain your mappings.

10. Once the mapping is deployed you should get >>> as output.
11. To check that the mapping is created there are 2 checks we can make:
12. Check for the below messages in the correct environment using the app id: "attrs.MARATHON_APP_ID"="/whc/tff01/betradar-standardiser".

		2018-11-26T15:02:48,318 UTC INFO  
		betradarlivescout-incidentMapping 
		com.williamhill.trading.platform.feed.standardiser.services.TransformationRepositoryServiceImpl - - - 
		| Added message transformation with key 
		[bet_radar_livescout_basketball_williamhillfullincident] to cache

13. Check the kafka topic that containd the mappings - This will be the topic that you have just deployed to. This can be done with:
		
		./kafka-console-consumer.sh  
		--bootstrap-server pp2xkaf02brk001.pp2.williamhill.plc:9092 
		--property print.key=true --topic WilliamHill-fixtureMapping 
		--from-beginning | grep -i timeout




# Gimmighoul respawn mechanics
Based on [data](https://twitter.com/Sibuna_Switch/status/1594002939450126338) from Anubis and personal research.

## Common misunderstanding of the respawn mechanics
The error in the common idea is that when a respawn attempt occurs **each** missing gimmighoul has a chance of being respawned (5% for the chest form, 50% for the roaming form). The respawn algorithm however is a bit different.  
For each respawn attempt, for each form (not for each gimmighoul):
+ first, the code runs the 5% or 50% check **only once**, on a failed attempt 0 gimmighoul will be respawned.
+ if the first check is successful a random value will be picked from the "QuantityData" table (see Anubis' tweet above) and **exactly that number** of gimmighoul will be respawned by filling the available empty spots

In pseudo-code:
```
function spawnGimmighoul(form) {  
    if (random(10000) > form.RevivalRate) return;  

    n = form.pickRandomValueFromQuantityData();  
    for(i=0,i!=n,i+=1) {
        if form.freeSpots.length() == 0 return;
        form.freeSpots.pickRandomSpot().respawn();
    } 
}
```  

The purpose of "-1" is uncertain, it may respawn all missing gimmighoul of that form (as in the pseudocode above), however it is so rare it is practically impossible to verify it.  
### How did you find out?
With a lot of experiments and time, luck also helped a couple of times.   
Initially I simply wanted to understand if the respawn attempt was run once per real day or once per in-game day. Both hypothesis were incompatible with my results so I kept investigating it.  
Below you can find a description of some of the experiments.  

## When does the spawn attempt occur?  
There are at least two situations:
+ while playing, shortly after midnight in Paldea (approximately 15 minutes after the sunset to night transition, when the moon is facing north)
+ shortly after loading the game, if the Paldean day has changed (forward) between the saving time and the loading time  
  
Notice that the attempts occur *shortly after* midnight/loading, not immediately. As far as I know it takes less than 1 minute.  
  
The day-night cycle stops when you enter the Switch home menu or while the console is in standby, as a consequence leaving the game in standby for a long time will not result in any additional spawn attempts.  
The in-game day lasts 72 minutes. See [serebii's guide](https://www.serebii.net/scarletviolet/daynightcycle.shtml) for the details.  
### "Double" spawn attempts
If you load the game shortly before the in-game midnight *and* at least one Paldea day has passed from the last save two spawn attempts will occur within a short time.  
For example if you save _before_ 4:48 (end of a Paldea day), close, load the game at 5:59 (1 minute before the next Paldea day) a respawn attempt will occur shortly after loading then another in-game day will start (at 6:00) and a second respawn attempt will occur within a minute.  
### Does the spawn attempt occur while the player is in another map (area zero, dlc regions...)?
I don't know yet. It can be tested but it takes time.  
The procedure would involve the following steps:
+ respawn all roaming form
+ collect coins from at least 21 specific roaming form locations, write down the locations or take a screenshot to remember where they are. This step must be done in a single Paldea day to be certain not to respawn any of them.
+ move to the map to test (for example Kitakami) and repeat several times the procedure to force respawn attempts.
+ go back to paldea during the day and check all locations before midnight.  
  
If all places are filled with gimmighoul then the respawn attempts probably did occur in the other map, run the test a second time to make sure it was not a rare occurrence of the "-1". If none or only an amount from the "QuantityData" then the spawn attempts did not occur on the other map, only one was triggered when going back to Paldea. 

## Does this help me to farm gimmighoul coins quickly?
It still takes a lot of effort and time to collect 999 coins. The data in the jupyter notebook can help you making the farming process more reliable but it will still take time.  
The most important part is that you can:  
+ easily force respawn attempts by saving, closing the game and moving the console clock forward
+ concentrate on places that are fast to reach and/or have many gimmighoul close to each other, ignore gimmighoul in places that take too long to reach. As long as there are enough free spots to fill, the algorithm will respawn the same amount of gimmighoul so you are not "wasting" respawn chances by getting that particular chest that takes a minute to reach. (You can use serebii's Paldea map to plan a route)
+ estimate approximately how many roaming form and chest form will be respawned after a certain number of respawn attempts (and therefore decide when it is worth collecting coins/avoid looking for them when only a few have respawned).  
  
  
If you want to farm coins more quickly make sure to also collect the roaming form (not only the chest form), they may yield less coins per gimmighoul but they respawn more often and in large quantities. On average about 80% of the respawned coins come from this form.
## Experimental evidence
Over time I will write a description of the experiments that led me to this conclusion. Right now the list is not complete.  

### Chest respawns not matching the expected amount 
If you try to read some posts (on reddit and forums) about gimmighoul spawns you will find people that are certain that the respawn occurs once every real day and other that are certain that the respawn occurs once every in-game day. My initial plan was to find out which one of the two was correct.  
  
If you consider a 5% chance for each chest to respawn at every respawn attempt, by clearing all 25 respawn locations, leaving the game running (afk) for 24 h and then checking again all locations you would expect one of the following outcomes:
+ one respawn attempt per real day: >99% probability of finding 4 or less respawned chests
+ one respawn attempt per in-game day (20 attempts in 24 h): extremely low probability of finding less than 9 respawned chests
  
Repeating the test over a few days I got contradictory results: sometimes 0 to 2 chests/real day, sometimes 5 or more. Also the spawns where occurring at different hours in the same (real) day, suggesting that the 1 spawn attempt per in-game day was probably right but the "5% each" was not.  
### Roaming form respawns not matching the expected amount
This is similar to the previous one. I cleared several roaming form locations and then checked a few of those locations at different time intervals, the respawns were occurring but at the same time they were clearly too low to be compatible with a "50% each".  
### Fixed route tests
I left the game running afk for long enough to respawn most of the roaming forms then developed a route to check on all chest form locations and 40 roaming form locations near them.  
The initial results where unclear.    
Eventually I decided to spend a couple of hours running non stop along the route, starting a new cycle as soon as I ended the first. This is where I got lucky and got 3 successful roaming form spawns in 3 consecutive in-game days (12.5% chance) and noticed that all of them had occurred in the middle of the night.  
After that I kept testing the same route and noticed that in all of my tests the roaming form always respawned in multiples of 5 (if you check the "QuantityData" tables the most common amounts are 10, 5 and 20, 1 and -1 are extremely rare), clearly incompatible with a respawn mechanism applied to each gimmighoul.   
Eventually I moved to testing on a shorter route to be able to run additional tests in a short time.  
After a few days I remembered about the misterious "???" tables on serebii (the "QuantityData" from Anubis tweet) and found out that my results could be explained by assuming that the respawn attempt was run once for each form and would pick the number of gimmighoul to respawn from those tables.

# Gimmighoul respawn mechanics
Based on [data](https://twitter.com/Sibuna_Switch/status/1594002939450126338) from Anubis and personal research.

## Common misunderstanding of the respawn mechanics
The common idea is that when a respawn attempt occurs **each** missing gimmighoul has a chance of being respawned (5% for the chest form, 50% for the roaming form). The respawn algorithm however is a bit different.  
For each respawn attempt, for each form (not for each gimmighoul):
+ first, the code runs the 5% or 50% check **only once**, on a failed attempt 0 gimmighoul will be respawned.
+ if the first check is successful a random value will be picked from the "NumData" table (see Anubis' tweet above) and **exactly that number** of gimmighoul will be respawned by filling the available empty spots

In pseudo-code:
```
function spawnGimmighoul(form) {  
    if (random(10000) > form.RevivalRate) return;  

    n = form.pickRandomValueFromNumData();  
    for(i=0,i!=n,i+=1) {
        if form.freeSpots.length() == 0 return;
        form.freeSpots.pickRandomSpot().respawn();
    } 
}
```  

The purpose of "-1" is uncertain, it may respawn all missing gimmighoul of that form however it is so rare it is practically impossible to verify it.  
### How did you find out?
With a lot of experiments and time, luck also helped a couple of times.  
Initially I simply wanted to understand if the respawn attempt was run once per real day or once per in-game day. Both hypothesis were incompatible with my results so I kept investigating it.  
When I will have more time I will add a description of some of the experiments.  

## When does the spawn attempt occur?  
There are at least two situations:
+ while playing, shortly after midnight in Paldea (approximately 15 minutes after the sunset to night transition, when the moon is facing north)
+ shortly after loading the game, if the Paldean day has changed (forward) between the saving time and the loading time  
  
Notice that the attempts occur *shortly after* midnight/loading, not immediately. As far as I know it takes less than 1 minute.  
  
The day-night cycle stops when you enter the Switch home menu or while the console is in standby, as a consequence leaving the game in standby for a long time will not result in any additional spawn attempts.  
The in-game day lasts 72 minutes. See [serebii's guide](https://www.serebii.net/scarletviolet/daynightcycle.shtml) for the details.  
### Does the spawn attempt occur while the player is in another map (area zero, dlc regions...)?
I don't know yet. It can be tested but it takes some time.  

## Does this help me to farm gimmighoul coins quickly?
It still takes a lot of effort and time to collect 999 coins. The data and text in the jupyter notebook can help you making the farming process more reliable but it will still take time.  
The most important part is that you can:  
+ easily force respawn attempts by saving, closing the game and moving the console clock forward
+ estimate approximately how many roaming form and chest form were respawned after a certain number of respawn attempts (and therefore when it is worth collecting coins)

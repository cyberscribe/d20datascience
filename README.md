# d20datascience
Data science investigations into the mechanics of the world's greatest role playing game.

## Purpose

The aim of this research is to use compuational analysis to provide useful insights to game masters seeking to exploit a deeper understanding of the components of combat effectiveness in service to creating combat encounters that are informed, intentional, and appropriate to the circumstances of their game.

## Introduction

### Role-Playing Games
Role-playing games offer participants the opportunity to engage in a shared storytelling experienced governed by a complex set of rules. Players assume the role of **characters** with both described and quantified attributes. A game master applys and interprets the rules to determine the results of a character's decisions and behaviour, often based on these attributes. Dice are used as part of the rules to simulate the statistical likelihood of success or failure.

The purpose of the game is to give players the opportunity to interact with other beings, explore their environment, and engage in combat against **creatures**. Because players are given free choice in relation to their character's behaviour, it is impossible to simulate the full range of possible experiences within a closed system. This may be one factor in the enduring popularity of role-playing games despite advances in the realism of video game technology.

### Managing Combat Challenge
The role of the game master therefore is a mix of science and art. For example, it is important for a game master to be able to create challenges for the players so that their accomplishments and progress feels earned. Quantified character levels and creature challenge ratings are used to pit a group of characters against a group of creatures to ensure an appropriate challenging during combat. 

A combat encounter that is too easy can be boring; one that is far too difficult can result in the death of one or more players' characters. While such encounters can be deployed strategically in service to the narrative, when they occur unintentionally they can be frustrating to both the players and the game master. It is therefore important for a game master to have a realistic understanding of combat effectiveness in order to create encounters that are appropriately challenging and intentional.

### Combat Complexity

However, even within the scope of combat, one subset of the role-playing experience, an encounter can include variables such as: unique terrain features, the starting distance of the encounter, complex spells, powerful magic items, and the relative experience of the game master and players in these types of combat situations. It is therefore impossible to fully simulate all of the factors involved either mathematically or computationally.

### Simulating Simplified Combat
That said, while the **fundamental mechanic** of melee combat is somewhat more complicated to describe mathematically, it is relatively straightforward to simulate computationally. By repeatedly simulating this mechanic between a variety of character and creature configurations, we are able to determine the relative effectiveness of such configurations in relation to this fundamental mechanic.

### Implications of Simulation Results
This fundamental mechanic may also serve as a useful proxy for combat effectiveness within the real, complex game. Assuming this is true, we can make deductions about the relative influence of various configutation elements on combat effectiveness within the actual game. 

Doing so can, for example, help us to gauge the accuracy of the current level and challenge rating system as well as provide game masters with a deeper understanding of the components of combat effectiveness overall. This will in turn help them to create combat encounters that are informed, intentional, and appropriate to the circumstances of their game.

## Methodology

Round-robin simulations of combat were performed in various contests between assembled characters and creatures to determine rankings of combat effectiveness for each entity configuration in relation to the **fundamental mechanic** (Monte Carlo simulation). The results were then analysed using regression analysis and machine learning (k-nearest-neighbours) to make conclusions about the relative influence of various configutation elements on combat effectiveness in relation to the fundamental mechanic. These results were then interpreted by an experienced game master to provide insights into how these findings might be applied by game masters in relation to the actual, more complex game.

## Scope

A **contest** has a number of **matches** equal to the nth triangular progression for the number of creatures participating in the contest minus one (since creatures do not fight themselves). It also therefore has a number of total **rounds** equal to the number of matches times the predetermined number of rounds per match. 

A reasonable number of rounds were performed to reduce the possibility of a few number of lucky (or unlucky) rolls causing skewed results. The number of **turns** is determined as a function of the number of rounds, but is determined by how many turns are required by each pairing of creatures in a round to declare a victor.

Therefore for any non-trivial contest, in general:

`num_turns > num_rounds > num_matches`

It is therefore convenient to use computational simulation of the fundamental mechanic for each turn in any contest of a size that is likely to produce significant and meaningful results.

## Data

### Structure
Simulation of the fundamental mechanic requires the following attribute variables for each entity involved:

 - `ac` (armour class) - measures defensive capability; in order for an attack to succeed `d20` + `hit_mod` must equal or excede this value in a **turn**
 - `hp` (hit points) - measures ability to withstand successful attacks without dying; for each succesful attack during a **turn** the target loses `dam_avg` hit points; when `hp` is less than or equal to zero the target is dead
 - `init` (initiative) - measures combat agility; `d20` + `init` is rolled at the start of a **round**; the entity with the highest total number goes first 
 - `att_num` (number of attacks) - measures speed of attack; the number of attacks that can be made by this entity in a single **turn**
 - `hit_mod` (to hit modifier) - measures ability to make successful attacks against `ac` on an attacker's **turn** [a.k.a. attack bonus or attack modifier]
 - `dam_avg` (average damage) - measures ability to inflict harm; decrements `hp` by this amount  during a **turn** when an attack succeeds

Additionally, correlation between combat effectiveness and the assigned challenge rating or level of a creature or character (respectively) can be inferred when a `cr` (challenge rating) variable is included.

Finally, an optional `name` label can be added to refer to each collection of attributes semantically.

### Source

While [v5.1 of the System Reference Document (SRD)](http://dnd.wizards.com/articles/features/systems-reference-document-srd) provides 325 pre-configured creatures, the distribution of creatures across challenge ratings is heavily skewed toward the lower values.

!["Distribution of CR for SRD v5.1 Creatures"](https://raw.githubusercontent.com/cyberscribe/d20datascience/master/images/cr-dist-srd.png "Distribution of CR for SRD v5.1 Creatures")

Creating our own **generic creatures** and **generic characters** using a more even distribution of `cr` values is therefore more relevant to our investigations.

## Computational Analysis

Details of the computational analysis are provided in the form of a [Jupyter notebook](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb).

## Findings

### [Correlation Between Combat Success and Attributes](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb#Correlation-Between-Combat-Success-and-Attributes)

Not surprisingly, there is almost no correlation (r ~ 0.02) between `init` (initiative) and combat success. While "first mover advantage" may apply in the real, complex game in relation to decisive initial moves, over a large-scale simulation initiative serves as a "tie breaker" only in very rare cases where entities are so well matched that a single turn of combat can decide the outcome.

More surprisingly, `ac` (armour class) and `hit_mod` (hit modifier) also had almost no correaltion to combat success (r ~ 0.08 in both cases). Given the extent to which armour is seen as a measure of defensive effectiveness and the hit modifier is seen as a measure of offensive effectiveness, this may seem counter-intuitive to longtime players. One explanation is that the use of critical success and critical fail mechanics significantly level the playing field. 

Any entitiy has a 5% chance of failure no matter how high their hit modifier and how low the target armour class. Conversely, any entitiy has a 5% chance of success no matter how low their hit modifier and how high the target armour class. So the mechanics of hitting or missing only take `ac` and `hit_mod` into account 90% of the time. Within this scope, there are very few other always-hit or always-miss scenarios. 

As a result, hitting or missing a target on a single turn, while incredibly dramatic and significant in the context of a single encounter, is less signficiant over a large number of combat encounters, and therefore armour and weapon proficiency are not as signficant to combat success overall as one might initially surmise.

However, `att num` (number of attacks) showed a stronger correlation to combat success (r ~ 0.42) as did `dam_avg` (r ~ 0.48). The product of these two, which we call `dam_max` (maximum average damage output per turn) showed a slightly higher correlation (r ~ 0.58) as did `hp` (hit points) (r ~ 0.57).

Combining `dam_max` and `hp` into a new metric by taking the square root of their product gives us `hp_dam` (hit-point-damage metric) with a very strong (r ~ 0.93) correlation to combat effectiveness.

!["Correlation Between dam_max and Combat Effectiveness"](https://raw.githubusercontent.com/cyberscribe/d20datascience/master/images/hp_dam.png "Randomly-Generated Creatures")

In short, an entity's ability to deliver damage (`dam_max`) rapidly and take damage over time without dying (`hp`) are the most significant controling factors in combat success, and should be considered as the most influential when determining creature challenge rating.

### [Generic Characters and Creatures](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb#Generic-Characters-and-Creatures)

Using published guidelines, it was possible to create generic characters and creatures, including parties composed of all one character type (fighter, wizard) that exhibited a strong correlation between challenge rating and combat success rate and even distribution/progression for that type in contests composed of entities entirely of that type. This allows us to perform contests with mixed types.

### [Contests Between Characters and Creatures](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb#Contests-Between-Characters-and-Creatures)

!["Correlation Between CR and Wins"](https://raw.githubusercontent.com/cyberscribe/d20datascience/master/images/characters-1monster.png "Party of 4 and Single Monster")

!["Correlation Between CR and Wins"](https://raw.githubusercontent.com/cyberscribe/d20datascience/master/images/characters-to30monsters.png "Party of 4 and 1-30 CR 1 Monsters")

## Interpretation

## Conclusion

## Definition of Terms

 - **d20** - the simulation of rolling a twenty-sided polyhedral die (icosahedron)
 - **Creature** - an entity controlled by the game master
 - **Character** - an entity controlled by a player
 - **Challenge rating** - A quantified measure of the relative difficulty of overcoming a **creature** as a foe in the actual role-playing game, either through combat or other means
 - **Character level** - A quantified measure of the powerfulness of a **character**, which includes their degree of effectiveness in combat as well as other activities such as exploration and social interaction
 - **Generic creature** - A **creature** of a specific **challenge rating** whose attributes have been generated using an interpretation of the guidelines on p. 274 of Dungeon Master's Guide (Wizards of the Coast, December 2014) in attempt to simulate a "typical" creature of this challenge rating in respect to the attributes necessary to simulate the **fundamental mechanic**
 - **Generic character** - A **character** of a specific level whose attributes have been generated using an interpretation of the guidelines in the Player's Handbook (Wizards of the Coast, August 2014) in attempt to simulate a "typical" character of this challenge rating in respect to the attributes necessary to simulate the **fundamental mechanic**
 - **Fundamental mechanic** - An simulation of an essentialised form of melee combat conducted as a **match** between two entities. In game terms, both entities start within melee range and can only take a melee attack action on their turn; no spells, magic items, movement, or other actions are simulated
 - **Contest** - a round-robin simulation pairing every creature with every other creature in a single match to determine rankings based on the total number of matches that each creature has won (survived)
 - **Match** - a simulation of repeated fights to the death between two creatures, performed for a predetermined number of rounds
 - **Round** - a simulation of a fight to the death between two creatures, determined by rolling for initiative to determine which creature goes first and then alternating between attacks where one is the attacker and one is the defender for as many turns as required until one of the creatures drops to zero hit points or below and is dead, whereupon the survivor wins
 - **Turn** - a simulation of an attack by one creature on another

## Reference

[Jupyter notebook containing all experiments](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb)

## Acknowledgments

Thanks to Robert Staton for providing review.

## Legal Disclaimer

This research is provided for educational purposes and is based on the game mechanics of the [SRD 5.1](http://dnd.wizards.com/articles/features/systems-reference-document-srd) licensed under [OGL v.01a](https://dnd.wizards.com/articles/features/systems-reference-document-srd) as well as researcher interpretations of guidelines set out in the Player's Handbook (Wizards of the Coast, August 2014) and Dungeon Master's Guide (Wizards of the Coast, December 2014). No license, contract, or agreement is hereby expressed or implied by the researcher in respect to the owner of the world's greatest role-playing game or its respective copyrights or trademarks.
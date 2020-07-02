# d20datascience
Data science investigations into the mechanics of the world's greatest role playing game.

## Purpose

The aim of this research is to use computational analysis to provide useful insights to game masters seeking to exploit a deeper understanding of the components of combat effectiveness in service to creating combat encounters that are informed, intentional, and appropriate to the circumstances of their game.

## Introduction

### Role-Playing Games
Role-playing games offer participants the opportunity to engage in a shared storytelling experience governed by a complex set of rules. Players assume the role of **characters** with both described and quantified attributes. A game master applies and interprets the rules to determine the results of a character's decisions and behaviour, often based on these attributes. Dice are used as part of the rules to simulate the statistical likelihood of success or failure.

The purpose of the game is to give players the opportunity to interact with other beings, explore their environment, and engage in combat against **creatures**. Because players are given free choice in relation to their character's behaviour, it is impossible to simulate the full range of possible experiences within a closed system. This may be one factor in the enduring popularity of role-playing games, despite advances in the realism of video game technology.

### Managing Combat Challenge
The role of the game master therefore is a mix of science and art. For example, it is important for a game master to be able to create challenges for the players so that their accomplishments and progress feel earned. Quantified character levels and creature challenge ratings are used to pit a group of characters against a group of creatures to ensure an appropriate challenge during combat. 

A combat encounter that is too easy can be boring; one that is far too difficult can result in the death of one or more players' characters. While such encounters can be deployed strategically in service to the narrative, when they occur unintentionally, they can be frustrating to both the players and the game master. It is therefore important for a game master to have a realistic understanding of combat effectiveness in order to create encounters that are appropriately challenging and intentional.

### Combat Complexity

However, even within the scope of combat--one subset of the role-playing experience--an encounter can include variables such as: unique terrain features, the starting distance of the encounter, complex spells, powerful magic items, and the relative experience of the game master and players in these types of combat situations. It is therefore impossible to fully simulate all of the factors involved either mathematically or computationally.

### Simulating Simplified Combat

#### Monte Carlo Simulation

It is relatively straightforward to simulate a simplified melee combat scenario computationally. By repeatedly simulating this mechanic between a variety of character and creature configurations, we are able to converge on stable results regarding the relative effectiveness of different numerical attribute combinations in relation to this fundamental mechanic.

### Implications of Simulation Results
This fundamental mechanic may also serve as a useful proxy for combat effectiveness within the real, complex game. Assuming this is true, we can make deductions about the relative influence of various configuration elements on combat effectiveness within the actual game. 

Doing so can, for example, help us to gauge the accuracy of the current level and challenge rating system as well as provide game masters with a deeper understanding of the components of combat effectiveness overall. This will in turn help them to create combat encounters that are informed, intentional, and appropriate to the circumstances of their game.

## Existing Research

There have been a number (e.g. [here](http://surfarcher.blogspot.com/2014/12/d-5e-monsters-part-10-construction-cr.html) and [here](http://blogofholding.com/?p=7338)) of mathematical analyses of the relationship between player or character attributes and challenge rating, conducted in an attempt to both test and simplify the published formula for calculating the challenge rating of a custom-built creature. [Matteo Ferla](http://home-matteo-ferla.a3c1.starter-us-west-1.openshiftapps.com/dnd) has programmed an extensive simulation of combat and drawn some conclusions. However, to date I am not aware of any analyses that simulate combat computationally on a wide scale in an attempt to infer these relationships using linear regression and machine learning.

## Methodology

Round-robin simulations of combat were performed in various contests between assembled characters and creatures to determine rankings of combat effectiveness for each entity configuration in relation to the **fundamental mechanic** (Monte Carlo simulation). The results were then analysed using regression analysis and machine learning (k-nearest-neighbours) to make conclusions about the relative influence of various configuration elements on combat effectiveness in relation to the fundamental mechanic. These results were then interpreted by an experienced game master to provide insights into how these findings might be applied by game masters in relation to the actual, more complex game.

## Scope

A **contest** has a number of **matches** equal to the nth triangular progression for the number of creatures participating in the contest minus one (since creatures do not fight themselves). It also therefore has a number of total **rounds** equal to the number of matches times the predetermined number of rounds per match. 

A reasonable number of rounds were performed to reduce the possibility of a few number of lucky (or unlucky) rolls causing skewed results. The number of **turns** is determined as a function of the number of rounds, but is determined by how many turns are required by each pairing of creatures in a round to declare a victor.

Therefore, for any non-trivial contest, in general:

`num_turns > num_rounds > num_matches`

It is therefore convenient to use computational simulation of the fundamental mechanic for each turn in any contest of a size that is likely to produce significant and meaningful results.

## Data

### Structure
Simulation of the fundamental mechanic requires the following attribute variables for each entity involved:

 - `ac` (armour class) - measures defensive capability; in order for an attack to succeed `d20` + `hit_mod` must equal or excede this value in a **turn**
 - `hp` (hit points) - measures ability to withstand successful attacks without dying; for each successful attack during a **turn** the target loses `dam_avg` hit points; when `hp` is less than or equal to zero the target is dead
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

Details of the computational analysis are provided in the form of a [Jupyter notebook](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb), which can be executed to reproduce the results of all experiments described.

## Findings

### [Correlation Between Combat Success and Attributes](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb#Correlation-Between-Combat-Success-and-Attributes)

### Init Doesn't Matter Very Much

Not surprisingly, there is almost no correlation (r ~ 0.02) between `init` (initiative) and combat success. While "first mover advantage" may apply in the real, complex game in relation to decisive initial moves, over a large-scale simulation initiative serves as a "tie breaker" only in very rare cases where entities are so well matched that a single turn of combat can decide the outcome.

### Neither Does AC or Hit Mod

More surprisingly, `ac` (armour class) and `hit_mod` (hit modifier) also had almost no correlation to combat success (r ~ 0.08 in both cases). Given the extent to which armour is seen as a measure of defensive effectiveness and the hit modifier is seen as a measure of offensive effectiveness, this may seem counter-intuitive to long-time players. 

### The Role of Crit Rules

One explanation is that the use of critical success and critical fail mechanics ("crit rules") significantly levels the playing field. Under these rules, any entity has a 5% chance of failure no matter how high their hit modifier and how low the target armour class. Conversely, any entity has a 5% chance of success no matter how low their hit modifier and how high the target armour class. So, the mechanics of hitting or missing only take `ac` and `hit_mod` into account 90% of the time. Within this scope, there are very few other always-hit or always-miss scenarios. 

However, while combat scenarios in which crit rules were not implemented, the relevance of `ac` and `hit_mod` increased but not to highly significant levels (r ~ 0.14 and r ~ 0.27, respectively). So, while crit rules do reduce the relevance of `ac` and `hit_mod` in relation to combat success, they are not the only factor at play.

### Single-Event Perception versus Simulation at Scale

Within the range of `ac` and `hit_mod`, certain outliers will almost never hit or never miss. Over a large-scale analysis, however, those configurations closer to the mean tend to indicate that the latter metrics matter more.

Ultimately, hitting or missing a target on a single turn, while incredibly dramatic and significant in the context of a single encounter, is less signficant over a large number of combat encounters, and therefore armour and weapon proficiency are not as significant to combat success overall as one might initially surmise.

### Dealing and Soaking Up Damage

However, `att num` (number of attacks) showed a stronger correlation to combat success (r ~ 0.42) as did `dam_avg` (r ~ 0.48). The product of these two, which we call `dam_max` (maximum average damage output per turn) showed a slightly higher correlation (r ~ 0.58) as did `hp` (hit points) (r ~ 0.57).

Combining `dam_max` and `hp` into a new metric by taking the square root of their product gives us `hp_dam` (hit-point-damage metric) with a very strong (r ~ 0.93) correlation to combat effectiveness.

!["Correlation Between dam_max and Combat Effectiveness"](https://raw.githubusercontent.com/cyberscribe/d20datascience/master/images/hp_dam.png "Randomly-Generated Creatures")

In short, an entity's ability to deliver damage (`dam_max`) rapidly and take damage over time without dying (`hp`) are the most significant controlling factors in combat success, and should be considered as the most influential when determining creature challenge rating.

### [Generic Characters and Creatures](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb#Generic-Characters-and-Creatures)

Using published guidelines, it was possible to create generic characters and creatures, including parties composed of all one character type (fighter, wizard) that exhibited a strong correlation between challenge rating and combat success rate and even distribution/progression for that type in contests composed of entities entirely of that type. This allows us to perform contests with mixed types with confidence that the results for each type will be representative of the progression for that type.

### [Contests Between Characters and Creatures](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb#Contests-Between-Characters-and-Creatures)

The results of a contest between 1-30 CR 1 monsters, single monsters whose CR ranged from 1-30, a party of 4 generic human fighters, and a party of 4 generic elven wizards can be visualised as follows.

!["Correlation Between CR and Wins"](https://raw.githubusercontent.com/cyberscribe/d20datascience/master/images/mixed-all.png "Party of 4, Single Monster, and 1-30 CR 1 Monsters")

Clearly, the effect of having a linear progression of the number of attacks in the scenario of 1-30 CR 1 monsters is significantly more predictive of combat success than the comparable progression through CR levels of a single monster. It has the strongest performance overall, but is very closely matched by that of a party of four fighters.

The single monster scenario performs poorly against these two, and the party of four wizards does even worse. However, the relative distance between the fighter party and wizard party from the single monster trendline would seem to indicate that a more typical "mixed" party, which we would presume to form a composite average of the two extremes, would be relatively well matched to the single-monster scenario performance line.

It is also interesting to note the somewhat elliptical shape of the outer progressions, converging at CR1 and CR 30. This would seem to indicate a deceleration in combat effectiveness for the most powerful groups, whereas the single monster scenario performance remains more linear.

## [K-Nearest-Neighbours Classification](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb#Classifying-fighters-using-k-nearest-neighbours-(k=5)-trained-on-generic-monsters)

The k-nearest-neighbours ("knn") machine learning algorithm offers us a convenient way to compute the Euclidian distance between points in n-space (where n is the number of quantified attributes) and classify new points based on their proximity to k points with known classifications. There is also [some indication that this form of analysis can provide a "tighter" fit than multiple linear regression analysis](https://www.researchgate.net/publication/265978662_Comparing_K_nearest_neighbours_methods_and_Linear_regression-is_there_reason_to_select_one_Over_the_other). Using the *generic creatures* created using published guidelines as the classified points (training set), we can then classify our parties of wizards and fighters based on their proximity to corresponding points in the generic creatures data set.

!["Correlation Between Actual CR and Predicted CR"](https://raw.githubusercontent.com/cyberscribe/d20datascience/master/images/fighters-knn.png "Party of 4 Fighters as predicted from generic monsters (k=5)")

In the case of fighters, the trend (curve shape) of the predictions, as well as the endpoint values, match that of our contest values. This indicates some deceleration in combat effectiveness at the higher levels. However, a level 20 fighter party is still both able to take on a cr 30 monster in simulated simplified melee combat, and also shares the most traits in common in terms of the Euclidian distance relative to all of its attributes with a CR 30 monster.

!["Correlation Between Actual CR and Predicted CR"](https://raw.githubusercontent.com/cyberscribe/d20datascience/master/images/wizards-knn.png "Party of 4 Wizards as predicted from generic monsters (k=5)")

In the case of wizards, the trend (curve shape) of the predictions again matches that of the contest values. However, the wizard cr classifications based on attributes are more closely aligned level-for-cr with those of the generic monsters. This may indicate that the results of a four-type contest tend to unfairly disadvantage the least-powerful group.

However, we again note an acceleration over time in classified challenge rating which matches the acceleration in combat effectiveness.

So, according to both contest results and classification results, wizards get somewhat better as they increase in level whereas fighters get somewhat worse. Still, fighters end up able to take on monsters of a cr nearly 150% of their rated level by the time they reach level 20.

## Interpretation of Results

Focusing on creatures as a "damage-dealing sack of hit points" is a simplified way to understand combat effectiveness and, in the context of this simplified mechanic, an effective one.

A number of "buffs" (enhancements) and "nerfs" (handicaps) to creatures and players can be layered on throughout the game. However, the simplified mechanic (I hit you, you hit me) represents the core of the combat experience, and so understanding this can help game masters working in real-time to select appropriate encounters quickly.

Knowing that the challenge rating system is somewhat conservative is useful as well. Increasing the number of opponents can increase the difficulty quickly by increasing hit points, damage output, and number of attacks in a more significant manner than selecting single creatures of higher challenge ratings. So, as a general rule player party versus single-monster will end up being somewhat easier than player party versus monster party for comparable overall challenge ratings.

The composition of a party matters, and in particular having fighters available early on in game play can help supplement and balance out character types weaker in combat. 

The most surprising result is the relatively lower importance of `ac` and `hit_mod` in predicting combat success. This should be taken in context of a large-scale analysis--in single encounters, a creature that "rarely hits" or "rarely misses" will indeed be highly ineffective or effective (respectively). After all, the game experience is enjoyed in the moment--and the death of a player, while just a tally mark in our analysis, is a significant in-game event. In this way, a conservative challenge rating system may be to everyone's benefit.

While low-level parties are vulnerable, there is a perception among game masters that [high-level parties can be difficult to challenge in combat](http://www.enworld.org/forum/showthread.php?577378-How-viable-is-5E-to-play-at-high-levels). One theory to explain this is that [the linear progression of hit points is out of alignment with the original game designer's vision](http://dmdavid.com/tag/would-dungeons-dragons-play-better-if-it-stayed-loyal-to-how-gary-gygax-awarded-hit-points/). Our analysis of the importance of hit points would tend to support the idea that a "damped" progression at higher levels would provide one solution. 

However, other options--such as "piling on monsters", as well as a range of "buffs" exist to provide challenge rating parity. One can also adjust the pairings based on the conservative nature of the system (pitting four `cr` 20 fighters against a single cr `30` monster, for example).

## Opportunities for Further Study

[Matteo Ferla](http://home-matteo-ferla.a3c1.starter-us-west-1.openshiftapps.com/dnd) has programmed a far more extensive simulation of combat. Running his simulations within the context of these techniques could provide another point of validation (or refutation) of these findings.

The trope that [fighters progress linearly whilst spell-casters progress quadratically](https://tvtropes.org/pmwiki/pmwiki.php/Main/LinearWarriorsQuadraticWizards) refers to the "buffs" (spells) that wizards gain. Such a progression clearly exists outside the scope of this research. Simulating maximum damage output and/or the "spell saving throw" mechanic, however, might provide an opportunity to test this widely-held perception.

## Conclusion

Using regression analysis and machine learning, we have gained insights into the relationship between combat attributes, challenge rating, and combat effectiveness using a simplified version of the combat mechanics of the world's greatest role-playing game. Understanding these mathematical relationships can empower game masters to create more dynamic encounters to maintain the perception in combat that there is both something at stake and worth fighting for. This, after all, is what creates an earned sense of heroic achievement--a unique experience, scarce in real life, which gives the game an enduring appeal.

## Definition of Terms

 - **d20** - the simulation of rolling a twenty-sided polyhedral die (icosahedron)
 - **Creature** - an entity controlled by the game master
 - **Character** - an entity controlled by a player
 - **Challenge rating** - A quantified measure of the relative difficulty of overcoming a **creature** as a foe in the actual role-playing game, either through combat or other means
 - **Character level** - A quantified measure of the powerfulness of a **character**, which includes their degree of effectiveness in combat as well as other activities such as exploration and social interaction
 - **Generic creature** - A **creature** of a specific **challenge rating** whose attributes have been generated using an interpretation of the guidelines on p. 274 of Dungeon Master's Guide (Wizards of the Coast, December 2014) in attempt to simulate a "typical" creature of this challenge rating in respect to the attributes necessary to simulate the **fundamental mechanic**
 - **Generic character** - A **character** of a specific level whose attributes have been generated using an interpretation of the guidelines in the Player's Handbook (Wizards of the Coast, August 2014) in attempt to simulate a "typical" character of this challenge rating in respect to the attributes necessary to simulate the **fundamental mechanic**
 - **Fundamental mechanic** - A simulation of an essentialised form of melee combat conducted as a **match** between two entities. In game terms, both entities start within melee range and can only take a melee attack action on their turn; no spells, magic items, movement, or other actions are simulated
 - **Contest** - a round-robin simulation pairing every creature with every other creature in a single match to determine rankings based on the total number of matches that each creature has won (survived)
 - **Match** - a simulation of repeated fights to the death between two creatures, performed for a predetermined number of rounds
 - **Round** - a simulation of a fight to the death between two creatures, determined by rolling for initiative to determine which creature goes first and then alternating between attacks where one is the attacker and one is the defender for as many turns as required until one of the creatures drops to zero hit points or below and is dead, whereupon the survivor wins
 - **Turn** - a simulation of an attack by one creature on another

## Reference

[Live Jupyter notebook on Google Collab](https://colab.research.google.com/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb)

## Acknowledgments

Thanks to Robert Staton, Matteo Ferla, and Moritz Speicher for reviewing and commenting on this research. Special thanks to instructors in the [UC Berkeley edX Data8x course](https://data.berkeley.edu/education/data-8x), upon which the methodologies used in this research are based.

## Legal Disclaimer

This research is provided for educational purposes and is based on the game mechanics of the [SRD 5.1](http://dnd.wizards.com/articles/features/systems-reference-document-srd) licensed under [OGL v.01a](https://dnd.wizards.com/articles/features/systems-reference-document-srd) as well as researcher interpretations of guidelines set out in the Player's Handbook (Wizards of the Coast, August 2014) and Dungeon Master's Guide (Wizards of the Coast, December 2014). 

No license, contract, or agreement is hereby expressed or implied by the researcher in respect to the owner of the world's greatest role-playing game or its respective copyrights or trademarks. 

This research is also not sponsored or endorsed by anyone mentioned in the acknowledgments, including UC Berkeley, edX, or the instructors of data8x.

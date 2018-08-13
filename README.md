# d20datascience
Data science investigations into the mechanics of the world's greatest role playing game.

## Introduction

Role-playing games offer participants the opportunity to engage in a shared storytelling experienced governed by a complex set of rules. Players assume the role of characters with both described and quantified attributes. A game master applys and interprets the rules to determine the results of a character's decisions and behaviour, often based on these attributes. Dice are used as part of the rules to simulate the statistical likelihood of success or failure.

The purpose of the game is to give players the opportunity to interact with other beings, explore their environment, and engage in combat against foes. Because players are given free choice in relation to their character's behaviour, it is impossible to simulate the full range of possible experiences within a closed system. This may be one factor in the enduring popularity of role-playing games despite advances in the realism of video game technology.

The role of the game master therefore is a mix of science and art. For example, it is important for a game master to be able to create challenges for the players so that their accomplishments and progress feels earned. Quantified character levels and creature challenge ratings are used to pit a group of characters against a group of creatures to ensure an appropriate challenging during combat. 

A combat encounter that is too easy can be boring; one that is far too difficult can result in the death of one or more players' characters. While such encounters can be deployed strategically in service to the narrative, when they occur unintentionally they can be frustrating to both the players and the game master. It is therefore important for a game master to have a realistic understanding of combat effectiveness in order to create encounters that are appropriately challenging and intentional.

However, even within the scope of combat, one subset of the role-playing experience, an encounter can include variables such as: unique terrain features, the starting distance of the encounter, complex spells, powerful magic items, and the relative experience of the game master and players in these types of combat situations. It is therefore impossible to fully simulate all of the factors involved either mathematically or computationally.

That said, while the fundamental mechanic of melee combat is somewhat more complicated to describe mathematically, it is relatively straightforward to simulate computationally. By repeatedly simulating this mechanic between a variety of character and creature configurations, we are able to determine the relative effectiveness of such configurations in relation to this fundamental mechanic.

This fundamental mechanic may also serve as a useful proxy for combat effectiveness within the real, complex game. Assuming this is true, we can make deductions about the relative influence of various configutation elements on combat effectiveness within the actual game. Doing so can, for example, help us to gauge the accuracy of the current level and challenge rating system.

The aim therefore is to use compuational analysis to provide useful insights to game masters seeking to exploit a deeper understanding of the components of combat effectiveness in service to creating combat encounters that are informed, intentional, and appropriate to the circumstances of their game.

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

## Methodology

Round-robin simulations of combat were performed in various contests between assembled characters and creatures to determine rankings of combat effectiveness for each entity configuration in relation to the **fundamental mechanic**. The results were then analysed using regression analysis and machine learning (k-nearest-neighbours) to make conclusions about the relative influence of various configutation elements on combat effectiveness in relation to the fundamental mechanic. These results were then interpreted by an experienced game master to provide insights into how these findings might be applied by game masters in relation to the actual, more complex game.

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

While [v5.1 of the System Reference Document (SRD)](http://dnd.wizards.com/articles/features/systems-reference-document-srd) provides several hundred pre-configured creatures, the distribution of creatures across challenge ratings is heavily skewed toward the lower values.



Creating our own **generic creatures** and **generic characters** is therefore more useful to our investigations.

## Sources

[Jupyter notebook containing all experiments](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb)

Data set based on the [SRD 5.1 licensed under OGL v.01a](https://dnd.wizards.com/articles/features/systems-reference-document-srd)

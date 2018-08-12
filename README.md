# d20datascience
Data science investigations into the mechanics of the world's greatest role playing game.

## Introduction

Role-playing games offer participants the opportunity to engage in a shared storytelling experienced governed by a complex set of rules and their interpretations. Players assume the role of characters with both described and quantified attributes. A game master applys and interprets the rules to determine the results of a character's decisions and behaviour based on these attributes. Dice are used as part of the rules to simulate the statistical likelihood of success or failure.

The purpose of all of this is to give players the opportunity to interact with other beings, explore their environment, and engage in combat against foes. Because players are given free choice in relation to their character's behaviour, it is impossible to simulate the full range of possible experience within a closed system.

At the same time, it is important for a game master to be able to create challenges for the players so that their accomplishments and progress feel earned. Levels and challenge ratings are used to allow a group of characters to fight against a group of creatures in ways that are appropriately challenging to the players.

Even within the scope of combat, one subset of the role-playing experience, it is impossible to simulate all of the factors involved, which can include variables such as: terrain features such as defensive cover, the starting distance of the encounter, spells, magic items, and the relative experience of the game master and players in strategic combat situations.

## Definition of Terms

 - **Contest** - a round-robin simulation pairing every creature with every other creature in a single match to determine rankings based on the total number of matches that each creature has won (survived)
 - **Match** - a simulation of repeated fights to the death between two creatures, performed for a predetermined number of rounds
 - **Round** - a simulation of a fight to the death between two creatures, determined by rolling for initiative to determine which creature goes first and then alternating between attacks where one is the attacker and one is the defender for as many turns as required until one of the creatures drops to zero hit points or below and is dead, whereupon the survivor wins
 - **Turn** - a simulation of an attack by one creature on another

A **contest** therefore has a number of **matches** equal to the nth triangular progression for the number of creatures participating in the contest minus one (since creatures do not fight themselves). It also therefore has a number of total **rounds** equal to the number of matches times the predetermined number of rounds per match. A reasonable number of rounds are performed to reduce the possibility of a few number of lucky (or unlucky) rolls causing skewed results. The number of **turns** is determined as a function of the number of rounds, but is determined by how many turns are required by each pairing of creatures in a round to determine a victor.

Therefore for any non-trivial contest, in general:

`num_turns > num_rounds > num_matches`



## Sources

[Jupyter notebook containing all experiments](https://nbviewer.jupyter.org/github/cyberscribe/d20datascience/blob/master/Creature%20Contest.ipynb)

Data set based on the [SRD 5.1 licensed under OGL v.01a](https://dnd.wizards.com/articles/features/systems-reference-document-srd)

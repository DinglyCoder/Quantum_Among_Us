# Fall CS378 Midterm Project: Quantum Among Us
### Akshat Kumar, Joseph Suess

## Background and rules
Among Us was released in late 2018, but did not boom until mainstream popularity in 2020. It is a simplistic game of social deduction in a spaceship setting. The game is easy to understand. Every player, but one, is a crewmate aboard a ship with certain tasks to complete while trying to stay alive. However one of the players is an imposter. The imposter’s mission is to kill all the crewmates without getting caught. Crewmates must therefore avoid death and deduce who they believe the imposter is so that they can eject them from the spaceship. The game ends when the imposter has been discovered or every crewmate has been killed. The Quantum Implementation does not derail significantly from the core game. Every player, at the start of the game is told their role, which is chosen at random. Only 1 imposter is allowed. Let n be the number of players, including the imposter. 

The game supports as many players as necessary but it is recommended to have at least 6. The game can be played on one laptop which is passed around the players who are sitting in a circle. Players must conceal what is displayed on their turn to not reveal whether they are an imposter or not. 

There are two phases to each round of the game. The night phase where the imposter chooses a victim to kill and the day phase where the crewmates vote on who they believe the imposter is. Therefore, there are two circuits, a circuit to keep track of the alive player’s as well as another that refreshes every round to vote out a corresponding player. Both circuits contain n quantum qubits as well as n classical bits. Every player begins in the ∣1⟩ which represents being alive. If that state is flipped to the ∣0⟩ state then that means that they are dead. This means that players could be in a superposition between dead and alive. The voting circuit starts with all n qubits in the default ∣0⟩ state.

A night phase begins in a round-robin fashion where crewmates are told to type 2 random numbers, to simulate a task and emulate the busy-work that crewmates perform in the real game. The imposter, however, is given two choices on their turn on the computer; they could either kill another player or kill two players in one round. To kill a single player, the circuit adds a controlled not gate with the target qubit as the victim and the control qubit as the imposter. This flips the target qubits state to 0 which “kills” them. The catch for the latter option is that the imposter themselves has a 50% probability to die instead of the two targets. In this scenario, the imposter sells his soul to the devil to put himself in a superposition between alive and dead. He performs the killing action on two unsuspecting crewmates entangling them both with his superposition and placing them in a mortal coil. The fate of the 2 crewmates are now linked with each other and the imposter. This can be demonstrated with the following example:

## Explanation of Entanglement Feature
Suppose there are three players and the imposter (qubit 0) chooses to attempt to kill qubit 1 and 2 in the same round. Since all the states are initialize in the 1 state, the circuit will look like this:

<img width="376" alt="Screen Shot 2024-12-07 at 8 12 13 PM" src="https://github.com/user-attachments/assets/1b4e41cb-6e23-4b9a-bc4f-61005c3b5942">

The old state of ∣111⟩ is now transformed to the new state: $\frac{| 110 \rangle - | 001 \rangle}{\sqrt{2}}$. When collapsed, this means that there are two outcomes, the imposter is killed and the two crewmates live, or the crewmates die and the imposter survives. Collapsing these superpositions is done by measuring the circuit after a simulation with 1024 shots. Whichever result had the higher outcome, is the final state. The dead player is announced and we check if the game has ended or not. 

## Explanation of Interference Feature
The night phase is followed by the day/voting phase where every player is given the opportunity to vote on who they believe the imposter is. When one votes on another player, that player's qubit is modified by a phase shift of π/n.  Using the principle of Quantum Interference, these phase shifts from different player’s votes will positively interfere to add up to a total phase which represents the number of votes a certain qubit receives. To measure this interference, we first put the qubits in a superposition with a Hadamard gate. This puts the qubit in the following state:
$\frac{| 0 \rangle - | 1 \rangle}{\sqrt{2}}$
Since the phase gate is defined by the following gate:

<img width="153" alt="Screen Shot 2024-12-07 at 8 29 20 PM" src="https://github.com/user-attachments/assets/1fa6c09a-25e5-4ad8-a84e-c7d96b1559e5">


An application of the P gate with lambda = π/n will affect the state in the following way:
\frac{| 0 \rangle - e^{i\pi/n}| 1 \rangle}{\sqrt{2}}
Afterwards, a second H gate is applied to undo the superposition and interfere the different phases. This creates the following state
$\frac{1}{2}\left [ (1+e^{i\pi/n})|0\rangle (1-e^{i\pi/n})|1\rangle \right ]$
We know that when measuring qubits, the outcome’s probability is defined as:
Prob[0] = \left|\frac{(1+e^{i\pi/n})}{2} \right|^{2}
Prob[1] = \left|\frac{(1-e^{i\pi/n})}{2} \right|^{2}

Therefore, a simulation which measures the outcome of 1024 shots will likely show a distribution of measurements that represent these probabilities. We can use this idea over multiple qubits where the qubit with the largest phase will have the highest probability of being 1. We have now created a way to use Quantum Interference to create a voting system. Ties on player votes are broken by Quantum Randomness where it is unlikely that two qubits will have the exact same number of counts. If that were to happen a simple coin toss determines the unlucky player to be eliminated. However, the imposter has another trick up their sleeve, At the start of the game, the imposter is given n // 3 sabotages. If the imposter chooses to use their sabotage, they too, will get a vote, potentially skewing the results without the others knowing! 

## How to Win
These rounds continue until the game ends with an Imposter or crewmate win. This game is a game of social deduction, so the crewmates greatest strength is their attentiveness in detecting suspicious acts from other players. The imposter’s strategy is to withhold their sabotages until the later stages of the game. Since people aren’t likely to vote on you in the beginning, there is no need to muddy the vote. However, a sabotage should not always be used in situations where there is only 1 crewmate since the result is a 50/50 regardless of whether the sabotage was used or not.

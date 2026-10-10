# Codenames
Multiplayer online game based on the board game, to replace Discord's game

The primary goal of this project is to practically learn React.js, in addition to learning more about how Node.js practically works when messages are passed from a client to impact several other clients.

# The Idea

A Host stands for logic of game initiation, including game options and optional custom words. During this time, many other players may enter the lobby using an ID-Code from Host. 

Server stands responsible for: 
1) Board creation         : (random from word-list + custom words)
2) Evaluation of guesses  : Operatives send their guesses to the server, and is returned a correct/opposite/citizen/assassin in response. 
3) Role divisions         : When game is initialized, the host sends a mapping of their planned teams. This must be saved in the server for their game instance, but spymasters must receive the answer sheet during initialization. 
4) Broadcasting           : actions sent from clients must reach all other clients

As in the original online game, there should be no time limit to influence the players actions. Though, there should be a timeout if a disconnected player fails to return within a 2 minute period. 

The following idea is described as a Client-Server connection. If necessary, might discuss this as a Peer-To-Peer connection, but that may require much more concurrency validation.

# During lobby

## The Host
A lobby is created by the Host, returns a unique lobby code. The Host may be a subclass of the Player-class, as once a game is started they behave identical to all other players. 

## The Players
Players use the lobby code to join a lobby, with a specified nickname -> How to handle duplicates? Add "The Cooler " in front of an occupied nickname? 

Players may toggle between the following roles: 
* Red/Blue Spymaster   : Exactly 1 per team
* Red/Blue Operative   : At least 1 per team, at max 5 per team.
* Spectators           : Unlimited amount? Maximum 6-7?

The players should receive some tutorial or way to read the rules.

# To start the game

The server must receive from the host: 
* Roles mapped to nicknames
  * Though Discord allows for these to change during a game, I want these to last. 
* Chosen game options:
  * Custom word list (optional)

# During the game

Spy-masters gives clues: Send string+int to server, to be broadcast as a clue to all players

Operatives evaluates clues: 
1) Highlight cards  : Minor animation to indicate a players wants to bring attention to their team of this card. Marked by index(row, col). Broadcast by server to all players.
2) Place a guess    : Evaluation if the team's guess matches the card's color -> Logic for server to handle

## A round

From the start of the game:
1) Server decides by a coin toss which team should receive one more card. This team is the starting team.
2) The Spymaster inputs a String (max 30? characters) and an int (0-9, or INF) to act as a Clue. Spymaster-client sends this to the server.
3) Server receives String+Int with from client. Server checks ID with their division of roles mapped to the IDs, and rejects unexpected messages.
4) Server broadcasts the String+Int+Team to all players. Marks who's turn it is to guess.
5) Clients receiving the message displays it for all players.
6) The current Team's operatives discuss the clue, and may highlight cards. Index's of the cards are sent to the server, and broadcast to be displayed on all screens. 
7) While the operatives guess correct and has more than 0 remaining cards to be guessed, for a maximum of (int+1)
   1) An Operative-client sends a Guess to the server
   2) The Server rejects unexpected messages from unknown or invalid IDs.
   3) The Server evaluates if the guess is correct/opposite/citizen/assassin
   4) The Server updates the game state. If the assassin was guessed, TEAM LOSSES!
   5) The answer is broadcast
8) If neither team has reached 0 remaining cards to be guessed, repeat from step 2

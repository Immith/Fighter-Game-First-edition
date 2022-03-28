#include <iostream>
#include <cmath>
#include <ctime>
#include <string>
#include "Fighter.h"

using namespace std;
string roadblock = "*****************************************************************";

class Fighter {
private:
	bool isLive = 1;
	bool haveBoots = 0;
	string name;
	int stamina = 0;
	int health = 100;
	int perception = 0;
	int strength = 0;
	int agility = 0;
	int defence = 0;
	int constitution = 0;
	int Gold = 20000;
	string* otherPName = NULL;
	string* otherPInt = NULL;

public:

	int GetStamina() { return stamina; }
	void SetStamina(int x) { stamina = x; }
	int GetHealth() { return health; }
	void SetHealth(int x) { health = x; }
	int GetPerception() { return perception; }
	void  SetPerception(int x) { perception = x; }
	int GetStrength() { return strength; }
	void SetStrength(int x) { strength = x; }
	int GetAgility() { return agility; }
	void SetAgility(int x) { agility = x; }
	int Getdefence() { return defence; }
	void Setdefence(int x) { defence = x; }
	int GetConstitution() { return constitution; }
	void SetConstitution(int x) { constitution = x; }
	int GetGold() { return Gold; }
	void SetGold(int x) { Gold = x; }
	string GetName() { return name; }
	void SetName(string x) { name = x; }
	bool GetLiveOrDead() { return isLive; }
	void Kill() { isLive = false; }
	bool GetBoots() { return haveBoots; }
	void SetBoots(bool x) { haveBoots = x; }

	void DealDamage(int damage) { health -= damage; }
	void LoseStamina(int stamLoss) { stamina -= stamLoss; }
	void GainStamina(int stamGain) { stamina += stamGain; }
};

int GameStart();
void Stats(Fighter &fighter, int playerNumber);
void Actions(Fighter &fighter);
bool CheckIfValueIsCorrect(int min, int max, int value);
bool CheckIfValueIsCorrect(int min, int max, int otherValue, int value);
void ActionList(Fighter& fighter);
void KillFighter(Fighter& fighter, int& numberOfLivePlayers);
void ActionLookForGold(Fighter &fighter);
bool ActionHeal(Fighter& fighter);
void ActionShop(Fighter& fighter);
void ActionTrain(Fighter& fighter);
void ActionDefend(Fighter& fighter);
void ActionRest(Fighter& fighter);



int main()
{
	Fighter* players;
	int numberOfPlayers;
	int numberOfLivePlayers;
	numberOfPlayers = GameStart();
	players = new Fighter[numberOfPlayers];
	numberOfLivePlayers = numberOfPlayers;

	for (int times = 0; times < numberOfPlayers; times++) {
		Stats(players[times], times+1);
	}
	

	while(numberOfLivePlayers>1)
		for (int x = 0; x < numberOfLivePlayers; x++) {
			Actions(players[x]);

		}
	
}


void PlayerTurn(int playerNumber, Fighter* players) {
	//players[playerNumber].GainStamina;

}

void Actions(Fighter &fighter) {
	bool finished=0;
	char actionTaken;
	while (!finished){
	while (true) {
		ActionList(fighter);
		cin >> actionTaken;
		/*if (actionTaken != 'h' & actionTaken != 'H' & actionTaken != 'a' & actionTaken != 'A' & actionTaken != 'l' & actionTaken != 'L' & actionTaken != 's' & actionTaken != 'S' & actionTaken != 't' & actionTaken != 'T' & actionTaken != 'd' & actionTaken != 'D' & actionTaken != 'r' & actionTaken != 'R') {
			//Kollar om det man skrivit in är acceptabelt
			cin.clear();
			cin.ignore();
			cout << "That is not a valid answer. Try again." << endl;
		}
		else{
		break;
		}
		*/
		break;
	}

	switch (actionTaken) {
	case 'h': case 'H':
		if (ActionHeal(fighter)) {
			finished=true;
		}
		break;
		case 'a': case 'A':
			break;
		case 'l': case 'L':
			ActionLookForGold(fighter);
			finished = true;
			break;
		case 's': case 'S':
			ActionShop(fighter);
			finished = true;
			break;
		case 't': case 'T':
			ActionTrain(fighter);
			finished = true;
			break;
		case 'r': case'R':
			ActionRest(fighter);
			finished = true;
			break;
		case'd':case'D':
			ActionDefend(fighter);
			finished = true;
			break;

		}
	
	}
	cout << endl << roadblock << endl << endl;
}

void ActionList(Fighter& fighter) {
	cout << fighter.GetName() << ", what action do you want to take?"
		<< endl << "(h) Heal, you lose lose stamina but regain health"
		<< endl << "(a) Attack, attack another player"
		<< endl << "(l) Look for gold, find some gold to use in the shop"
		<< endl << "(s) Shop, shop for items, costs gold"
		<< endl << "(t) Train a stat, increase a stat by a few points"
		<< endl << "(d) Defend, increase defence permanently, meaning you take less damage"
		<< endl << "(r) Rest, regain stamina" << endl;
}

void ActionLookForGold(Fighter &fighter) {
	srand(time(0));
	int goldGain;
	goldGain = rand() % 20 + fighter.GetPerception() / 4;
	fighter.SetGold(fighter.GetGold() + goldGain);
	cout << "You found " << goldGain << " Gold!" << endl << "You now have " << fighter.GetGold() << " Gold." << endl;
}

void ActionDefend(Fighter& fighter) {
	srand(time(NULL));
	int random = rand() % 10;
	fighter.Setdefence(fighter.Getdefence() + (fighter.GetPerception() / 5 + random));
	cout << "You build some defences, and permently increase your defense by " << fighter.GetPerception() / 5 + random << " points" << endl << "Your new defense is: " << fighter.Getdefence() << endl << endl << roadblock << endl << endl;
}

bool ActionHeal(Fighter &fighter) {
	int healthGain=0;
	if (fighter.GetStamina() < 15) {
		cout << "You need at least 15 stamina to heal, you have " << fighter.GetStamina() << ", but you can take another action" << endl << roadblock << endl << endl;
		return false;
	}
	else {
		healthGain = fighter.GetConstitution() / 3;
		cout << "You lost 15 stamina and gained " << healthGain << " Health.";
		fighter.LoseStamina(15);
		fighter.SetHealth(fighter.GetHealth()+healthGain);
		cout << "Health: " << fighter.GetHealth() << endl << "Stamina: " << fighter.GetStamina() << endl << endl;
		return true;
	}
}

void ActionTrain(Fighter& fighter) {
	char stat;
	int random;
	srand(time(NULL));
	while (true) {
		random = rand() % 20 + 10;
		cout << "What stat do you want to train: (p) Perception, (c) Constitution, (s) Strength, or (a) Agility?" << endl;
		cin >> stat;
		switch (stat)
		{
		case 's':case'S':
			fighter.SetStrength(fighter.GetStrength() + random);
			cout << "Your strength was increased by " << random << " points. It is now " << fighter.GetStrength() << endl;
			return;
		case 'p':case'P':
			fighter.SetPerception(fighter.GetPerception() + random);
			cout << "Your perception was increased by " << random << " points. It is now " << fighter.GetPerception() << endl;
			return;
		case 'c':case'C':
			fighter.SetConstitution(fighter.GetConstitution() + random);
			cout << "Your constitution was increased by " << random << " points. It is now " << fighter.GetConstitution() << endl;
			return;
		case 'a':case'A':
			fighter.SetAgility(fighter.GetAgility() + random);
			cout << "Your agility was increased by " << random << " points. It is now " << fighter.GetAgility() << endl;
			return;
		}
		cout << "That is not a valid input, try again." << endl << endl;
	}
}

void ActionRest(Fighter& fighter) {
	srand(time(NULL));
	int random=rand()%20+20;
	fighter.SetHealth(fighter.GetHealth() + random);
	fighter.SetStamina(fighter.GetStamina() + random);
	cout << "Ah, a nice refreshing rest, it feels great. Your stamina and health were íncreased by " << random << " points" << endl;

}

void ActionShop(Fighter &fighter) {
	bool leave=0;
	char item;
	cout << "Hello, and welcome to the shop! We have a variety of items for sale." << endl;
	if (fighter.GetGold() < 100) {
		cout << "You look kind of poor now that I think about it. You better leave. (You have too little gold, but you can do something else with your turn, it is not wasted. Feel free to come back to the shop when you have 40 gold.)" << endl;
		//return false;
	}
	else {
		while ((!(fighter.GetGold() < 100)) & (leave == false)) {
			cout << "Gold: " << fighter.GetGold() << endl
				 << "(s) Potion of strength: This potion increases your strength by 40. Cost: 100" << endl
			 	 << "(a) Potion of agility: This potion increases your agility by 40.   Cost: 120" << endl
			 	 << "(b) Boots of fancyness: These boots are hella fancy.               Cost: 1000" << endl
			 	 << "(l) Leave: leave the shop" << endl << endl;
			cin >> item;
			switch (item) {
			case 's': case'S':
				if (fighter.GetGold() >= 100) {
					fighter.SetStrength(fighter.GetStrength() + 40);
					fighter.SetGold(fighter.GetGold() - 100);
					cout << "Your strength is now: " << fighter.GetStrength() << endl;
					break;
				}
				else {
					cout << "You cannot afford that, leave the shop." << endl;
						break;
				}

			case 'a': case 'A':
				if (fighter.GetGold() >= 120) {
					fighter.SetAgility(fighter.GetAgility() + 40);
					fighter.SetGold(fighter.GetGold() - 120);
					cout << "Your agility is now: " << fighter.GetAgility() << endl;
					break;
				}
				else {
					cout << "You cannot afford that, please buy something else, or leave the shop." << endl;
					break;
				}
			case'b': case'B':
				if (fighter.GetGold() >= 1000) {
					fighter.SetBoots(1);
					fighter.SetGold(fighter.GetGold() - 1000);
					cout << "Wow, such boots" << endl;
					break;
				}
				else {
					cout << "You cannot afford that, please buy something else, or leave the shop." << endl;
					break;
			case'l':case'L':
				leave = 1;
				}

			}

		}
	}
}

int GameStart() {
	int numberOfPlayers;
	int minPlayers = 2;
	int maxPlayers = 5;
	cout << "Hello, and welcome to the fighter game (not TM). In this game you will take turns and try to defeat the other players." 
	<< endl << "The game will begin with you choosing stats, then everyone will take turns starting with player one. The last fighter standing wins. " << endl << endl;
	while (true) {
		cout << "How many players are there? (2 to 5 players may play.)" << endl;
		cin >> numberOfPlayers;
		if (!(numberOfPlayers <= 5) || !(numberOfPlayers >= 2)) {
			cin.clear();
			cin.ignore();
			cout << "That is not a valid answer, try again!" << endl;
		}
		else {
			return numberOfPlayers;
		}

	}
}

void Stats(Fighter &fighter, int playerNumber) {
	int stats;
	string name;
	int statPoints = 300;
	cout << "Player " << playerNumber << endl << "You have a maximum of 300 points to distribute among your stats, you must not use all points, and you may not put more than 100 points in each stat. The stats are: Strength, defence, agility, perception and constitution." << endl << endl;
	cout << "What is your name?" << endl;
	cin >> name;
	fighter.SetName(name);
	while (true) {
		cout << "What do you want your Strength to be? Your strength increases your damage." << endl;
		cin >> stats;
		if (CheckIfValueIsCorrect(0, 100, statPoints, stats)) {
			statPoints -= stats;
			fighter.SetStrength(stats);
			break;
		}
	}
	while (true) {
		cout << "What do you want your defence to be? Your defence makes you take less damage when you are hit." << endl;
		cin >> stats;
		if (CheckIfValueIsCorrect(0, 100, statPoints, stats)) {
			statPoints -= stats;
			fighter.Setdefence(stats);
			break;
		}
	}
	while (true) {
		cout << "What do you want your Agility to be? Your agility makes you harder to hit.." << endl;
		cin >> stats;
		if (CheckIfValueIsCorrect(0, 100, statPoints, stats)) {
			statPoints -= stats;
			fighter.SetAgility(stats);
			break;
		}
	}
	while (true) {
		cout << "What do you want your Perception to be? Your percption increases your chance to hit others." << endl;
		cin >> stats;
		if (CheckIfValueIsCorrect(0, 100, statPoints, stats)) {
			statPoints -= stats;
			fighter.SetPerception(stats);
			break;
		}
	}
	while (true) {
		cout << "What do you want your constituion to be? Your constitution increases your stamina regain, which you need to attack." << endl;
		cin >> stats;
		if (CheckIfValueIsCorrect(0, 100, statPoints, stats)) {
			statPoints -= stats;
			fighter.SetConstitution(stats);
			break;
		}
	}
	cout << fighter.GetName() << ":" << endl << roadblock << endl <<
		"Strength: " << fighter.GetStrength() << endl <<
		"defence: " << fighter.Getdefence() << endl <<
		"Agility: " << fighter.GetAgility() << endl <<
		"Perception: " << fighter.GetPerception() << endl <<
		"Contitution: " << fighter.GetConstitution() << endl << 
		"Unused points: " << statPoints << endl <<roadblock << endl << endl;
}

bool CheckIfValueIsCorrect(int min, int max, int value) {
	if (!(value<=max) || !(value>=min)) {
		cout << "That answer is not valid. Try again." << endl;
		return false;
	}
	else {
		return true;
	}
}

bool CheckIfValueIsCorrect(int min, int max, int otherValue, int value) {
	if (!(value>=min) || !(value<=max) || !(value<=otherValue)) {
		cout << "That answer is not valid. Try again." << endl;
		return false;
	}
	else {
		return true;
	}
}

void KillFighter(Fighter& fighter, int& numberOfLivePlayers) {
	fighter.Kill();
	numberOfLivePlayers--;
}

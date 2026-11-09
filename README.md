# quiz-game-code.cpp-25BCD10285-
Quiz / Number Guessing Game with SMART LEARNING MODE - OOP Mini Project (C++)// UID: 25BCD10285// Concepts used: classes, encapsulation, inheritance (multi-level),//                polymorphism (virtual functions), vectors #include <iostream>
#include <string>
#include <vector>
#include <cstdlib>
#include <ctime>
using namespace std; 
// ---------- Player class ----------
class Player {private:    
string name;    
int score;public:   
 Player(string n = "Player") : name(n), score(0) {}   
 void addScore(int s) { score += s; }   
 int getScore() const { return score; }    string getName() const { return name; }}; 
// ---------- Base class (abstract) ----------
class Game {protected:   
 Player player;public:    
Game(const Player &p) : player(p) {}    virtual void play() = 0;         
 // pure virtual -> polymorphism  
  virtual ~Game() {}    
void showResult() const {       
 cout << "\n" << player.getName() << ", your final score: "          
   << player.getScore() << "\n";  
  }}; 
// ---------- Number Guessing Game ----------
class NumberGuessGame : public Game {public:    NumberGuessGame(const Player &p) : Game(p) {}     void play() override {      
  int secret = rand() % 100 + 1;    
    int guess, attempts = 0;      
  const int maxAttempts = 7;       
  cout << "\n--- Number Guessing Game ---\n";        cout << "Guess the number between 1 and 100 (" << maxAttempts << " tries).\n";         while (attempts < maxAttempts) {           
 cout << "Attempt " << attempts + 1 << ": ";            cin >> guess;            
attempts++;             
if (guess == secret) {             
   int points = (maxAttempts - attempts + 1) * 10;                
cout << "Correct! You earned " << points << " points.\n";                player.addScore(points);                showResult();              
  return;        
    } else if (guess < secret) {              
  cout << "Too low!\n";          
  } else {              
  cout << "Too high!\n";        
    }       
 }       
 cout << "Out of tries! The number was " << secret << ".\n";      
  showResult();   
 }}; 
// ---------- Question class ----------
class Question {public:    
string text;    
string options[4];   
 int correct;          
// 1 to 4    
string explanation;   
// shown in Smart Learning Mode when answer is wrong     
Question(string t, string a, string b, string c, string d,             
int ans, string exp = "") {       
 text = t;        
options[0] = a; options[1] = b; options[2] = c; options[3] = d;       
 correct = ans;       
 explanation = exp;    
}     
// Shows the question, reads the answer, returns true if correct    
bool ask(int number) const {      
  int choice;     
   cout << "\nQ" << number << ". " << text << "\n";       
 for (int j = 0; j < 4; j++)         
   cout << "  " << j + 1 << ") " << options[j] << "\n";       
 cout << "Your answer (1-4): ";       
 cin >> choice;       
 return choice == correct;  
  }}; 
// ---------- Quiz Game ----------
class QuizGame : public Game {protected:    vector<Question> questions;public:    QuizGame(const Player &p) : Game(p) {        
questions.push_back(Question("Which is the capital of India?",         
   "Mumbai", "New Delhi", "Chennai", "Kolkata", 2,       
   
  "New Delhi is the capital of India. Mumbai is only the financial capital."));        questions.push_back(Question("Which language is used in this project?",            "Python", "Java", "C++", "HTML", 3,    
        "This project is written in C++ using classes and objects."));        questions.push_back(Question("Which OOP concept is 'one name, many forms'?",            "Inheritance", "Polymorphism", "Encapsulation", "Abstraction", 2,  
          "Polymorphism means one function name can behave differently (virtual functions)."));        questions.push_back(Question("Which symbol creates a derived class?",            "extends", "inherits", ":", "implements", 3, 
           "C++ uses a colon: class Child : public Parent. 'extends' is Java."));        questions.push_back(Question("2 + 2 * 3 = ?",            "8", "12", "10", "6", 1,            "Multiplication comes first (BODMAS): 2 * 3 = 6, then 2 + 6 = 8."));  
  }     void play() override {      
  cout << "\n--- Quiz Game ---\n";    
    for (int i = 0; i < (int)questions.size(); i++) {      
     
 if (questions[i].ask(i + 1)) {               
 cout << "Correct! +10\n";                player.addScore(10);          
  } else {               
 cout << "Wrong! Correct answer: " << questions[i].correct << "\n";          
  }      
  }       
 showResult();   
 }};
 // ---------- SMART LEARNING MODE (unique feature) ----------//
 Inherits from QuizGame (multi-level inheritance: Game -> QuizGame -> this).
// 1. Wrong answers are explained immediately.
// 2. Wrong questions are repeated in revision rounds until mastered.
// 3. A learning report shows accuracy, level and topics still weak.class SmartLearningGame : public QuizGame {private:    vector<int> weak;      
  // indexes of questions answered wrong    int firstRoundCorrect; public:    SmartLearningGame(const Player &p) : QuizGame(p), firstRoundCorrect(0) {}    
 void play() override {       
 int total = (int)questions.size();         cout << "\n--- Smart Learning Mode ---\n";        cout << "Wrong answers are explained and repeated until you master them.\n";        
 // Round 1: ask every question      
  for (int i = 0; i < total; i++) {        
    if (questions[i].ask(i + 1)) {               
 cout << "Correct! +10\n";                player.addScore(10);                firstRoundCorrect++;           
 } else {              
  cout << "Wrong! Correct answer: " << questions[i].correct << "\n";                cout << "Why: " << questions[i].explanation << "\n";               
 weak.push_back(i);           
 }      
  }       
  // Revision rounds: repeat only the weak questions (max 3 rounds)       
 int round = 1;       
 while (!weak.empty() && round <= 3) {           
 cout << "\n=== Revision Round " << round << " ("               
  << weak.size() << " question(s) to improve) ===\n";           
 vector<int> stillWeak;          
  for (size_t k = 0; k < weak.size(); k++) {               
 int idx = weak[k];              
  if (questions[idx].ask(idx + 1)) {                   
 cout << "Mastered! +5\n";                    player.addScore(5);               
 } else {                   
 cout << "Not yet. Correct answer: " << questions[idx].correct << "\n";                    stillWeak.push_back(idx);            
    }           
 }            
weak = stillWeak;          
  round++;      
  }        
 showLearningReport(total);   
 }    
 void showLearningReport(int total) const {       
 int accuracy = firstRoundCorrect * 100 / total;        
string level;       
 if (accuracy >= 80)      
level = "Expert";       
 else if (accuracy >= 50) level = "Intermediate";        
else                   
  level = "Beginner";        
 cout << "\n===== LEARNING REPORT =====\n";        cout << "First-attempt accuracy : " << accuracy << "%\n";      
  cout << "Learning level        
 : " << level << "\n";      
   if (weak.empty()) {           
 cout << "Topics to revise   
    : None - all questions mastered!\n";      
  } else {         
   cout << "Topics to revise      
 :\n";           
 for (size_t k = 0; k < weak.size(); k++)                cout << "  - " << questions[weak[k]].text << "\n";      
  }       
 showResult();   
 }}; 
// ---------- Main ----------
int main() {    
srand(time(0));    
 string name;   
 cout << "Enter your name: ";    
getline(cin, name);   
 Player p(name);     
int option = 0;    
do {       
 cout << "\n===== MAIN MENU =====\n";        cout << "1. Number Guessing Game\n";        cout << "2. Quiz Game\n";       
 cout << "3. Smart Learning Mode\n";        cout << "4. Exit\n";       
 cout << "Choose: ";       
 if (!(cin >> option)) break;      
   Game *game = nullptr;       
 if (option == 1)      
game = new NumberGuessGame(p);      
  else if (option == 2) game = new QuizGame(p);       
 else if (option == 3) game = new SmartLearningGame(p);       
  if (game) {          
  game->play();    
 // runtime polymorphism           
 delete game;       
 } else if (option != 4) {           
 cout << "Invalid choice!\n";      
  }    
} while (option != 4);     
cout << "Thanks for playing!\n";  
  return 0;} 
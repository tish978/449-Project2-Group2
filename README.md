CPSC 449-project-2
Wordle REST API

Group members:  
1. Satish Bisa  
2. Shivangi Shakya  
3. Mayur  
 


In this project, our team was able to effectively build a back-end API for a game quite similar to the popular game "Wordle", with the exception of a few
conditions that the original game is known for. Some of these features include allowing more than one game to be played per day per player, as well offering
different games to different players. 

The web service leverages Python's Quart web framework, and utilizes 6 main endpoints (described below) to simulate a user interacting with the game. 

NOTE 1: There are 2 "bonus" endpoints noted below that were used for the loading of "correct words" and "valid words" into the database at their respective 
tables.

NOTE 2: Prior to any testing, please be sure to update the "URL" value in wordle-api.toml to match the directory/file path of the rest of the project files.

NOTE 3: On line 21 of wordle-api.py ("app.config.from_file("/home/student/Documents/449-wordle/wordle-api.toml", toml.load") ensure that the URL there correctly reflects the file path location of the wordle-api.toml file, which should just be the same location of where all the other project files are. 


GETTING STARTED:  

1. TO LAUNCH THE SERVICE:  
From the command line of the project directory, simply run   
	```bash
	foreman start --formation users-api=1,games-api=3
	```  
if error: Already running process, run below steps:  
	a.	```bash
		sudo lsof -n -i :5000 | grep LISTEN
		```  
	b. Run below for every PID:  
	   ```bash
	   lsof -ti tcp:5000 | xargs kill
	   ```

2. From the command line of the project directory, run the following commands to init/populate the database:  
	```bash
	./bin/init.sh
	```

3. Once exit the foreman, follow the below steps:  
	a. Run three times:   
		```bash
		sudo umount litefs
		```  
	Ignore the errors/warnings.  
	b. Remove all the contents of the following:  
		var/primary/data  
		var/primary/mount  
		var/secondary1/data  
		var/secondary1/mount  
		var/secondary2/data  
		var/secondary2/mount


ENDPOINT 1: @app.route("/register/", methods=["POST"])
- This endpoint is used for registering a user/creating an account for a user to authenticate themselves.
- ex:   
	```bash
	http POST http://tuffix-vm/register/ user_id=310 password=absd
	```  
- After executing, the users table in the database will have a new account correlating to the user's user_id and password.

ENDPOINT 2: @app.route("/login/", methods=["POST"])
- This endpoint is used for logging in and authenticating a user, if their account already exists. 
- ex:   
	```bash
	http --auth 310:absd --auth-type basic GET http://tuffix-vm/login/
	```  
- After executing, the users table is checked to see if the specified user's account exists and authenticates if the password is correct.

ENDPOINT 3: @app.route("/create_new_game/", methods=["POST"])
- This endpoint is used for creating a new game for a specified user via user_id.
- ex:   
	```bash
	http --auth 310:absd --auth-type basic POST http://tuffix-vm/create_new_game/ user_id=15
	```  
- After executing, the games table is updated to have a new game started for the specified user. This also generates the secret word
that the user must guess in order to win the game.

ENDPOINT 4: @app.route('/answer/', methods=['POST'])
- This endpoint is used for submitting an answer to what the secret word might be, by specifying the exact game_id of the game being
played as well as the exact answer that is being used.
- ex:   
	```bash
	http --auth 310:absd --auth-type basic POST http://tuffix-vm/answer/ game_id=3 answer=lemon
	```  
- After executing, if the answer is correct the game is over as a victory. If incorrect, the guess count of that game is incremented,
And the user may use the same endpoint with a new answer value to answer again. Once the guess count reaches 6, the game ends as a 
loss for the user. 

ENDPOINT 5: @app.route("/get_games_in_progress/", methods=["POST"])
- This endpoint is used for seeing all the games that are in progress for a user based on their user_id.
- ex:   
	```bash
	http --auth 310:absd --auth-type basic POST http://tuffix-vm/get_games_in_progress/ user_id=15
	```  
- After executing, the user can see a list of all their games in progress and continue them using "ENDPOINT 4" until completion.

ENDPOINT 6: @app.route("/get_game_state/", methods=["POST"])
- This endpoint is used to see the current state of a game.
- ex:   
	```bash
	http --auth 310:absd --auth-type basic POST http://tuffix-vm/get_game_state/ game_id=19
	```  
- After executing, the user can see a list of the number of guesses they have left remaining in the game, as well as the number
of guesses they have made as a whole up to that point.

**BONUS ENDPOINT: @app.route("/word_load/", methods=["POST"])
- was used to load DB with all correct words that can be used as a secret word. Do not need to use again.

**BONUS ENDPOINT: @app.route("/valid_word_load/", methods=["POST"])
- was used to load DB with all valid words. Do not need to use again.

Full Stack Nanodegree Project - Hangman API

Link to working Google App Engine API ... 

https://apis-explorer.appspot.com/apis-explorer/?base=https://guess-a-word.appspot.com/_ah/api#p/hangman/v1/

Set-Up Instructions:

Update the value of application in app.yaml to the app ID you have registered in the App Engine admin console and would like to use to host your instance of this sample.
Run the app with the devserver using dev_appserver.py DIR, and ensure it's running by visiting the API Explorer - by default localhost:8080/_ah/api/explorer.

Game Description:

This Guess a Word (Hangman) API randomly chooses a word from a list of words, and lets the user guess the word by inputting one letter at a time. When the user makes a guess, they are notified if the letter is in the word or not. When the letter is in the word the response will show the position of the letter with astericks (*) for the undiscovered letters.  When the letter is not in the word, the response shows the incorrectly guessed letters and how many attemps are remaining. 

The number of attempts is based on the length of the word, multiplied by two. If the user does not guess the word in the alloted number of tries, they will lose the game and no score is recorded. 

The Score mechanism is as follows. When a user guesses a letter correctly, they will recieve an amount of points based on the attempts remaining and incorrectly guessed letters (Total Attempts Allowed - Number of incorrect guesses). This is done so that longer (harder) words award higher number of points, but with each wrong guess the player won't earn as many points.
The same amount of points is deducted if the player guesses incorrectly, except if the score will result in a negative number. In that case, the score will be zero. The last guess (winning guess), the player is awarded points based on the number of attempts remaining. This is so that, the less incorrect guesses and attempts it takes a player to guess the word, the more points they will earn. 

An user can either win, lose or cancel a game, so players will be ranked based on the number of wins they accumulate. 

Files Included:

    game.py: Contains endpoints and game playing logic.
    app.yaml: App configuration.
    models.py: Entity and message definitions including helper methods.
    utils.py: Helper functions

Endpoints Included:

    create_user
        Path: 'users'
        Method: POST
        Parameters: user_name
        Returns: Message confirming creation of the User.
        Description: Creates a new User. user_name provided must be unique. Will raise a ConflictException if a User with that user_name already exists.

    new_game
        Path: 'game'
        Method: POST
        Parameters: user_name
        Returns: GameForm with initial game state.
        Description: Creates a new Game. user_name provided must correspond to an existing user - will raise a NotFoundException if not. Also adds a task to a task queue to update the average moves remaining for active games.

    get_game
        Path: 'game/{urlsafe_game_key}/get'
        Method: GET
        Parameters: urlsafe_game_key
        Returns: GameForm with current game state.
        Description: Returns the current state of a game.

    make_move
        Path: 'game/{urlsafe_game_key}/move'
        Method: PUT
        Parameters: urlsafe_game_key, guess
        Returns: GameForm with new game state.
        Description: Accepts a 'guess' and returns the updated state of the game. If this causes a game to end, a corresponding Score entity will be created.

    get_scores
        Path: 'scores'
        Method: GET
        Parameters: None
        Returns: ScoreForms.
        Description: Returns all Scores in the database (unordered).

    get_user_scores
        Path: 'scores/user/{name}'
        Method: GET
        Parameters: name
        Returns: ScoreForms.
        Description: Returns all Scores recorded by the provided player (unordered). Will raise a NotFoundException if the User does not exist.
        
    get_user_rankings
        Path: 'users/ranks/{top}'
        Method: GET
        Parameters: top
        Returns: RankingForms
        Description: Get all user rankings, limited to top parameter of choosing.

    get_user_games
        Path: 'games/user/{name}'
        Method: GET
        Parameters: None
        Returns: UserGameForms
        Description: Get an user's active games

    get_high_scores
        Path: 'scores/highscores/{top}'
        Method: GET
        Parameters: number_of_results
        Returns: RankingForms
        Description: Get high scores (limited by number_of_results)

    cancel_game
        Path: 'game/{urlsafe_game_key}/cancel'
        Method: POST
        Parameters: urlsafe_game_key
        Returns: StringMessage
        Description: Cancel an active game.

    get_game_history
        Path: 'sgame/{urlsafe_game_key}/history'
        Method: GET
        Parameters: urlsafe_game_key
        Returns: StringMessage
        Description: Get a game's history (moves made so far)

Models Included:

    User
        Stores unique name

    Game
        Stores unique game states. Associated with User model via KeyProperty.

    Score
        Records completed games. Associated with Users model via KeyProperty.

Forms Included:

    GameForm
        Representation of a Game's state (urlsafe_key, attempts_remaining, game_over flag, message, user_name).
    GameHistoryForm
        Returns list of moves made through the history of the game. 
    NewGameForm
        Used to create a new game (user_name, min, max, attempts)
    MakeMoveForm
        Inbound make move form (guess).
    ScoreForm
        Representation of a completed game's Score (user_name, date, won flag, guesses).
    ScoreForms
        Multiple ScoreForm container.
    JSONMessage
        General purpose String container.
    HighScoreForm
        Used to limit the number of high scores
    HighScoreForms
        Returns multiple HighScoreForms
    RankingForm
	Representation of user and wins for ranking
    RankingForms
	Returns multiple rankings for users

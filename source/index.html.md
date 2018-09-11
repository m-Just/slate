# class *Server*
## Introduction
This class implements all data communication methods with server. We use [Firebase](https://firebase.google.cn/)
as our backend provider. We provide full backend supports, and can make minor adjustment according to your frontend
development needs. You should handle all UI events, sending request to server,
and respond to listened events, etc. For example:

  * Show a message to user if he typed any invalid character in password input field.
  * Create UI buttons, add listeners to them and send corresponding request to server, then handle the servers respond.
  * Listen on messages from the server, and show them to the user once the event is triggered.

## Examples
Here are some examples on using class *Server* to communicate with the server.
### Scenario \#1 - Sign In

> Sample code (Unity C#)

```csharp
  public Text message;
  public InputField usernameInput;
  public InputField passwordInput;

  void SignInEventHandler(res, error) {
    if (error) {
      // handles error
      message.text = "服务器错误";
    } else {
      if (res.approved == true)
        SceneManager.LoadScene("Welcome");
      else message.text = "用户名或密码错误";
    }
  }

  void SignInButtonClicked() {
    string username = usernameInput.text;
    string password = passwordInput.text;
    // TODO: validate user input

    // send sign-in request to server
    Server.SignIn(SignInEventHandler, username, password);
  }
```

Use method `Server.SignIn` to send a asynchronous sign-in request to server.

Parameter | Type | Description
--------- | ---- | -----------
handler  | void   | A function for handling the asynchronous response.
username | string | The username of the user.<br>Should be a valid phone number with country code.<br>For example, if a Chinese user's phone number is 138xxxxxxxx then the string value should be `+86138xxxxxxxx`.
<!-- password | string | The password of the user.<br>Should satisfy the following rules:<br><ul><li>a minimum of 6 characters required</li><li>no more than 15 characters</li><li>a-z, A-Z, 0-9 allowed</li><li>special characters allowed <code>~`!@#$%^&*()-_+=&#124;}]{["':;?/>.<,</code></li><li>white space allowed</li></ul> -->

#### Respond (parameters passed to the handler):

  * `bool approved`: if the sign-in request has been approved
  * `string uid`: a unique ID for this user

#### Returns

  * void

### Scenario \#2 - Game State Listening

> Sample code (Unity C#)

```csharp
  public string uid = "QYcNR3JP2Jge0vrah9LFbX9KrmQ2";
  public Text message;
  public Text pot_size;

  void GameStateHandler(res, error) {
    if (error) {
      // handles error
      message.text = "服务器错误";
    } else {
      pot_size.text = (string)res.pot_size;
      // TODO: more UI responses
    }
  }

  void Start() {
    // start listening on server game state
    Server.ListenGameState(GameStateHandler, uid);
  }
```

When user is in game, the client should listen to database events, and the UI should
respond to these events accordingly. Use `Server.ListenGameState` to listen.

Parameter | Type | Description
--------- | ---- | -----------
handler   | void | The handler of game events.<br>Triggered every time the server game state changed.
uid       | string | The uid obtained on user sign-in.

#### Respond (parameters passed to the handler):

  * `uint pot_size`: the size of the pot
  * other information such as flop card value and current player id

#### Returns:

  * `bool status`: if the listener has been successfully started

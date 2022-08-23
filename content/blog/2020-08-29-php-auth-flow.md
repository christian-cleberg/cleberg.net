+++
date = 2020-08-29
title = "PHP Authentication Flow"
description = "Build a basic PHP website by controlling the flow of user authentication."
draft = false
aliases = ["/blog/php-authentication-flow"]
+++

## Introduction

When creating websites that will allow users to create accounts, the developer
always needs to consider the proper authentication flow for their app. For
example, some developers will utilize an API for authentication, some will use
OAuth, and some may just use their own simple database.

For those using pre-built libraries, authentication may simply be a problem of
copying and pasting the code from their library's documentation. For example,
here's the code I use to authenticate users with the Tumblr OAuth API for my
Tumblr client, [Vox Populi](https://git.cleberg.io/cgit.cgi/vox-populi.git/):

```php
// Start the session
session_start();

// Use my key/secret pair to create a new client connection
$consumer_key = getenv('CONSUMER_KEY');
$consumer_secret = getenv('CONSUMER_SECRET');
$client = new Tumblr\API\Client($consumer_key, $consumer_secret);
$requestHandler = $client->getRequestHandler();
$requestHandler->setBaseUrl('https://www.tumblr.com/');

// Check the session and cookies to see if the user is authenticated
// Otherwise, send user to Tumblr authentication page and set tokens from Tumblr's response

// Authenticate client
$client = new Tumblr\API\Client(
    $consumer_key,
    $consumer_secret,
    $token,
    $token_secret
);
```

However, developers creating authentication flows from scratch will need to
think carefully about when to make sure a web page will check the user's
authenticity.

In this article, we're going to look at a simple authentication flow using a
MySQL database and PHP.

## Creating User Accounts

The beginning to any type of user authentication is to create a user account.
This process can take many formats, but the simplest is to accept user input
from a form (e.g. username and password) and send it over to your database. For
example, here's a snippet that shows how to get username and password parameters
that would come when a user submits a form to your PHP script.

**Note**: Ensure that your password column is large enough to hold the hashed
value (at least 60 characters or longer).

```php
// Get the values from the URL
$username = $_POST['username'];
$raw_password = $_POST['password'];

// Hash password
// password_hash() will create a random salt if one isn't provided, and this is generally the easiest and most secure approach.
$password = password_hash($raw_password, PASSWORD_DEFAULT);

// Save database details as variables
$servername = "localhost";
$username = "username";
$password = "password";
$dbname = "myDB";

// Create connection to the database
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
  die("Connection failed: " . $conn->connect_error);
}

$sql = "INSERT INTO users (username, password)
VALUES ('$username', '$password')";

if ($conn->query($sql) === TRUE) {
  echo "New record created successfully";
} else {
  echo "Error: " . $sql . "<br>" . $conn->error;
}

$conn->close();
```

## Validate Returning Users

To be able to verify that a returning user has a valid username and password in
your database is as simple as having users fill out a form and comparing their
inputs to your database.

```php
// Query the database for username and password
// ...

if(password_verify($password_input, $hashed_password)) {
    // If the input password matched the hashed password in the database
    // Do something, log the user in.
}

// Else, Redirect them back to the login page.
...
```

## Storing Authentication State

Once you've created the user's account, now you're ready to initialize the
user's session. **You will need to do this on every page you load while the user
is logged in.** To do so, simply enter the following code snippet:

```php
session_start();
```

Once you've initialized the session, the next step is store the session in a
cookie so that you can access it later.

```php
setcookie(session_name());
```

Now that the session name has been stored, you'll be able to check if there's an
active session whenever you load a page.

```php
if(isset(session_name())) {
    // The session is active
}
```

## Removing User Authentication

The next logical step is to give your users the option to log out once they are
done using your application. This can be tricky in PHP since a few of the
standard ways do not always work.

```php
// Initialize the session.
// If you are using session_name("something"), don't forget it now!
session_start();

// Delete authentication cookies
unset($_COOKIE[session_name()]);
setcookie(session_name(), "", time() - 3600, "/logged-in/");
unset($_COOKIE["PHPSESSID"]);
setcookie("PHPSESSID", "", time() - 3600, "/logged-in/");

// Unset all of the session variables.
$_SESSION = array();
session_unset();

// If it's desired to kill the session, also delete the session cookie.
// Note: This will destroy the session, and not just the session data!
if (ini_get("session.use_cookies")) {
    $params = session_get_cookie_params();
    setcookie(session_name(), '', time() - 42000,
        $params["path"], $params["domain"],
        $params["secure"], $params["httponly"]
    );
}

// Finally, destroy the session.
session_destroy();
session_write_close();

// Go back to sign-in page
header('Location: https://example.com/logged-out/');
die();
```

## Wrapping Up

Now you should be ready to begin your authentication programming with PHP. You
can create user accounts, create sessions for users across different pages of
your site, and then destroy the user data when they're ready to leave.

For more information on this subject, I recommend reading the
[PHP Documentation](https://www.php.net/). Specifically, you may want to look at
[HTTP authentication with PHP](https://www.php.net/manual/en/features.http-auth.php),
[session handling](https://www.php.net/manual/en/book.session.php), and
[hash](https://www.php.net/manual/en/function.hash.php).

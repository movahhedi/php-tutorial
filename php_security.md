# PHP Security Advices
Hi, I'm Shahab. We're going to go through some important points to make your php code and website secure. These points are simple, but very, verrrrrrry effective! Actually, if you didn't do these, don't even call your site safe!

**DISCLAIMER:** We developers always try our best to limit the possibility of being hacked as much as possible. but no one can be 100.00% sure.

## User Input
We always have to check and @@@@@ user input to be safe.
> Never trust the user.

### Method
Try to use `POST` instead of `GET` when you have data that you need to keep safe and (mostly) unchangable, as it is safer and not changable by a normal user.

### `isset` Function
Always check if the input exists.
```php
if (isset($_POST["username"]) && isset($_POST["password"])) {
    // Do the thing..
}
```

### No Empty Here!
Check for the input to not be empty if it shouldn't be empty (if the input is required).
This has two more-used methods. I prefer the first one.
```php
if ($_POST["username"] && $_POST["password"]) {
    // Do the thing..
}
// OR
if ( ! empty($_POST["username"]) && ! empty($_POST["password"])) {
    // Do the thing..
}
```

### NONCE, Only Once!
Sending a NONCE (Number ONCE) along with the user input. NONCE is a hashed string to check if the input is valid. It's usually based on UNIX-timestamp so we can fact-check it and limit the usablility time.
With this, we can be more sure that no not-direct @@@@@
Below are simple functions that generates a NONCE that is valid for 12 hours (I think so, I don't remember :P). by `action` I mean the act that is about to be done, like `dologin` or `dosignup`. and by `user` I mean the the user id. (if the user is logged in tho. if not, I pass `0`).
```php
// A salt for the NONCE. I keep this in a `secret.php` file. Make it long :)
define("SECRET_NONCE_SALT", "BlaBlaBlaBlaBlaBla");

function CreateNonce($action = "" , $user = "") { return substr(NonceGenerateHash($action . $user), -12, 10); }
function IsNonceValid($nonce = "", $action = "", $user = "") { return (substr(NonceGenerateHash($action . $user), -12, 10) == $nonce); }
function NonceGenerateHash($action = "", $user = "") { return md5(  ceil(time() / (86400 / 2))  . $action . $user . SECRET_NONCE_SALT); }
```

### Checking the `HTTP_HOST`
Our website has a domain name, it can be `example.com`, `subdomain.example.com`, an IP, or even `localhost`! When a php script is executed, it always has a super-variable array named `$_SERVER`. In this array, we can find `HTTP_HOST` and we can compare it with the one we expect it to be.
```php
// The domain name. don't use the protocol (like http). I keep this in a `config.php` file.
define("SITE_DOMAIN", "example.com");

if ($_SERVER["HTTP_HOST"] == SITE_DOMAIN) {
    // Do the thing..
}
```

### Checking the `HTTP_REFFERER`
The `$_SERVER["HTTP_REFFERER"]` shows where we were before (cool rhyme :D). If we hit the page directly, `$_SERVER["HTTP_REFFERER"]` won't exist. so make sure to check if it `isset`. It will have the full URL, like `https://example.com/path/to/file.php?page=5&joe=mama`.
I say only use this method if you know the script should ONLY be access from a specific previous-page. 
```php
// The domain name. don't use the protocol (like http). I keep this in a `config.php` file.
define("SITE_DOMAIN", "example.com");

if (isset($_SERVER["HTTP_REFFERER"]) && $_SERVER["HTTP_REFFERER"] == "http://" . SITE_DOMAIN . "/path/to/file.php?page=5&joe=mama") {
    // Do the thing..
}
```

### Always Sanitize + Get Vaccinated :)
Always sanitize the user input. Again, "Never trust the user". We'll have an overview of some methods. In most cases you have to sanitize inputs for specifically tho.

##### Trimming Whitespaces
`trim()` clears whitespaces from before and after the string. `[trim() on PHP Documentation](https://www.php.net/manual/en/function.trim.php)`
```php
$a = "     Hello    World   !    ";
$b = trim($temp);
// $b = "Hello    World   !";
```

##### Length Limiting
Limit the maximum and minimum length of user inputs. You probably want to keep an eye on your database as well. Based on the context, either show an error, or trim it using `substr()` on limit exceed [and tell the user]. Don't set it too limited. like allow passwords 8-64, and names 3-50, and emails 10-128. `[substr() on PHP Documentation](https://www.php.net/manual/en/function.substr.php)`
```php
if (strlen($_POST["password"]) >= 8 && strlen($_POST["password"]) <= 64) {
    // Do the thing..
}
```

##### Type & Validity Checking
Let's say our site has different pages specified by the `GET` of `?page=15`. Of course in this case we don't want anything like `?page=John`! So we always check if the input is valid. In most cases we use [RegEx](https://www.w3schools.com/php/php_regex.asp). It's brilliant for validating strings.
```php
// A RegEx example for numbers-only

```







> Did I mention you have to do all of these? :)

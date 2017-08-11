# PhishingPost

PHP Script intdended to be used during Phishing campaigns as a credentials collector linked to backdoored HTML `<form>` action parameter.
Originally this script had been developed in the following [gist](https://gist.github.com/mgeeky/32375178621a5920e8c810d2d7e3b2e5).

---

## Configuration

Firstly, one has to apply this script in backdoored form's `action` parameter, for instance like so:

```
<form [...] action="/post.php" [...]>
```

Then, this script shall be named as `post.php` to get it working. 

#### Most important configuration parameters to set:

- **`$harvest_filename`**
        Filename for harvested data. For CSV logging method, the `.csv` extension will be appended.
        Remember to keep the filename not guessable, to avoid forceful browsing against your own
        phishing box!

- **`$post_url`**
        This is target URL used for posting form (form's `action` attribute value).

- **`$resend_post_data`**
		Resend post data to the `$post_url` address? 
		If set to false, the user will be simply redirected to the `$redirect` address or if it's empty to the `$post_url` address.

- **`$wrong_password_url`**
        URL for "wrong password" message redirection (applicable only if `$password_retry` is set to more than 1). You can use here something like:
    ```
    '/login.html?wrong_pass=1' . (isset($_GET['redir']) ? '&redir=' . $_GET['redir'] : '');
    ```
    Having a `login.html` as your phish login form saved to the HTML file, one can make the HTML
    file know that it must display the _'wrong password'_ error message and also to let the
    the backend script (this very file) know where to redirect user after couple of 
    login tries (`$password_retry`)
    
    **Notice**: If left empty - the page will be simply reloaded.

- **`$redirect`**
        Target address to redirect victim to after collecting his input data, in case of an **error** or 
        when `$resend_post_data = false`. In other words, if we don't want to transmit data to the
        target (or we are unable to do so) this variable states where to redirect user after grabbing his POST data.
        
    **Notice**:
    You can dynamically overwrite this variable by using `$_GET['redir']`
    If You leave this empty (default), script will use `$post_url` value


One can also specify something like this in his `action` parameter value:
```
<form [...] action="/post.php?redir=https://TARGET.SITE/<?php echo $_SERVER['REQUEST_URI'];?>">
```

To make the PHP construct GET `redir` parameter denoting where to move the user after he has submitted his credentials (_not to confuse with where to POST credentials after logging them_).

Then this script could be configured as follows:
```$redirect = "";
$post_url = "https://TARGET.SITE/LOGIN-FORM-ACTION";
```

The POST data collected from the phished form will then be transmited to the `LOGIN-FORM-ACTION`, whereas the `$redirect` will take the value of `$_GET['redir']` and move the user to the same page as he came from upon some error or after stealing data and deciding not to transmit them along (`$resend_post_data = false`). This `$_GET['redir']` way is an example of creating redirection address in a dynamic manner.

When crafting HTML login page, one can use the PHP session variable: 

```
$_SESSION['phished_already']
```
to add forced redirection to the target site in his HTML files through the inline PHP code.

---


### How it works?

Having phished for instance login form of the target website, this login form will have it's action attribute changed to direct into this very script. Then, the script will collect the `$_POST` data and store them in file (in a `print_r` format, `csv` list or both, depending on setting `$log_format`). This script also takes care of issuing the warning message of having typed wrong password (or rather of redirecting the user onto file capable of displaying such message, via `$wrong_password_url` variable). Then, the script keeps _wrong passwords_ typed numer in a session variable to detect whether specific user has been phished already and he should be redirected to the **target site** immediately (in order to prevent him looking at the webpage and inspecing it). 


---

### Authors

- Mariusz B. / mgeeky
- Jakub M. / [unkn0w](https://github.com/unkn0w)

---


### Changelog

 - **v0.1** - init
 - **v0.2** - added metadata gathering
 - **v0.2.1 **- unkn0w adds redirection to faked 'wrong password' message
 - **v0.3** - added CSV reporting method
 - **v0.4** - unkn0w have added redirection facility and improved nested POST arrays handling
 - **v0.5** - unkn0w added support for $post_url variable to support pages that have separate URLs for login form and login backend

---

### TODO:

- Refactor the code to make it more readable and configurable



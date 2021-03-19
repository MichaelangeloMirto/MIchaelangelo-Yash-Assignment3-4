# MIchaelangelo-Yash-Assignment3-4
Fixing PHP Blog
Below is the new /templates/page_header.php file for CSRF


<?php
//create new session
        session_start();
        include("config.php");
        include("lib/db.php");

/*Function to check the csrf token. If the session and post
tokens are not the same it will send user to error page */
function check_csrf(){

if($_REQUEST["csrf_token"] !== $_SESSION["csrf_token"]){

unset($_SESSION["csrf_token"]);
die("CSRF token validation failed");

}
return true;
}

/*Function generates a new csrf token. If a token is not created in the session, 
it will generate a new token and store it in the session. If the session token is already
created it closes the session writing and doesnt allow further processing.  */
function generate_csrf_token(){

if(!isset($_SESSION["csrf_token"])){

$token = md5(microtime());
$_SESSION["csrf_token"] = $token;

}else{
session_write_close();

}
return $token;
}
?>


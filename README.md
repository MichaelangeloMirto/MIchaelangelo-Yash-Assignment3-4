# MIchaelangelo-Yash-Assignment3-4
Fixing PHP Blog
Below is the new /login.php file for stopping against sql injection

<?php
include("templates/page_header.php");


//$pattern = '/[^\,\.\*\=\(\)\!\]*/';
if(($_SERVER['REQUEST_METHOD'] == 'POST') &&
 (check_csrf()))  { 
//if($_SERVER['REQUEST_METHOD'] == 'POST')  {

	$result = authenticate_user($dbconn, $_POST['username'], $_POST['password']);

	if (pg_num_rows($result) == 1) {
		$_SESSION['username'] = $_POST['username'];
		$_SESSION['authenticated'] = True;
		$_SESSION['id'] = pg_fetch_array($result)['id'];	
		//Redirect to admin area
       		header("Location: /admin.php");

/*The code below wil log all successful logins into the application recording
the date, time and username. In the event that somebody uses sql injection to bypass
you can detect the username is not the correct one. Furthermore this can be implemented
for failure as well*/
$date = new DateTime();
$date = $date->format("y:m:d h:i:s");
$log_file_data = $log_filename ." " . $date .
 " " . $_POST['username'] ."\n";
error_log($log_file_data, 3, "./loginfo.log");
}
} 
?>
<!doctype html>
<html lang="en">
<head>
	<title>Login</title>
	<?php include("templates/header.php"); ?>
<style>

.form-signin {
  width: 100%;
  max-width: 330px;
  padding: 15px;
  margin: 0 auto;
}

.form-signin .form-control {
  position: relative;
  box-sizing: border-box;
  height: auto;
  padding: 10px;
  font-size: 16px;
}

.form-signin .form-control:focus {
  z-index: 2;
}

.form-signin input[type="email"] {
  margin-bottom: -1px;
  border-bottom-right-radius: 0;
  border-bottom-left-radius: 0;
}

.form-signin input[type="password"] {
  margin-bottom: 10px;
  border-top-left-radius: 0;
  border-top-right-radius: 0;
}
</style>
</head>

<body>
	<?php include("templates/nav.php"); ?>
	<?php include("templates/contentstart.php"); ?>
/* In the input field for both username and password we made sure that the textbox
only accepts usernames and passwords that contain lowercase, uppercase and numbers. It 
will not accept any special characters or usernames/passwords that have special
characters in it.
*/
<form class="form-signin" action='#' method='POST'>
      <h1 class="h3 mb-3 font-weight-normal">Please sign in</h1>
      <label for="inputUsername" class="sr-only">Username</label>
      <input type="text" id="inputUsername" pattern="[a-zA-z0-9]+" class="form-control" placeholder="Username" required autofocus name='username'>
      <label for="inputPassword" class="sr-only">Password</label>
      <input type="password" id="inputPassword" pattern="[a-zA-z0-9]+" class="form-control" placeholder="Password" required name='password'>
  //    <input type="hidden" name="cookie" value="<?php echo $attempt_count; ?>" />	
  //<input type="hidden" name="csrf_token" value="<?php echo generate_csrf_token(); ?>" /> 
      <button class="btn btn-lg btn-primary btn-block" type="submit">Sign in</button>
</form>
<br>
	<?php include("templates/contentstop.php"); ?>
	<?php include("templates/footer.php"); ?>
//	<?php echo "login_attempt"; ?>
	<?php echo " $attempt_count"; ?>	
</body>
</html>

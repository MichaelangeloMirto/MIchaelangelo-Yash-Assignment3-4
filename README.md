# MIchaelangelo-Yash-Assignment3-4
Fixing PHP Blog
Below is the new admin.php file for role-based and XSS


<?php include("templates/page_header.php");?>
<?php include("lib/auth.php") ?>
<!doctype html>
<html lang="en">
<head>
	<title>Admin</title>
	<?php include("templates/header.php"); ?>
</head>
<body>
	<?php include("templates/nav.php"); ?>
	<?php include("templates/contentstart.php"); ?>

<h2>Article Management</h2>

<p><button type="button" class="btn btn-primary" aria-label="Left Align" onclick="window.location='/newarticle.php';">
New Post <span class="fa fa-plus" aria-hidden="true"></span>
</button></p>

<table class="table">
<tr><th>Post Title</th><th>Author</th><th>Date</th><th>Modify</th><th>Delete</th></tr>

<?php
# get articles by user or, if role is admin, all articles
		$result = get_article_list($dbconn);
		while ($row = pg_fetch_array($result)) {
	?>
<tr>
<?php # Adding in the htmlspecialchars line to stop the rendering of all html code being used on the application. HTML code gets rendered as normal text on screen
      /*In the code below we used an if statement to check if the username is admin,
and if the user is logged in as admin they are allowed to do whatever they want with
the posts that are being displayed. The else statement is for the username student,
where they are not allowed to delete posts that are made by admin. Student user
is only allowed to delete posts that are made by the user student.
*/
	?>
<?php if($_SESSION['username']=='admin'){ ?>
  <td><a href='article.php?aid=<?php echo $row['aid'] ?>'><?php echo htmlspecialchars($row['title'], ENT_QUOTES, 'UTF-8'); ?></a></td>
  <td><?php echo $row['author'] ?></td>
  <td><?php echo substr($row['date'],0,10) ?></td>
  <td><a href="/editarticle.php?aid=<?php echo $row['aid'] ?>"><i class="fa fa-pencil-square-o fa-2x" aria-hidden="true"></i></a></td>
  <td><a href="/deletearticle.php?aid=<?php echo $row['aid'] ?>"><i class="fa fa-times fa-2x" aria-hidden="true"></i></a></td>
</tr>
<?php }else{    ?>
<td><a href='article.php?aid=<?php echo $row['aid'] ?>'><?php echo htmlspecialchars($row['title'], ENT_QUOTES, 'UTF-8'); ?></a></td>
  <td><?php echo $row['author'] ?></td>
  <td><?php echo substr($row['date'],0,10) ?></td>
<?php if($_SESSION['username']=='student'){ ?>
<?php	if($row['author']=='student'){ ?>
  <td><a href="/editarticle.php?aid=<?php echo $row['aid'] ?>"><i class="fa fa-pencil-square-o fa-2x"> 
<?php }else{ ?>
 <td><a href="/admin.php"><i class="fa fa-pencil-square-o fa-2x" aria-hidden="true"></i></a></td>
<?php } ?>
<?php } ?>
<?php if($row['author']=='student'){    ?> 
	<td><a href="/deletearticle.php?aid=<?php echo $row['aid'] ?>"><i class="fa fa-times fa-2x" aria-hidden="true"></i></a></td> 
<?php } ?>
<?php } ?>

	<?php } //close while loop ?>
</table>
	<?php include("templates/contentstop.php"); ?>
	<?php include("templates/footer.php"); ?>

</body>
</html>





#Below is the new editarticle.php

<?php include("templates/page_header.php");?>
<?php include("lib/auth.php") ?>
<?php /*The below 6 lines are checking if the user logged in is a student and 
if the post they are trying to access is a 1. If the both conditions are met
they are not allowed to edit an admins post. The else condition checks for student
and author 2(student), or admin trying to edit any post
*/ ?>
<?php if(($_SESSION['username']=='student') && ($row['author']=1)){
 Header ("Location: /admin.php");
}else{
 Header ("Location: /");
}
?>

<?php
if($_SERVER['REQUEST_METHOD'] == 'GET') {
	$aid = $_GET['aid'];	
	$result=get_article($dbconn, $aid);
	$row = pg_fetch_array($result, 0);
} elseif ($_SERVER['REQUEST_METHOD'] == 'POST') {
	$title = $_POST['title'];
	$content = $_POST['content'];
	$aid = $_POST['aid'];
	$result=update_article($dbconn, $title, $content, $aid);
	Header ("Location: /");
}?>

<!doctype html>
<html lang="en">
<head>
	<title>New Post</title>
	<?php include("templates/header.php"); ?>
</head>
<body>
	<?php include("templates/nav.php"); ?>
	<?php include("templates/contentstart.php"); ?>

<h2>New Post</h2>

<form action='#' method='POST'>
	<input type="hidden" value="<?php echo $row['aid'] ?>" name="aid">
	<div class="form-group">
	<label for="inputTitle" class="sr-only">Post Title</label>
	<input type="text" id="inputTitle" required autofocus name='title' value="<?php echo $row['title'] ?>">
	</div>
	<div class="form-group">
	<label for="inputContent" class="sr-only">Post Content</label>
	<textarea name='content' id="inputContent"><?php echo $row['content'] ?></textarea>
	</div>
	<input type="submit" value="Update" name="submit" class="btn btn-primary">
</form>
<br>

	<?php include("templates/contentstop.php"); ?>
	<?php include("templates/footer.php"); ?>
</body>
</html>

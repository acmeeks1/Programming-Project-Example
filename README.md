# Programming-Project-Example
# Registration Forum for a Project
<?php

/*
*/
$showform = 1;
$errmsg = 0;
$errfname ="";
$errlname ="";
$erruname ="";
$erremail ="";
$errpwd="";
$errpwd2="";
$hashedpwd="";
$pagename= "Program 3";
require_once "includes/header.php";
?>
<?php
if ($_SERVER['REQUEST_METHOD']=="POST"){
    //var_dump($_POST);
   // echo "<hr>";
   // print_r($_POST);
   // echo "<hr>";
    //sanitize data
    $fname =trim($_POST['fname']);
    $lname =trim($_POST['lname']);
    $uname =trim($_POST['uname']);
    $email =trim(strtolower($_POST['email']));
    $pwd= $_POST['pwd'];
    $pwd2= $_POST['pwd2'];
    $submit= $_POST;

    //check empty fields
    if (empty($fname)){
        $errmsg =1;
        $errfname = "Missing first name.";
    }
    if (empty($lname)){
        $errmsg =1;
        $errlname ="Missing last name.";
    }
    if (empty($uname)){
        $errmsg =1;
        $erruname ="Missing username.";
    }

    if (empty($email)){
        $errmsg=1;
        $erremail="Missing email.";
    }
    else{
        if (!filter_var($email,FILTER_VALIDATE_EMAIL)){
            $errmsg =1;
            $erremail= "Email is not valid.";
        }
    }
    if (empty($pwd)){
        $errmsg=1;
        $errpwd="Missing password.";
    }
    if (empty($pwd2)){
        $errmsg=1;
        $errpwd2="Missing confirmation password.";
    }

    if($pwd!=$pwd2)
    {
        $errmsg =1;
        $errpwd2 = "<span class 'error'>The Passwords do not match.</span>";
    }
    //validate email and urls
//checkdup function
    $sql = "SELECT * FROM acmeeks1_members WHERE email = ?";
    $emailexists = checkdup($pdo, $sql, $email);
    if ($emailexists) {
        $errmsg = 1; //update the general error flag
        $erremail .= " The email is taken.";
    }
    $sql = "SELECT * FROM acmeeks1_members WHERE uname = ?";
    $unameexists = checkdup($pdo, $sql, $uname);
    if ($unameexists){
        $errmsg =1;
        $erruname .="The username is taken";
    }
    //control for form

    if ($errmsg == 1) {
        echo "<p class='error'>Oops!  There are errors.  Please make changes to your form and resubmit.</p>";
    }
    else {
        echo "<p class='success'>Thank you for entering your information.</p>";
        $hashedpwd = password_hash($pwd, PASSWORD_DEFAULT);


        //control for form

        $sql = "INSERT INTO acmeeks1_members (inputdate, email, uname, pwd, fname)
                VALUES (:inputdate, :email, :uname, :pwd, :fname)";
        $stmt = $pdo->prepare($sql);
        $stmt->bindValue(':inputdate', time());
        $stmt->bindValue(':email', $email);
        $stmt->bindValue(':uname', $uname);
        $stmt->bindValue(':pwd', $hashedpwd);
        $stmt->bindValue(':fname', $fname);
        $stmt->execute();
        $showform = 0;
    }

}
if($showform==1){
?>
    <form name="usernew" id="usernew" method="post" action="<?php echo $currentfile;?>">
        <br/>
        <label for="fname">First Name:</label>
        <input type="text" name="fname" id="fname" size="40" maxlength="40" placeholder="Required First Name"
               value="<?php if(isset($fname)){echo $fname;}?>">
        <?php if (!empty($errfname)){ echo "<span class='error'>$errfname</span>";}?>
        <br/>
        <label for="lname">Last Name:</label>
        <input type="text" name="lname" id="lname" size="40" maxlength="40" placeholder="Required Last Name"
               value="<?php if(isset($lname)){echo $lname;}?>">
        <?php if (!empty($errlname)){ echo "<span class='error'>$errlname</span>";}?>
        <br/>
        <label for="email">Email:</label>
        <input type="email" name="email" id="email" size="40" maxlength="40" placeholder="Required Email"
            value="<?php if(isset($email)){echo $email;}?>">
        <?php if (!empty($erremail)){ echo "<span class='error'>$erremail</span>";}?>
        <br/>
        <label for="uname">User Name:</label>
        <input type="text" name="uname" id="uname" size="40" maxlength="40" placeholder="Required Username"
               value="<?php if(isset($uname)){echo $uname;}?>">
        <?php if (!empty($erruname)){ echo "<span class='error'>$erruname</span>";}?>
        <br/>
        <label for="pwd">Password:</label>
        <input type="password" name="pwd" id="pwd" size="40"  minlength="8" maxlength="40" placeholder="Required Password">
        <?php if (!empty($errpwd)){ echo "<span class='error'>$errpwd</span>";}?>
        <br/>
        <label for="pwd2">Confirm Password:</label>
        <input type="password" name="pwd2" id="pwd2" size="40" minlength="8" maxlength="40" placeholder="Confirm Password">
        <?php if (!empty($errpwd2)){ echo "<span class='error'>$errpwd2</span>";}?>
        <br/>
        <label for="submit">Submit:</label>
        <input type="submit" name="submit" id="submit" value="submit">

    </form>


<?php
}//end of showform
require_once "includes/footer.php";
?>

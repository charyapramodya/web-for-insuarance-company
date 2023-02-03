# web-for-insuarance-company
this is a group project to implement a web site for an insurance company 

The full project is in https://github.com/lahiruT97/GroupProject 

My contribution is in following pages

<!Backend>

********************* database ********************
<?php
    require_once 'dbConnection039.php';
    session_start();

    $conn = Database::getConnection();

    
    $reqnic = isset($_POST["NICNum"]) ? $_POST["NICNum"] : null;
    $reqemail = isset($_POST["email"]) ? $_POST["email"] : null;
    $reqnumber = isset($_POST["number"]) ? $_POST["number"] : null;
  
  if($reqnic){
    $accountFound="SELECT *
    FROM customer_details
    WHERE NIC='$reqnic'";

$result = $conn->query($accountFound);

if ($result->num_rows == 1) {

    if( $reqemail ){
      $updateemail = "UPDATE customer_details SET email='$reqemail' WHERE NIC=$reqnic";

      if ($conn->query($updateemail) === TRUE) {
        echo "<div style='margin:20% 30%;'><h4>Record updated successfully</h4></div>";
      } else {
      echo "Error updating record: " . $conn->error;
      }
  }

 if( $reqnumber){

    $updatenumber = "UPDATE customer_details SET ContactNumber='$reqnumber' WHERE NIC=$reqnic";

      if ($conn->query($updatenumber) === TRUE) {
        echo "<div style='margin:20% 30%;'><h4>Record updated successfully</h4></div>";
      } else {
        echo "Error updating record: " . $conn->error;
      }
    }
  }
    else{
      echo "<div style='margin:20% 30%;'><h4>Invalid NIC</h4></div>";
    }
  
}
  else{
     echo "<div style='margin:20% 30%;'><h4>Values are not set</h4></div>";
}


header('Refresh:2 ; URL= account_profile039.php');

?>



************************database Connection*******************
<?php

class Database {
    private static $connection = null;

    public static function createConnection() {
        $servername = "localhost";
        $username = "root";
        $password = "";
        $dbname = "group12";
        self::$connection = new mysqli($servername, $username, $password, $dbname);

        if (self::$connection->connect_error) {
            die("connection failed: " . self::$connection->connect_error);
        }
    }

    public static function getConnection() {
        if (self::$connection == null) {
            self::createConnection();
        }

        return self::$connection;
    }

}
?>

*******************register****************
<!DOCTYPE html>
<html>
    <head></head>
    <body>
    <?php
    include('./dbConnection039.php');
    session_start();

    $conn = Database::getConnection();


//check whether any username is there
mysqli_select_db($conn,"group12");
$sql = "SELECT userName,NIC
        FROM customer_details
        WHERE userName='$_POST[username]' OR NIC='$_POST[NICNum]'";

$result = $conn->query($sql);

if ($result->num_rows == 0) {

    //check if the policy number and given NIC is matched
    $sqlpn="SELECT NIC
            FROM policyncustomer
            WHERE policy_Number = '$_POST[policyNum]";

    $result = $conn->query($sqlpn);

    if($result->num_rows == 0){
        echo "<div style='margin:20% 30%;'><h4>Please inform your agent!<br> There is no record with entered policy number </h4></div>";
        header('Refresh:2; URL=register039.php');
    }
    else{
    //Insert values into the database
    mysqli_select_db($conn,"group12");
    $sql = "INSERT INTO customer_details
    VALUES('$_POST[NICNum]','$_POST[title]','$_POST[fullName]','$_POST[number]','$_POST[email]',$_POST[age],'$_POST[username]','$_POST[passwrd]')";

        
            if(!mysqli_query($conn,$sql))
            {
                die("error".mysqli_error($conn));
            }
            else
            {
                echo "<div style='margin:20% 30%;'><h4>New record Created successfully</h4></div>";
                 header('Refresh:2; URL=register039.php');
            }

    }

}
else{
    echo "<div style='margin:20% 30%;'><h4>This NIC number or username is used!</h4></div>";
    header('Refresh:2; URL=register039.php');
}


mysqli_close($conn);
?>
    </body>
</html>


*********************login**************
<?php
    include('./dbConnection039.php');
    session_start();

    $conn = Database::getConnection();
?>
<html>
    <body>
<?php
$reqUsername = isset($_POST["username"]) ? $_POST["username"] : null;
$reqPassword = isset($_POST["password"]) ? $_POST["password"] : null;

if ($reqUsername and $reqPassword) {
    $accountFound="SELECT *
                FROM customer_details
                WHERE userName='$reqUsername' AND password='$reqPassword'";
    
    $result = $conn->query($accountFound);

    if ($result->num_rows == 1) {
        $user = $result->fetch_assoc();
        $_SESSION["user"] = $user;
        
        header('location: account_profile039.php');
    }
    else {
        echo "<div style='margin:20% 30%;'><h4>Please inform your agent!<br> There is no record with entered policy number </h4></div>";
         header('Refresh:2; URL=login039.php');
     }
}

else {
    echo "<div style='margin:20% 30%;'><h4>Values are not assigned!</h4></div>";
    header('Refresh:2; URL=login039.php');
}

?>
    </body>
</html>

*****************************forget password**********************
<?php
    include('./dbConnection039.php');
    session_start();

    $conn = Database::getConnection();
?>
<html>
<body>

    <?php
    $reqUsername = isset($_POST["username"]) ? $_POST["username"] : null;
    $reqemail = isset($_POST["email"]) ? $_POST["email"] : null;

    if ($reqUsername and $reqemail) {
        $accountFound = "SELECT *
            FROM customer_details
            WHERE userName='$reqUsername' AND email='$reqemail'";

        $result = $conn->query($accountFound);

        if ($result->num_rows == 1) {
            $updater = $result->fetch_assoc();
            $_SESSION["updater"]= $updater ;

                header('location: reset039.php');
                
        } else {
            echo "<div style='margin:20% 30%;'><h4>Invalid username or email! </h4></div>";
            header('Refresh:2; URL=forget039.php');
        }
    } else {
        echo "<div style='margin:20% 30%;'><h4>Values are not assigned ! </h4></div>";
        header('Refresh:2; URL=forget039.php');
    }


    mysqli_close($conn);
    ?>
</body>

</html>

*********************resetpassword************8
<?php
    require_once 'dbConnection039.php';
    session_start();

    $conn = Database::getConnection();

    $updater = $_SESSION["updater"];
    
    $reqnic = $updater["NIC"];
    $reqpassword = isset($_POST["passwrd"]) ? $_POST["passwrd"] : null;
  
  if($reqnic and $reqpassword){
    $accountFound="SELECT *
    FROM customer_details
    WHERE NIC='$reqnic'";

$result = $conn->query($accountFound);

if ($result->num_rows == 1) {

      $resetpasswrd = "UPDATE customer_details SET password='$reqpassword' WHERE NIC=$reqnic";

      if ($conn->query($resetpasswrd) === TRUE) {
        echo "<div style='margin:20% 30%;'><h4>password is reset successfully</h4></div>";
        header('Refresh:1 ; URL= login039.php');
    } else {
      echo "Error updating record: " . $conn->error;
      header('Refresh:1 ; URL= reset039.php');

      }
  }
    else{
      echo "<div style='margin:20% 30%;'><h4>Invalid NIC</h4></div>";
      header('Refresh:1 ; URL= reset039.php');

    }
  
}
  else{
     echo "<div style='margin:20% 30%;'><h4>Values are not set</h4></div>";
     header('Refresh:1 ; URL= reset039.php');

}

?>

************************new applications************
<!DOCTYPE html>
<body>
<?php

include('./dbConnection039.php');
$conn = Database::getConnection();

//Insert values into the database

mysqli_select_db($conn,"group12");
$sql = "INSERT INTO applynew(title,fullName,ContactNumber,email,age,policy_name)
VALUES('$_POST[title]','$_POST[fullName]','$_POST[number]','$_POST[email]','$_POST[age]','$_POST[policy]')";

if(!mysqli_query($conn,$sql))
{
    die("error".mysqli_error($conn));
  
}
else
{
	 echo "<div style='margin:20% 30%;'><h4>New record Created successfully</h4></div>";
         header('Refresh:2; URL=ApplyNewPolicy039.php');
}

mysqli_close($conn);
?>
</body>
</html>



<!front end>
styles
@import url("https://fonts.googleapis.com/css?family=Lato:100,300,400,700,900");

/* -----------------------------------
    1 - General Styles
------------------------------------*/
*,
body {
    font-family: "Lato", sans-serif;
    font-weight: 400;
    -webkit-font-smoothing: antialiased;
    text-rendering: optimizeLegibility;
    -moz-osx-font-smoothing: grayscale;
}

.update-row-btn {
    width: 50%;
}

a {
    color: #f6ff00;
    text-decoration: none;
    background-color: transparent;
}

.form-items .small-forms {
    max-width: 340px;
    position: relative;
    margin-left: 10%;
}

a::hover::after {
    color: #ef940b;
}

.warning-div {
    background-color: rgb(255, 1, 1);
    text-align: center;
    color: rgb(251, 251, 251);
    font-weight: bolder;
    font-style: oblique;
    margin-block: 2%;

}

.btn-update {
    border-radius: 6px;
    border: 0;
    padding: 6px 28px;
    background-color: rgb(240, 220, 3);
    color: #0408fd;
    font-size: 14px;
    font-weight: 700;
    text-decoration: none;
}

.apply {
    text-align: center;
    margin-top: 5%;
    margin-left: 40%;
    margin-bottom: -60px;
}

.apply>h1 {
    margin-bottom: 1%;
}

.apply>h5 {
    color: #0408fd;
}


p {
    color: #e2f0ff;
}

h1 {
    font-weight: bolder;
    color: #ffc400;
    margin-block: 5%;
    text-align: center
}

h3 {
    color: #fff;
    font-style: italic;
    font-weight: bolder;
    margin-block: 5%;
}

.modal-header39,
.modal-footer39,
h4,
.close39 {
    background-color: #ffc400;
    color: #0a0054 !important;
    text-align: center;
    font-size: 30px;
    font-weight: bolder;

}

.modal-body39 {
    background-color: aliceblue;
    color: #0a0054;
}

.btn-modal-new {
    border: 2px;
    background-color: #0a0054;
    color: aliceblue;
}

table {
    width: 100%;
    color: #B0C2D0;
    align-items: center;
}

thead {
    color: #f1e5be;
    font-weight: bold;
    font-size: large;
    text-align: center;
}

.form-body {
    height: 100%;
    background-image: url("../images/background/forms.PNG");

}

.form-body-hr {
    height: 100%;

}

.form-body>.row,
.form-body-hr>.row {
    margin-left: 0;
    margin-right: 0;
    height: 100%;
    /* background-image: url("../images/background/forms.PNG"); */

}

.form-body>.row-small {
    height: 100%;
    width: 30%;
}

.website-logo {
    display: inline-block;
    position: absolute;
    z-index: 1000;
    top: 50px;
    left: 50px;
    right: initial;
    bottom: initial;
}

.website-logo img {
    width: 100px;
}

.website-logo a {
    display: inline-block;
}


.website-logo .logo img {
    width: 100px;
}

.website-logo .logo img.logo-size {
    opacity: 0 !important;
}

.website-logo-inside {
    margin-bottom: 70px;
}

.website-logo-inside img {
    width: 100px;
}

.website-logo-inside a {
    display: inline-block;
}



.preview-body {
    padding-top: 70px;
    padding-bottom: 70px;
    text-align: center;
}



.preview-body .web-title {
    font-size: 30px;
    font-weight: 300;
    color: #000;
    line-height: 35px;
    margin-bottom: 50px;
}

.preview-body .img-link {
    display: inline-block;
    width: 100%;
    margin: 20px 0;
    padding: 0 5px;
}

.preview-body .img-link img {
    width: 100%;
    border-radius: 15px;
    -webkit-box-shadow: 0 0 5px rgba(160, 163, 165, 0.38);
    box-shadow: 0 0 5px rgba(160, 163, 165, 0.38);
    -webkit-transition: all 0.5s cubic-bezier(0.34, 1.61, 0.7, 1);
    transition: all 0.5s cubic-bezier(0.34, 1.61, 0.7, 1);
}

.preview-body .img-link:hover img,
.preview-body .img-link:focus img {
    -webkit-transform: scale(1.05);
    -moz-transform: scale(1.05);
    -ms-transform: scale(1.05);
    transform: scale(1.05);
    -webkit-box-shadow: 0 11px 19px rgba(160, 163, 165, 0.3);
    box-shadow: 0 11px 19px rgba(160, 163, 165, 0.3);
}






.form-holder {
    margin-left: 550px;
    width: 80%;
    height: fit-content;
    top: 100px;
    align-items: center;

}

.form-holder .form-content {
    position: relative;
    text-align: center;
    display: -webkit-box;
    display: -moz-box;
    display: -ms-flexbox;
    display: -webkit-flex;
    display: flex;
    -webkit-justify-content: center;
    justify-content: center;
    -webkit-align-items: center;
    align-items: center;
    padding: 60px;
    min-height: 100%;
}

.form-holder .form-content ::-webkit-input-placeholder {
    color: #526489;
}

.form-holder .form-content :-moz-placeholder {
    color: #526489;
}

.form-holder .form-content ::-moz-placeholder {
    color: #526489;
}

.form-holder .form-content :-ms-input-placeholder {
    color: #526489;
}

.form-control:focus {
    -webkit-box-shadow: none;
    box-shadow: none;
}

.form-control ::-webkit-input-placeholder {
    color: #526489;
}

.form-control :-moz-placeholder {
    color: #526489;
}

.form-control ::-moz-placeholder {
    color: #526489;
}

.form-control :-ms-input-placeholder {
    color: #526489;
}

.form-content {
    position: relative;
    background-color: #0c057b;
    margin-left: 60%;
    width: 550px;
}

.form-content .form-group {
    color: #fff;
    font-size: 15px;
    font-weight: 300;
}

.form-content .form-items {
    display: inline-block;
    width: 100%;
    max-width: 500px;
    text-align: left;
    -webkit-transition: all 0.4s ease;
    transition: all 0.4s ease;
}

.form-content .form-icon {
    text-align: center;
    width: 100%;
    line-height: 0;
    margin-top: calc(-42px - 35px);
    margin-bottom: 28px;
}

.form-content .form-icon .icon-holder {
    position: relative;
    display: inline-block;
    width: 85px;
    height: 85px;
    border-radius: 85px;
    background-color: #4A77F7;
    padding: 20px;
}

.form-content .form-icon .icon-holder img {
    position: absolute;
    width: 50%;
    top: 50%;
    left: 50%;
    margin-top: -23%;
    margin-left: -25%;
}

.form-content h3 {
    color: #fff;
    text-align: left;
    font-size: 24px;
    font-weight: 900;
    margin-bottom: 10px;
}

.form-content h3.form-title {
    margin-bottom: 30px;
}

.form-content h3.form-title-center {
    margin-bottom: 30px;
    text-align: center;
    font-size: 22px;
}

.form-content p {
    color: #fff;
    text-align: left;
    font-size: 18px;
    font-weight: 300;
    line-height: 20px;
    margin-bottom: 30px;
}

.form-content p.form-subtitle {
    font-size: 16px;
    margin-bottom: 15px;
}

.form-content small.error-message {
    color: lightcoral;
}

.form-content label {
    color: #fff;
    text-align: left;
    font-size: 15px;
    font-weight: 300;
    line-height: 20px;
    margin-bottom: 10px;
}

.form-content .page-links {
    margin-bottom: 34px;
}

.form-content .page-links a {
    position: relative;
    display: inline-block;
    text-decoration: none;
    color: #fff;
    font-weight: 300;
    font-size: 15px;
    margin-right: 20px;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
}

.form-content .page-links a:last-child {
    margin-right: 0;
}

.form-content .page-links a:after {
    position: absolute;
    content: "";
    width: 100%;
    height: 2px;
    left: 0;
    bottom: -10px;
    background-color: rgba(255, 255, 255, 0.5);
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
}

.form-content .page-links a.active {
    font-weight: 700;
}

.form-content .page-links a.active:after {
    background-color: #fff;
}

.form-content .page-links a:hover:after,
.form-content .page-links a:focus:after {
    background-color: #fff;
}

.form-content input,
.form-content .dropdown-toggle.btn-default {
    width: 100%;
    padding: 9px 20px;
    text-align: left;
    border: 0;
    outline: 0;
    border-radius: 6px;
    background-color: #fff;
    font-size: 15px;
    font-weight: initial;
    color: #1807ca;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
    margin-bottom: 14px;
}

.form-content input:hover,
.form-content input:focus,
.form-content .dropdown-toggle.btn-default:hover,
.form-content .dropdown-toggle.btn-default:focus {
    border: 0;
    background-color: #ebeff8;
    color: #8D8D8D;
}

.form-content textarea {
    position: static !important;
    width: 100%;
    padding: 8px 20px;
    border-radius: 6px;
    text-align: left;
    background-color: #fff;
    border: 0;
    font-size: 15px;
    font-weight: 300;
    color: #8D8D8D;
    outline: none;
    resize: none;
    height: 120px;
    -webkit-transition: none;
    transition: none;
    margin-bottom: 14px;
}

.form-content textarea:hover,
.form-content textarea:focus {
    border: 0;
    background-color: #ebeff8;
    color: #8D8D8D;
}

.form-content .custom-file {
    margin-bottom: 14px;
}

.form-content .custom-file-label {
    position: absolute;
    padding: 9px 44px 9px 20px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    text-align: left;
    border: 0;
    outline: 0;
    border-radius: 6px;
    background-color: #fff;
    font-size: 15px;
    font-weight: 300;
    color: #8D8D8D;
    outline: none !important;
    -webkit-box-shadow: none !important;
    box-shadow: none !important;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
}

.form-content .custom-file-label:after {
    content: "\f382" !important;
    font-family: Font Awesome\ 5 Free;
    font-style: normal;
    font-weight: 600;
    padding: 0.475rem 0.75rem 0.375rem;
    color: #495057;
    background-color: transparent;
    border-left: 0;
    border-radius: 0;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
}

.form-content input[type=checkbox],
.form-content input[type=radio] {
    width: auto;
}

.form-content input[type=checkbox]:not(:checked),
.form-content input[type=checkbox]:checked,
.form-content input[type=radio]:not(:checked),
.form-content input[type=radio]:checked {
    position: absolute;
    left: -9999px;
}

.form-content input[type=checkbox]:not(:checked)+label,
.form-content input[type=checkbox]:checked+label,
.form-content input[type=radio]:not(:checked)+label,
.form-content input[type=radio]:checked+label {
    position: relative;
    padding-left: 23px;
    cursor: pointer;
    display: inline;
    color: #fff;
    font-size: 15px;
    font-weight: 700;
    margin-left: 0;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}

.form-content input[type=checkbox]:checked+label,
.form-content input[type=radio]:checked+label {
    color: #fff;
}

.form-content input[type=checkbox]:checked+label:before,
.form-content input[type=radio]:checked+label:before {
    content: "";
    position: absolute;
    left: 0;
    top: 2px;
    width: 15px;
    height: 15px;
    background: #fff;
    border-radius: 50px;
    border: 0px solid #fff;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
}

.form-content input[type=checkbox]:not(:checked)+label:before,
.form-content input[type=radio]:not(:checked)+label:before {
    content: "";
    position: absolute;
    left: 0;
    top: 2px;
    width: 15px;
    height: 15px;
    background: transparent;
    border-radius: 50px;
    border: 2px solid #fff;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
}

.form-content input[type=checkbox]:not(:checked)+label:after,
.form-content input[type=radio]:not(:checked)+label:after {
    opacity: 0;
    -webkit-transform: scale(0);
    -moz-transform: scale(0);
    -ms-transform: scale(0);
    transform: scale(0);
}

.form-content input[type=checkbox]:disabled+label,
.form-content input[type=radio]:disabled+label {
    opacity: 0.6;
}

.form-content input[type=checkbox]:checked+label:after,
.form-content input[type=checkbox]:not(:checked)+label:after {
    content: "\f00c";
    font-family: "Font Awesome 5 Free";
    font-style: normal;
    font-weight: 600;
    position: absolute;
    top: 3px;
    left: 3px;
    font-size: 9px;
    color: #0093FF;
    line-height: 14px;
    -webkit-transition: all 0.2s ease;
    transition: all 0.2s ease;
}

.form-content input[type=checkbox]:checked+label:before {
    border-radius: 4px;
}

.form-content input[type=checkbox]:not(:checked)+label:before {
    border-radius: 4px;
}

.form-content input[type=radio]:checked+label:after,
.form-content input[type=radio]:not(:checked)+label:after {
    content: "";
    position: absolute;
    top: 7px;
    left: 5px;
    width: 5px;
    height: 5px;
    border-radius: 20px;
    background-color: #0093FF;
    -webkit-transition: all 0.2s ease;
    transition: all 0.2s ease;
}

.form-content .custom-options {
    display: -webkit-box;
    display: -moz-box;
    display: -ms-flexbox;
    display: -webkit-flex;
    display: flex;
}

.form-content .custom-options input[type=checkbox],
.form-content .custom-options input[type=radio] {
    width: auto;
}

.form-content .custom-options input[type=checkbox]:not(:checked),
.form-content .custom-options input[type=checkbox]:checked,
.form-content .custom-options input[type=radio]:not(:checked),
.form-content .custom-options input[type=radio]:checked {
    position: absolute;
    left: -9999px;
}

.form-content .custom-options input[type=checkbox]:not(:checked)+label,
.form-content .custom-options input[type=checkbox]:checked+label,
.form-content .custom-options input[type=radio]:not(:checked)+label,
.form-content .custom-options input[type=radio]:checked+label {
    position: relative;
    padding-left: 0;
    cursor: pointer;
    display: inline;
    color: #606060;
    background-color: #F7F7F7;
    font-size: 13px;
    font-weight: 400;
    margin-left: 0;
    border-radius: 5px;
    padding: 4px 10px;
    margin-right: 10px;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    flex-grow: 1;
    text-align: center;
}

.form-content .custom-options input[type=checkbox]:not(:checked)+label:last-child,
.form-content .custom-options input[type=checkbox]:checked+label:last-child,
.form-content .custom-options input[type=radio]:not(:checked)+label:last-child,
.form-content .custom-options input[type=radio]:checked+label:last-child {
    margin-right: 0;
}

.form-content .custom-options input[type=checkbox]:not(:checked)+label:after,
.form-content .custom-options input[type=checkbox]:checked+label:after,
.form-content .custom-options input[type=radio]:not(:checked)+label:after,
.form-content .custom-options input[type=radio]:checked+label:after {
    display: none;
}

.form-content .custom-options input[type=checkbox]:checked+label,
.form-content .custom-options input[type=radio]:checked+label {
    color: #fff;
    background-color: #57D38C;
    font-weight: 400;
    -webkit-box-shadow: 0 3px 8px rgba(74, 230, 142, 0.35);
    box-shadow: 0 3px 8px rgba(74, 230, 142, 0.35);
}

.form-content .custom-options input[type=checkbox]:checked+label:before,
.form-content .custom-options input[type=radio]:checked+label:before {
    display: none;
}

.form-content .custom-options input[type=checkbox]:not(:checked)+label:before,
.form-content .custom-options input[type=radio]:not(:checked)+label:before {
    display: none;
}

.form-content .custom-options input[type=checkbox]:not(:checked)+label:after,
.form-content .custom-options input[type=radio]:not(:checked)+label:after {
    display: none;
}

.form-content .form-button {
    margin-top: 30px;
    margin-bottom: 25px;
    float: right;
}

.form-content .form-button .ibtn {
    border-radius: 6px;
    border: 0;
    padding: 6px 28px;
    background-color: rgb(240, 220, 3);
    color: #0408fd;
    font-size: 14px;
    font-weight: 700;
    text-decoration: none;
    cursor: pointer;
    margin-right: 10px;
    outline: none;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
    -webkit-box-shadow: 0 0 0 rgba(0, 0, 0, 0.16);
    box-shadow: 0 0 0 rgba(0, 0, 0, 0.16);
}

.form-content .form-button .ibtn.ibtn-full {
    width: 50px;
}

.form-content .form-button .ibtn:last-child {
    margin-right: 0;
}

.form-content .form-button .ibtn:hover,
.form-content .form-button .ibtn:focus {
    -webkit-box-shadow: 0 5px 6px rgba(0, 0, 0, 0.16);
    box-shadow: 0 5px 6px rgba(0, 0, 0, 0.16);
}

.form-content .form-button .ibtn.less-padding {
    padding: 6px 15px !important;
}

.form-content .form-button .ibtn.extra-padding {
    font-size: 16px;
    padding: 10px 32px;
}

.form-content .form-button a {
    font-size: 13px;
    font-weight: 700;
    color: #fff;
}

.form-content .form-button.full-width {
    margin-top: 15px;
}

.form-content .form-button.full-width .ibtn {
    width: 100%;
}

.form-content .btn {
    border-radius: 6px;
    padding: 6px 28px;
    font-size: 14px;
    font-weight: 700;
    margin-right: 10px;
    border: 0;
}

.form-content .btn.btn-light {
    color: #B0C2D0;
}

.form-content .btn.btn-light:hover,
.form-content .btn.btn-light:focus {
    color: #a0b6c6;
}

.form-content .btn :last-child {
    margin-right: 0;
}

.form-content form {
    margin-bottom: 30px;
}

.form-content .other-links span {
    font-size: 12px;
    font-weight: 300;
    color: #fff;
    margin-right: 20px;
}

.form-content .other-links a {
    font-size: 12px;
    font-weight: 700;
    color: #fff;
    margin-right: 10px;
}

.form-content .other-links a:last-child {
    margin-right: 0;
}

.form-content .other-links a i {
    display: inline-block;
    width: 25px;
    height: 25px;
    background-color: #000;
    color: #fff;
    border-radius: 25px;
    text-align: center;
    padding-top: 5px;
    font-size: 15px;
    margin: 0 5px;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
}

.form-content .other-links a i[class*=fa-twitter] {
    background-color: #00aced;
}

.form-content .other-links a i[class*=fa-facebook] {
    background-color: #3b5998;
}

.form-content .other-links a i[class*=fa-youtube] {
    background-color: #bb0000;
}

.form-content .other-links a i[class*=fa-google] {
    background-color: #dd4b39;
}

.form-content .other-links a i[class*=fa-linkedin] {
    background-color: #007bb6;
}

.form-content .other-links a i[class*=fa-instagram] {
    background-color: #517fa4;
}

.form-content .other-links a i:hover,
.form-content .other-links a i:focus {
    opacity: 0.8;
}

.form-content.form-sm input,
.form-content.form-sm .dropdown-toggle.btn-default {
    padding: 6px 16px;
    margin-bottom: 10px;
    font-size: 14px;
}

.form-content.form-sm textarea {
    padding: 6px 16px;
    margin-bottom: 10px;
    font-size: 14px;
}

.form-content.form-sm .form-button .ibtn {
    padding: 4px 28px;
}

.form-content.form-sm .btn {
    padding: 4px 28px;
}

.form-content .form-sent {
    position: absolute;
    text-align: center;
    opacity: 0;
    pointer-events: none;
    z-index: 1;
    -webkit-transform-origin: center center;
    -moz-transform-origin: center center;
    -ms-transform-origin: center center;
    transform-origin: center center;
    -webkit-transform: scale(0.7) translateX(200px);
    -moz-transform: scale(0.7) translateX(200px);
    -ms-transform: scale(0.7) translateX(200px);
    transform: scale(0.7) translateX(200px);
    -webkit-transition: all 0.4s ease;
    transition: all 0.4s ease;
}

.form-content .form-sent.show-it {
    opacity: 1;
    pointer-events: all;
    z-index: 2;
    -webkit-transform: scale(1) translateX(0);
    -moz-transform: scale(1) translateX(0);
    -ms-transform: scale(1) translateX(0);
    transform: scale(1) translateX(0);
}

.form-content .form-sent.show-it .tick-holder .tick-icon {
    -webkit-animation: tick-anime3 0.7s cubic-bezier(0.34, 1.61, 0.7, 1) 0s forwards;
    -moz-animation: tick-anime3 0.7s cubic-bezier(0.34, 1.61, 0.7, 1) 0s forwards;
    -ms-animation: tick-anime3 0.7s cubic-bezier(0.34, 1.61, 0.7, 1) 0s forwards;
    animation: tick-anime3 0.7s cubic-bezier(0.34, 1.61, 0.7, 1) 0s forwards;
}

.form-content .form-sent.show-it .tick-holder .tick-icon:before {
    -webkit-animation: tick-anime1 0.2s linear 0.2s forwards;
    -moz-animation: tick-anime1 0.2s linear 0.2s forwards;
    -ms-animation: tick-anime1 0.2s linear 0.2s forwards;
    animation: tick-anime1 0.2s linear 0.2s forwards;
}

.form-content .form-sent.show-it .tick-holder .tick-icon:after {
    -webkit-animation: tick-anime2 0.4s ease 0.4s forwards;
    -moz-animation: tick-anime2 0.4s ease 0.4s forwards;
    -ms-animation: tick-anime2 0.4s ease 0.4s forwards;
    animation: tick-anime2 0.4s ease 0.4s forwards;
}

.form-content .form-sent .tick-holder {
    text-align: center;
    margin-bottom: 12px;
}

.form-content .form-sent .tick-holder .tick-icon {
    position: relative;
    display: inline-block;
    width: 40px;
    height: 40px;
    border-radius: 40px;
    background-color: rgba(255, 255, 255, 0);
    -webkit-transform: rotate(35deg) scale(2);
    -moz-transform: rotate(35deg) scale(2);
    -ms-transform: rotate(35deg) scale(2);
    transform: rotate(35deg) scale(2);
    -webkit-transform-origin: center center;
    -moz-transform-origin: center center;
    -ms-transform-origin: center center;
    transform-origin: center center;
}

.form-content .form-sent .tick-holder .tick-icon:before {
    content: "";
    position: absolute;
    background-color: #fff;
    width: 10px;
    height: 2px;
    top: 28px;
    left: 14px;
    border-radius: 2px;
    -webkit-transform-origin: left center;
    -moz-transform-origin: left center;
    -ms-transform-origin: left center;
    transform-origin: left center;
    -webkit-transform: scaleX(0);
    -moz-transform: scaleX(0);
    -ms-transform: scaleX(0);
    transform: scaleX(0);
}

.form-content .form-sent .tick-holder .tick-icon:after {
    content: "";
    position: absolute;
    background-color: #fff;
    width: 2px;
    height: 20px;
    top: 9px;
    left: 22px;
    border-radius: 2px;
    -webkit-transform-origin: center bottom;
    -moz-transform-origin: center bottom;
    -ms-transform-origin: center bottom;
    transform-origin: center bottom;
    -webkit-transform: scaleY(0);
    -moz-transform: scaleY(0);
    -ms-transform: scaleY(0);
    transform: scaleY(0);
}

.form-content .form-sent h3 {
    text-align: center;
    color: #fff;
}

.form-content .form-sent p {
    text-align: center;
    color: #fff;
    font-size: 15px;
    opacity: 0.8;
    margin-bottom: 20px;
}

.form-content .form-sent .info-holder {
    font-size: 12px;
    font-weight: 700;
    color: #fff;
    border-top: 1px solid rgba(255, 255, 255, 0.5);
    padding: 10px;
    margin-top: 60px;
}

.form-content .form-sent .info-holder span {
    font-size: 12px;
    font-weight: 700;
    color: #fff;
    opacity: 0.6;
}

.form-content .form-sent .info-holder a {
    font-size: 12px;
    font-weight: 700;
    color: #fff;
    opacity: 0.9;
}

.form-content .hide-it {
    opacity: 0;
    z-index: 1;
    pointer-events: none;
    -webkit-transform-origin: center center;
    -moz-transform-origin: center center;
    -ms-transform-origin: center center;
    transform-origin: center center;
    -webkit-transform: scale(0.7) translateX(-200px);
    -moz-transform: scale(0.7) translateX(-200px);
    -ms-transform: scale(0.7) translateX(-200px);
    transform: scale(0.7) translateX(-200px);
}

.form-content .row {
    margin-right: -6px;
    margin-left: -6px;
}

.form-content .row.top-padding {
    padding-top: 30px;
}

.form-content .row.top-padding .form-button {
    margin-top: 0;
}

.form-content .row .col {
    padding-right: 6px;
    padding-left: 6px;
}

.input-with-ccicon {
    position: relative;
    display: inline-block;
    width: 100%;
}

.input-with-ccicon #ccicon {
    position: absolute;
    right: 0.6rem;
    top: 0.55rem;
    font-size: 1.6rem;
}

.input-with-ccicon #ccicon[class*=visa] {
    color: #3744a2;
}

.input-with-ccicon #ccicon[class*=amex] {
    color: #1d8bd4;
}

.input-with-ccicon #ccicon[class*=diners-club] {
    color: #1d72d4;
}

.input-with-ccicon #ccicon[class*=mastercard] {
    color: #e42613;
}

.input-with-ccicon #ccicon[class*=discover] {
    color: #ef940b;
}

.input-with-ccicon input {
    padding-right: 45px;
}

.nav-tabs {
    border-bottom: 0;
    margin-bottom: 2.2rem;
}

.nav-tabs .nav-item .nav-link {
    position: relative;
    border: 0;
    font-weight: 300;
    padding: 0.5rem 0;
    margin-right: 1.2rem;
    text-align: center;
    color: #000;
    background-color: transparent;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
}

.nav-tabs .nav-item .nav-link:before {
    content: "";
    position: absolute;
    left: 0;
    bottom: -3px;
    width: 100%;
    height: 2px;
    background-color: #DEDEDE;
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
}

.nav-tabs .nav-item .nav-link.active {
    font-weight: 700;
}

.nav-tabs .nav-item .nav-link.active:before {
    height: 3px;
    background-color: #57D38C;
}

.form-subtitle {
    font-size: 19px;
    font-weight: 300;
    color: #fff;
    margin-bottom: 1rem;
}

.inline-el-holder .inline-el {
    display: inline-block;
    margin-right: 1.3rem;
}

.rad-with-details {
    margin-bottom: 1rem;
}

.rad-with-details .more-info {
    color: #fff;
    font-size: 14px;
    font-weight: 300;
    margin-top: 0.3rem;
}

.separator {
    border-top: 1px solid #C7C7C7;
    margin-top: 1rem;
    margin-bottom: 2rem;
}

input.sm-content {
    max-width: 110px;
}

.form-body.on-top .website-logo {
    position: absolute;
}

.form-body.on-top .img-holder {
    display: block;
    position: relative;
    width: 100%;
    min-height: initial;
    height: 40%;
    overflow: initial;
    padding: 40px;
}

.form-body.on-top .img-holder .info-holder.simple-info h3 {
    margin-bottom: 16px;
}

.form-body.on-top .img-holder .info-holder.simple-info p {
    margin-bottom: 10px;
}

.form-body.on-top .img-holder .info-holder.simple-info img {
    margin-bottom: 20px;
}

.form-body.on-top .form-holder {
    margin-left: 0;
}

@keyframes tick-anime1 {
    0% {
        -webkit-transform: scaleX(0);
        -moz-transform: scaleX(0);
        -ms-transform: scaleX(0);
        transform: scaleX(0);
    }

    100% {
        -webkit-transform: scaleX(1);
        -moz-transform: scaleX(1);
        -ms-transform: scaleX(1);
        transform: scaleX(1);
    }
}

@keyframes tick-anime2 {
    0% {
        -webkit-transform: scaleY(0);
        -moz-transform: scaleY(0);
        -ms-transform: scaleY(0);
        transform: scaleY(0);
    }

    100% {
        -webkit-transform: scaleY(1);
        -moz-transform: scaleY(1);
        -ms-transform: scaleY(1);
        transform: scaleY(1);
    }
}

@keyframes tick-anime3 {
    0% {
        background-color: rgba(255, 255, 255, 0);
        -webkit-transform: rotate(35deg) scale(2);
        -moz-transform: rotate(35deg) scale(2);
        -ms-transform: rotate(35deg) scale(2);
        transform: rotate(35deg) scale(2);
    }

    100% {
        background-color: rgba(255, 255, 255, 0.2);
        -webkit-transform: rotate(45deg) scale(1);
        -moz-transform: rotate(45deg) scale(1);
        -ms-transform: rotate(45deg) scale(1);
        transform: rotate(45deg) scale(1);
    }
}

@keyframes c-tick-anime3 {
    0% {
        background-color: rgba(233, 253, 214, 0);
        -webkit-transform: rotate(35deg) scale(2);
        -moz-transform: rotate(35deg) scale(2);
        -ms-transform: rotate(35deg) scale(2);
        transform: rotate(35deg) scale(2);
    }

    100% {
        background-color: #E9FDD6;
        -webkit-transform: rotate(45deg) scale(1);
        -moz-transform: rotate(45deg) scale(1);
        -ms-transform: rotate(45deg) scale(1);
        transform: rotate(45deg) scale(1);
    }
}

.alert {
    position: relative;
    padding: 6px 12px;
    border: 1px solid #000;
    color: #000000;
    font-size: 13px;
    font-weight: 700;
}

.alert a,
.alert a.alert-link {
    font-weight: 700;
    color: #000000;
}

.alert p {
    font-size: 13px;
    font-weight: 700;
    margin-bottom: 18px;
}

.alert.alert-primary {
    background-color: #e2f0ff;
    border-color: #3a86d6;
}

.alert.alert-primary hr {
    border-top-color: #3a86d6;
}

.alert.alert-secondary {
    background-color: #f0f0f0;
    border-color: #8e9396;
}

.alert.alert-secondary hr {
    border-top-color: #8e9396;
}

.alert.alert-success {
    background-color: #F7FFF0;
    border-color: #8CCB57;
}

.alert.alert-success hr {
    border-top-color: #8CCB57;
}

.alert.alert-danger {
    background-color: #FFFAFA;
    border-color: #F55050;
}

.alert.alert-danger hr {
    border-top-color: #F55050;
}

.alert.alert-warning {
    background-color: #fff8e1;
    border-color: #f1cb4b;
}

.alert.alert-warning hr {
    border-top-color: #f1cb4b;
}

.alert.alert-info {
    background-color: #dcedf1;
    border-color: #42bfdb;
}

.alert.alert-info hr {
    border-top-color: #42bfdb;
}

.alert.alert-light {
    background-color: #fefefe;
    border-color: #a7a4a4;
}

.alert.alert-light hr {
    border-top-color: #a7a4a4;
}

.alert.alert-dark {
    background-color: #d6d8d9;
    border-color: #525557;
}

.alert.alert-dark hr {
    border-top-color: #525557;
}

.alert.with-icon {
    padding-left: 32px;
}

.alert.with-icon[class*=alert-]:before {
    position: absolute;
    font-family: "Font Awesome 5 Free";
    font-style: normal;
    font-weight: 600;
    top: 7px;
    left: 7px;
    width: 20px;
    font-size: 12px;
    text-align: center;
}

.alert.with-icon.alert-primary:before {
    content: "\f12a";
    color: #3a86d6;
}

.alert.with-icon.alert-secondary:before {
    content: "\f12a";
    color: #8e9396;
}

.alert.with-icon.alert-success:before {
    content: "\f00c";
    color: #8CCB57;
}

.alert.with-icon.alert-danger:before {
    content: "\f071";
    color: #F55050;
}

.alert.with-icon.alert-warning:before {
    content: "\f06a";
    color: #f1cb4b;
}

.alert.with-icon.alert-info:before {
    content: "\f129";
    color: #42bfdb;
}

.alert.with-icon.alert-light:before {
    content: "\f12a";
    color: #a7a4a4;
}

.alert.with-icon.alert-dark:before {
    content: "\f12a";
    color: #525557;
}

.alert .close {
    color: #727272;
    font-size: 0.9rem;
    padding: 3px;
    outline: none;
}

.alert .close span {
    color: #727272;
}

.form-body.without-side .website-logo {
    top: 70px;
    left: 50%;
    margin-left: -50px;
    right: initial;
    bottom: initial;
    display: inline-block;
}

.form-body.without-side .website-logo-inside .logo {
    background-image: url("../images/logo-dark.svg");
}

.form-body.without-side .form-holder .form-content ::-webkit-input-placeholder {
    color: #000;
}

.form-body.without-side .form-holder .form-content :-moz-placeholder {
    color: #000;
}

.form-body.without-side .form-holder .form-content ::-moz-placeholder {
    color: #000;
}

.form-body.without-side .form-holder .form-content :-ms-input-placeholder {
    color: #000;
}

.form-body.without-side h3 {
    color: #000;
}

.form-body.without-side p {
    color: #000;
}

.form-body.without-side label {
    color: #000;
}

.form-body.without-side .img-holder {
    z-index: 0;
    width: 100%;
    overflow: hidden;
}

.form-body.without-side .img-holder .info-holder img {
    display: none;
    max-width: 534px;
    -webkit-animation: zoom-in-img 50s linear 0s infinite;
    -moz-animation: zoom-in-img 50s linear 0s infinite;
    -ms-animation: zoom-in-img 50s linear 0s infinite;
    animation: zoom-in-img 50s linear 0s infinite;
    -webkit-transform-origin: center center;
    -moz-transform-origin: center center;
    -ms-transform-origin: center center;
    transform-origin: center center;
    -webkit-transform: scale(1);
    -moz-transform: scale(1);
    -ms-transform: scale(1);
    transform: scale(1);
}

.form-body.without-side .form-holder {
    margin-left: 0;
}

.form-body.without-side .form-holder .form-content {
    background-color: transparent;
}

.form-body.without-side .form-content {
    padding: 125px 60px 60px;
    -webkit-perspective: 800px;
    -moz-perspective: 800px;
    -ms-perspective: 800px;
    perspective: 800px;
}

.form-body.without-side .form-content .form-items {
    padding: 35px 30px;
    border-radius: 10px;
    background-color: #fff;
    -webkit-box-shadow: 0 6px 15px rgba(0, 0, 0, 0.16);
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.16);
}

.form-body.without-side .form-content .form-items form {
    margin-bottom: 0;
}

.form-body.without-side .form-content .form-items .other-links {
    margin-top: 38px;
    margin-bottom: 30px;
}

.form-body.without-side .form-content .form-items .other-links .text {
    font-size: 13px;
    font-weight: 300;
    color: #000;
    margin-bottom: 15px;
}

.form-body.without-side .form-content .form-items .other-links a {
    display: inline-block;
    padding: 5px;
    border-radius: 2px;
    color: #000;
    background-color: #F7F7F7;
}

.form-body.without-side .form-content .form-items .other-links a i {
    width: 18px;
    height: 18px;
    font-size: 9px;
    margin-left: 0;
}

.form-body.without-side .form-content .form-items .page-links {
    margin-bottom: 0;
}

.form-body.without-side .form-content .form-items .page-links a {
    font-weight: 700;
}

.form-body.without-side .form-content .form-items .page-links a:after {
    bottom: -3px;
}

.form-body.without-side .form-content .page-links a {
    color: #000;
}

.form-body.without-side .form-content .page-links a:after {
    background-color: rgba(222, 222, 222, 0.7);
}

.form-body.without-side .form-content .page-links a.active:after,
.form-body.without-side .form-content .page-links a:hover:after,
.form-body.without-side .form-content .page-links a:focus:after {
    background-color: #0092FE;
}

.form-body.without-side .form-content input,
.form-body.without-side .form-content .dropdown-toggle.btn-default {
    border: 0;
    background-color: #F7F7F7;
    color: #000000;
}

.form-body.without-side .form-content input:hover,
.form-body.without-side .form-content input:focus,
.form-body.without-side .form-content .dropdown-toggle.btn-default:hover,
.form-body.without-side .form-content .dropdown-toggle.btn-default:focus {
    border: 0;
    background-color: #eaeaea;
    color: #000000;
}

.form-body.without-side .form-content textarea {
    background-color: #F7F7F7;
    border: 0;
    color: #000000;
}

.form-body.without-side .form-content textarea:hover,
.form-body.without-side .form-content textarea:focus {
    border: 0;
    background-color: #eaeaea;
    color: #000000;
}

.form-body.without-side .form-content input[type=checkbox]:not(:checked)+label,
.form-body.without-side .form-content input[type=checkbox]:checked+label,
.form-body.without-side .form-content input[type=radio]:not(:checked)+label,
.form-body.without-side .form-content input[type=radio]:checked+label {
    color: #000;
}

.form-body.without-side .form-content input[type=checkbox]:checked+label,
.form-body.without-side .form-content input[type=radio]:checked+label {
    color: #000;
}

.form-body.without-side .form-content input[type=checkbox]:checked+label:before,
.form-body.without-side .form-content input[type=radio]:checked+label:before {
    background: #000;
    border: 0px solid #000;
}

.form-body.without-side .form-content input[type=checkbox]:not(:checked)+label:before,
.form-body.without-side .form-content input[type=radio]:not(:checked)+label:before {
    background: transparent;
    border: 2px solid #000;
}

.form-body.without-side .form-content input[type=checkbox]:not(:checked)+label:after,
.form-body.without-side .form-content input[type=checkbox]:checked+label:after {
    color: #fff;
}

.form-body.without-side .form-content input[type=radio]:not(:checked)+label:after,
.form-body.without-side .form-content input[type=radio]:checked+label:after {
    background-color: #fff;
}

.form-body.without-side .form-content .custom-options input[type=checkbox]:not(:checked)+label,
.form-body.without-side .form-content .custom-options input[type=checkbox]:checked+label,
.form-body.without-side .form-content .custom-options input[type=radio]:not(:checked)+label,
.form-body.without-side .form-content .custom-options input[type=radio]:checked+label {
    color: #606060;
    background-color: #F7F7F7;
}

.form-body.without-side .form-content .custom-options input[type=checkbox]:checked+label,
.form-body.without-side .form-content .custom-options input[type=radio]:checked+label {
    color: #fff;
    background-color: #57D38C;
    -webkit-box-shadow: 0 3px 8px rgba(74, 230, 142, 0.35);
    box-shadow: 0 3px 8px rgba(74, 230, 142, 0.35);
}

.form-body.without-side .form-content .form-button {
    margin-bottom: 0;
}

.form-body.without-side .form-content .form-button .ibtn {
    background-color: #29A4FF;
    color: #fff;
    -webkit-box-shadow: 0 0 0 rgba(0, 0, 0, 0.16);
    box-shadow: 0 0 0 rgba(0, 0, 0, 0.16);
}

.form-body.without-side .form-content .form-button .ibtn:hover,
.form-body.without-side .form-content .form-button .ibtn:focus {
    -webkit-box-shadow: 0 5px 6px rgba(0, 0, 0, 0.16);
    box-shadow: 0 5px 6px rgba(0, 0, 0, 0.16);
}

.form-body.without-side .form-content .form-button a {
    font-weight: 300;
    color: #000;
}

.form-body.without-side .form-content .form-sent {
    padding: 35px 30px;
    border-radius: 10px;
    background-color: #fff;
    -webkit-box-shadow: 0 6px 15px rgba(0, 0, 0, 0.16);
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.16);
    -webkit-transform: rotateY(-180deg);
    -moz-transform: rotateY(-180deg);
    -ms-transform: rotateY(-180deg);
    transform: rotateY(-180deg);
}

.form-body.without-side .form-content .form-sent.show-it {
    -webkit-transform: rotateY(0deg);
    -moz-transform: rotateY(0deg);
    -ms-transform: rotateY(0deg);
    transform: rotateY(0deg);
}

.form-body.without-side .form-content .form-sent .tick-holder .tick-icon {
    -webkit-animation: c-tick-anime3 0.7s cubic-bezier(0.34, 1.61, 0.7, 1) 0s forwards;
    -moz-animation: c-tick-anime3 0.7s cubic-bezier(0.34, 1.61, 0.7, 1) 0s forwards;
    -ms-animation: c-tick-anime3 0.7s cubic-bezier(0.34, 1.61, 0.7, 1) 0s forwards;
    animation: c-tick-anime3 0.7s cubic-bezier(0.34, 1.61, 0.7, 1) 0s forwards;
    background-color: rgba(233, 253, 214, 0);
}

.form-body.without-side .form-content .form-sent .tick-holder .tick-icon:before {
    background-color: #8CCB57;
}

.form-body.without-side .form-content .form-sent .tick-holder .tick-icon:after {
    background-color: #8CCB57;
}

.form-body.without-side .form-content .form-sent h3 {
    color: #000;
}

.form-body.without-side .form-content .form-sent p {
    color: #000;
}

.form-body.without-side .form-content .form-sent .info-holder {
    border-top: 1px solid rgba(0, 0, 0, 0.5);
}

.form-body.without-side .form-content .form-sent .info-holder span {
    color: #000;
}

.form-body.without-side .form-content .form-sent .info-holder a {
    color: #000;
}

.form-body.without-side .form-content .hide-it {
    -webkit-transform: rotateY(180deg);
    -moz-transform: rotateY(180deg);
    -ms-transform: rotateY(180deg);
    transform: rotateY(180deg);
}

@keyframes zoom-in-img {
    0% {
        -webkit-transform: scale(1);
        -moz-transform: scale(1);
        -ms-transform: scale(1);
        transform: scale(1);
    }

    50% {
        -webkit-transform: scale(1.15);
        -moz-transform: scale(1.15);
        -ms-transform: scale(1.15);
        transform: scale(1.15);
    }

    100% {
        -webkit-transform: scale(1);
        -moz-transform: scale(1);
        -ms-transform: scale(1);
        transform: scale(1);
    }
}

/* -----------------------------------
    2 - Responsive Styles
------------------------------------*/
@media (max-width: 992px) {
    .img-holder {
        display: none;
    }

    .form-holder {
        margin-left: 0;
    }

    .form-holder .form-content {
        padding: 125px 60px 60px;
    }

    .form-body.on-top .form-holder .form-content,
    .form-body.on-top-mobile .form-holder .form-content {
        padding: 60px;
    }

    .website-logo {
        position: relative;
        top: 50px;
        left: 50px;
        right: initial;
        bottom: initial;
    }

    .website-logo .logo {
        background-image: url("../images/logo-light.svg");
    }

    .form-body.without-side .img-holder {
        display: inline-block;
    }

    .form-body.without-side .website-logo .logo {
        background-image: url("../images/logo-light.svg");
    }

    .form-body.without-side .form-holder .form-content {
        padding: 125px 30px 60px;
    }

    .form-body.on-top-mobile .website-logo {
        position: absolute;
    }

    .form-body.on-top-mobile .img-holder {
        display: block;
        position: relative;
        width: 100%;
        min-height: initial;
        height: initial;
        overflow: initial;
        padding: 40px;
    }

    .form-body.on-top-mobile .img-holder .info-holder.simple-info h3 {
        margin-bottom: 16px;
    }

    .form-body.on-top-mobile .img-holder .info-holder.simple-info p {
        margin-bottom: 10px;
    }

    .form-body.on-top-mobile .img-holder .info-holder.simple-info img {
        margin-bottom: 20px;
    }

    .form-body.on-top-mobile .form-holder {
        margin-left: 0;
    }
}

@media (max-width: 575px) {

    .form-body.on-top .img-holder,
    .form-body.on-top-mobile .img-holder {
        padding: 90px 40px 40px;
    }

    .form-content .row.top-padding .form-button {
        text-align: left !important;
        margin-top: 30px;
    }
}

*************scripts***************


window.onload = function () {
    var re_enter = document.getElementById('reset');

    re_enter.addEventListener('submit', (e) => {
        var password = document.getElementById('passwrd');
        var reenter = document.getElementById('repasswrd');

        //validate the password
        if (password.value.length < 8) {

            const element = document.getElementById('hide-passwrd');
            element.className = "";
            const message = document.getElementById('passwrd-warning')
            message.innerText = "8 characters should be included in the password"
            e.preventDefault();

        }

        else if (!/^(?=.*[A-Za-z])(?=.*\d)(?=.*[@$!%*#?&])[A-Za-z\d@$!%*#?&]{8,}$/.test(password.value)) {
            const element = document.getElementById('hide-passwrd');
            console.log("in the character");

            element.className = "";
            const message = document.getElementById('passwrd-warning')
            message.innerText = "At least a number and a special character should be included in the password"
            e.preventDefault();

        };
        //compare the entered password 
        if (password.value.localeCompare(reenter.value) !== 0) {
            const element = document.getElementById('hide-repasswrd');
            element.className = "";
            const message = document.getElementById('repasswrd-warning')
            message.innerText = "Re-entered password does not match with the given password"
            e.preventDefault();
        }

    });
};

****************validatios*************


window.onload = function () {
    var form = document.getElementById('register');

    form.addEventListener('submit', (e) => {
        var policyNum = document.getElementById('policyNum');
        var age = document.getElementById('age');
        var username = document.getElementById('username');
        var password = document.getElementById('passwrd');
        var NIC = document.getElementById('NICNum');
        var contNumber = document.getElementById('number');
        var reenter = document.getElementById('repasswrd');

       //validate the policy number
       if(policyNum.value.length==8 && !(isNaN(policyNum.value)) ){
          if(policyNum.value.slice(0,2)< 01 || policyNum.value.slice(0,2)> 10){
            const element = document.getElementById('hide-tr-policy');
            element.className = "";
            const message = document.getElementById('policy-warning')
            message.innerText = "Invalid policy number"
            e.preventDefault();
            }
        }
        else{
            const element = document.getElementById('hide-tr-policy');
            element.className = "";
            const message = document.getElementById('policy-warning')
            message.innerText = "Invalid policy number"
            e.preventDefault();
        }

        //validate the age
         if(age.value <0 || age.value>120){
            const element = document.getElementById('hide-tr-age');
            element.className = "";
            const message = document.getElementById('age-warning')
            message.innerText = "Invalid age"
            e.preventDefault();
        };
        
        //validae the NIC
        if (NIC.value.length ==10)
        {
            if(NIC.value.slice(-1) == 'v' || NIC.value.slice(-1) == 'V')
            {
                if (isNaN(NIC.value.slice(0,9))){
                    const element = document.getElementById('hide-tr-NIC');
                    element.className = "";
                    const message = document.getElementById('NIC-warning')
                    message.innerText = "Invalid NIC number"
                       e.preventDefault();
                }
            }
            else 
                {
                    const element = document.getElementById('hide-tr-NIC');
                    element.className = "";
                    const message = document.getElementById('NIC-warning')
                    message.innerText = "Invalid NIC number"
                     e.preventDefault();
                }
        
        }else if(NIC.value.length ==12){
           if (isNaN(NIC.value)){
            const element = document.getElementById('hide-tr-NIC');
            element.className = "";
            const message = document.getElementById('NIC-warning')
            message.innerText = "Invalid NIC number"
       e.preventDefault();
           }
        }
        else{
            const element = document.getElementById('hide-tr-NIC');
            element.className = "";
            const message = document.getElementById('NIC-warning')
            message.innerText = "Invalid NIC number"
       e.preventDefault();
        };

        //validate the contact number
        if(contNumber.value.length != 10 || isNaN(contNumber.value)){
            const element = document.getElementById('hide-tr-number');
            element.className = "";
            const message = document.getElementById('number-warning')
            message.innerText = "Invalid Contact number"
    e.preventDefault();
        }

        //validate the username
        if (username.value.length < 7 || username.value.length > 13) {
            const element = document.getElementById('hide-tr-username');
            element.className = "";
            const message = document.getElementById('username-warning')
            message.innerText = "Enter username with 8-12 characters"
            e.preventDefault();
        };

         //validate the password
         if (password.value.length < 7 ) {
            const element = document.getElementById('hide-tr-passwrd');
            element.className = "";
            const message = document.getElementById('passwrd-warning')
            message.innerText = "8 characters should be included in the password"
            e.preventDefault();
        };

        if (!/^(?=.*[A-Za-z])(?=.*\d)(?=.*[@$!%*#?&])[A-Za-z\d@$!%*#?&]{8,}$/.test(password.value)) {
            const element = document.getElementById('hide-tr-passwrd');
            element.className = "";
            const message = document.getElementById('passwrd-warning')
            message.innerText = "At least a number and a special character should be included in the password"
            e.preventDefault();
        }
        //compare the entered password 
        if (password.value.localeCompare(reenter.value) !== 0 ){
            const element = document.getElementById('hide-tr-repasswrd');
            element.className = "";
            const message = document.getElementById('repasswrd-warning')
            message.innerText = "Re-entered password does not match with the given password"
            e.preventDefault();
        }
        
    });
};


    
*********************register**************
<?php
include 'dbConnection039.php';
$conn = Database::getConnection();
?>
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">

    <link href="css/jpreloader.css" rel="stylesheet" type="text/css">
    <link id="bootstrap" href="css/bootstrap.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-grid" href="css/bootstrap-grid.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-reboot" href="css/bootstrap-reboot.min.css" rel="stylesheet" type="text/css" />
    <link href="css/animate.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.carousel.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.theme.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.transitions.css" rel="stylesheet" type="text/css" />
    <link href="css/magnific-popup.css" rel="stylesheet" type="text/css" />
    <link href="css/jquery.countdown.css" rel="stylesheet" type="text/css" />
    <link href="css/style.css" rel="stylesheet" type="text/css" />

    <link id="colors" href="css/colors/scheme-01.css" rel="stylesheet" type="text/css" />
    <link href="css/coloring.css" rel="stylesheet" type="text/css" />

    <link rel="stylesheet" type="text/css" href="css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="css/theme039.css">
    <link rel="stylesheet" type="text/css" href="css/style039.css">
</head>

<body>
    <div>
        <?php include 'includes/header.php'; ?>
    </div>
    <section class="form-body">
        <div class="row">
            <div class="col">
            </div>
        </div>
        <div class="col">
            <div class=" row">
                <div class="form-content">
                    <div class="form-items">
                        <h1>Register as a customer</h1>
                        <form action="serve_register039.php" method="POST" name="register" id="register">
                            <table>
                                <tbody>
                                    <tr>
                                        <td><label>Title</label></td>
                                        <td>
                                            <div class="form-group">
                                                <div class="form check">
                                                    <div class="container">
                                                        <div class="row row-cols-2 row-cols-lg-3">
                                                            <input class="col " class="form-check-input" type="radio" id="Mr" name="title" value="Mr">
                                                            <label for="Mr">Mr</label>
                                                            <input class="col " class="form-check-input" type="radio" id="Ms" name="title" value="Ms">
                                                            <label for="Ms">Ms</label>
                                                            <input class="col " class="form-check-input" type="radio" id="Rev" name="title" value="Rev">
                                                            <label for="Rev">Rev</label>
                                                        </div>
                                                    </div>
                                                </div>
                                            </div>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td><label>Full name</label></td>
                                        <td>
                                            <input type="text" placeholder="Full Name" name="fullName" id="fullName" required>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td><label>Policy Number</label></td>
                                        <td>
                                            <input type="text" placeholder="Policy Number" name="policyNum" id="policyNum" required>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td></td>
                                        <td>
                                            <p style="font-size: 14px">If you have more than one policies enter one of those</p>
                                        </td>

                                    </tr>
                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-policy">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="policy-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>


                                    <tr>
                                        <td><label>Age</label></td>
                                        <td>
                                            <input type="number" placeholder="Age" name="age" id="age" required>
                                        </td>
                                    </tr>

                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-age">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="age-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>

                                    <tr>
                                        <td><label>NIC Number</label></td>
                                        <td>
                                            <input type="text" placeholder="NIC number" name="NICNum" id="NICNum" required>
                                        </td>
                                    </tr>

                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-NIC">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="NIC-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>

                                    <tr>
                                        <td><label>Address</label></td>
                                        <td>
                                            <input type="text" placeholder="Address" name="address" id="address" required>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td><label>Email address</label></td>
                                        <td>
                                            <input type="email" placeholder="email@mail.com" name="email" id="email" required>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td><label>Contact Number</label></td>
                                        <td>
                                            <input type="text" placeholder="07XXXXXXXX" name="number" id="number" required>
                                        </td>
                                    </tr>
                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-number">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="number-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>

                                    <tr>
                                        <td><label>User Name</label></td>
                                        <td>
                                            <input type="text" placeholder="userName" name="username" id="username" required>
                                        </td>
                                    </tr>
                                    <tr classname="d-none" id="hide-tr-username">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="username-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td><label>Password</label></td>
                                        <td>
                                            <input type="password" placeholder="password" name="passwrd" id="passwrd" required>
                                            <br>
                                        </td>
                                    <tr>
                                        <td></td>
                                        <td>
                                            <p style="font-size: 14px">(8 or more characters are required including numerical values and special characters)</p>
                                        </td>
                                    </tr>
                                    </tr>
                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-passwrd">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="passwrd-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>

                                    <tr>
                                        <td><label>Confirm the Password</label></td>
                                        <td>
                                            <input type="password" placeholder="re-enter the password" name="repasswrd" id="repasswrd" required>
                                        </td>
                                    </tr>

                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-repasswrd">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="repasswrd-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>

                                    <tr>
                                        <td>
                                            <div class="form-button ">
                                            <input type="submit" class="ibtn " value="clear form" onclick="this.form.reset();">
                                            </div>
                                        </td>
                                        <td>
                                            <div class="form-button ">
                                            <input type="submit" class="ibtn" value="Register">
                                            </div>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </form>
                    </div>
                </div>
            </div>
        </div>
        </div>
    </section>

    <div>
        <?php include 'includes/footer.php' ?>
    </div>

    <script src="js/register_validations039.js"></script>
    <script src="js/jquery.min.js"></script>
    <script src="js/jpreLoader.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script src="js/bootstrap.bundle.min.js"></script>
    <script src="js/wow.min.js"></script>
    <script src="js/jquery.isotope.min.js"></script>
    <script src="js/easing.js"></script>
    <script src="js/owl.carousel.js"></script>
    <script src="js/jquery.magnific-popup.min.js"></script>
    <script src="js/enquire.min.js"></script>
    <script src="js/jquery.stellar.min.js"></script>
    <script src="js/jquery.plugin.js"></script>
    <script src="js/typed.js"></script>
    <script src="js/designesia.js"></script>

</body>

</html>

************************login**************
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">

    <link href="css/jpreloader.css" rel="stylesheet" type="text/css">
    <link id="bootstrap" href="css/bootstrap.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-grid" href="css/bootstrap-grid.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-reboot" href="css/bootstrap-reboot.min.css" rel="stylesheet" type="text/css" />
    <link href="css/animate.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.carousel.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.theme.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.transitions.css" rel="stylesheet" type="text/css" />
    <link href="css/magnific-popup.css" rel="stylesheet" type="text/css" />
    <link href="css/jquery.countdown.css" rel="stylesheet" type="text/css" />
    <link href="css/style.css" rel="stylesheet" type="text/css" />

    <link id="colors" href="css/colors/scheme-01.css" rel="stylesheet" type="text/css" />
    <link href="css/coloring.css" rel="stylesheet" type="text/css" />

    <link rel="stylesheet" type="text/css" href="css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="css/theme039.css">
    <link rel="stylesheet" type="text/css" href="css/style039.css">

</head>

<body>
    <?php include 'includes/header.php'; ?>

    <div class="form-body">
        <div class="row-small form-holder">
            <section >
                    <div class="form-content">
                        <div class="form-items ">
                            <div class="small-forms" >
                                <h1>Customer Login</h1>
                                <br>
                                <form action="serve_login039.php" method="post" id="login_validate" name="login_validate">
                                    <input class="form-control" type="text" name="username" id="username" placeholder="Username" required>
                                    <input class="form-control" type="password" name="password" id="passwrd" placeholder="Password" required>

                                    <button id="submit" type="submit" class="btn-update" style="width: auto;">Login</button>
                                    <a href="forget039.php">Forgot password?</a>


                                </form>
                                <div class="other-links">

                                    <p>New User? <a href="register039.php">Register as a new Customer</a></p>
                                </div>
                            </div>
                        </div>
                    </div>
            </section>
        </div>

    </div>
    <div>
        <?php include 'includes/footer.php' ?>
    </div>

    <script src="js/jquery.min.js"></script>
    <script src="js/jpreLoader.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script src="js/bootstrap.bundle.min.js"></script>
    <script src="js/wow.min.js"></script>
    <script src="js/jquery.isotope.min.js"></script>
    <script src="js/easing.js"></script>
    <script src="js/owl.carousel.js"></script>
    <script src="js/jquery.magnific-popup.min.js"></script>
    <script src="js/enquire.min.js"></script>
    <script src="js/jquery.stellar.min.js"></script>
    <script src="js/jquery.plugin.js"></script>
    <script src="js/typed.js"></script>
    <script src="js/designesia.js"></script>

</body>

</html>

****************************forget password************
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title> Illuminating_Insurance </title>
    <link href="css/jpreloader.css" rel="stylesheet" type="text/css">
    <link id="bootstrap" href="css/bootstrap.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-grid" href="css/bootstrap-grid.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-reboot" href="css/bootstrap-reboot.min.css" rel="stylesheet" type="text/css" />
    <link href="css/animate.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.carousel.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.theme.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.transitions.css" rel="stylesheet" type="text/css" />
    <link href="css/magnific-popup.css" rel="stylesheet" type="text/css" />
    <link href="css/jquery.countdown.css" rel="stylesheet" type="text/css" />
    <link href="css/style.css" rel="stylesheet" type="text/css" />


    <link id="colors" href="css/colors/scheme-01.css" rel="stylesheet" type="text/css" />
    <link href="css/coloring.css" rel="stylesheet" type="text/css" />


    <link rel="stylesheet" href="revolution/css/settings.css" type="text/css">
    <link rel="stylesheet" href="revolution/css/layers.css" type="text/css">
    <link rel="stylesheet" href="revolution/css/navigation.css" type="text/css">

    <link rel="stylesheet" type="text/css" href="css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="css/fontawesome-all.min.css">
    <link rel="stylesheet" type="text/css" href="css/theme039.css">
    <link rel="stylesheet" type="text/css" href="css/style039.css">
</head>

<body>
    <?php include 'includes/header.php'; ?>

    <div class="form-body">
        <div class="row-small form-holder">
            <section >
                    <div class="form-content">
                        <div class="form-items ">
                             <h1>Password Reset</h1>
                                <p>To reset your password, enter the user name and email address you used to register</p>
                                <form id="forget" method="POST" action="serve_forget039.php">
                                    <input class="form-control" type="text" name="username" id="username" placeholder="User name" required>
                                    <input class="form-control" type="email" name="email" id="email" placeholder="email@mail.com" required>
                                    <div class="form-button">
                                        <button id="submit" type="submit" class="btn-update ibtn">Reset</button>
                                    </div>
                                </form>
                            </div>
                        </div>
            </section>
        </div>
    </div>
    <div>
        <?php include 'includes/footer.php' ?>
    </div>


    <script src="js/jquery.min.js"></script>
    <script src="js/popper.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script src="js/main.js"></script>
    <script src="js/jquery.min.js"></script>
    <script src="js/jpreLoader.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script src="js/bootstrap.bundle.min.js"></script>
    <script src="js/wow.min.js"></script>
    <script src="js/jquery.isotope.min.js"></script>
    <script src="js/easing.js"></script>
    <script src="js/owl.carousel.js"></script>
    <script src="js/jquery.magnific-popup.min.js"></script>
    <script src="js/enquire.min.js"></script>
    <script src="js/jquery.stellar.min.js"></script>
    <script src="js/jquery.plugin.js"></script>
    <script src="js/typed.js"></script>
    <script src="js/designesia.js"></script>

</body>

</html>

************************reset paaword***************
<?php
include 'dbConnection039.php';
$conn = Database::getConnection();
?>
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">

    <link href="css/jpreloader.css" rel="stylesheet" type="text/css">
    <link id="bootstrap" href="css/bootstrap.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-grid" href="css/bootstrap-grid.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-reboot" href="css/bootstrap-reboot.min.css" rel="stylesheet" type="text/css" />
    <link href="css/animate.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.carousel.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.theme.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.transitions.css" rel="stylesheet" type="text/css" />
    <link href="css/magnific-popup.css" rel="stylesheet" type="text/css" />
    <link href="css/jquery.countdown.css" rel="stylesheet" type="text/css" />
    <link href="css/style.css" rel="stylesheet" type="text/css" />

    <link id="colors" href="css/colors/scheme-01.css" rel="stylesheet" type="text/css" />
    <link href="css/coloring.css" rel="stylesheet" type="text/css" />

    <link rel="stylesheet" type="text/css" href="css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="css/theme039.css">
    <link rel="stylesheet" type="text/css" href="css/style039.css">
</head>

<body>
    <div>
        <?php include 'includes/header.php'; ?>
    </div>
    <section class="form-body">
        <div class="row">
            <div class="col">
            </div>
        </div>
        <div class="col">
            <div class=" row">
                <div class="form-content">
                    <div class="form-items">
                        <h1>Register as a customer</h1>
                        <form action="serve_register039.php" method="POST" name="register" id="register">
                            <table>
                                <tbody>
                                    <tr>
                                        <td><label>Title</label></td>
                                        <td>
                                            <div class="form-group">
                                                <div class="form check">
                                                    <div class="container">
                                                        <div class="row row-cols-2 row-cols-lg-3">
                                                            <input class="col " class="form-check-input" type="radio" id="Mr" name="title" value="Mr">
                                                            <label for="Mr">Mr</label>
                                                            <input class="col " class="form-check-input" type="radio" id="Ms" name="title" value="Ms">
                                                            <label for="Ms">Ms</label>
                                                            <input class="col " class="form-check-input" type="radio" id="Rev" name="title" value="Rev">
                                                            <label for="Rev">Rev</label>
                                                        </div>
                                                    </div>
                                                </div>
                                            </div>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td><label>Full name</label></td>
                                        <td>
                                            <input type="text" placeholder="Full Name" name="fullName" id="fullName" required>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td><label>Policy Number</label></td>
                                        <td>
                                            <input type="text" placeholder="Policy Number" name="policyNum" id="policyNum" required>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td></td>
                                        <td>
                                            <p style="font-size: 14px">If you have more than one policies enter one of those</p>
                                        </td>

                                    </tr>
                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-policy">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="policy-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>


                                    <tr>
                                        <td><label>Age</label></td>
                                        <td>
                                            <input type="number" placeholder="Age" name="age" id="age" required>
                                        </td>
                                    </tr>

                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-age">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="age-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>

                                    <tr>
                                        <td><label>NIC Number</label></td>
                                        <td>
                                            <input type="text" placeholder="NIC number" name="NICNum" id="NICNum" required>
                                        </td>
                                    </tr>

                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-NIC">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="NIC-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>

                                    <tr>
                                        <td><label>Address</label></td>
                                        <td>
                                            <input type="text" placeholder="Address" name="address" id="address" required>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td><label>Email address</label></td>
                                        <td>
                                            <input type="email" placeholder="email@mail.com" name="email" id="email" required>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td><label>Contact Number</label></td>
                                        <td>
                                            <input type="text" placeholder="07XXXXXXXX" name="number" id="number" required>
                                        </td>
                                    </tr>
                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-number">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="number-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>

                                    <tr>
                                        <td><label>User Name</label></td>
                                        <td>
                                            <input type="text" placeholder="userName" name="username" id="username" required>
                                        </td>
                                    </tr>
                                    <tr classname="d-none" id="hide-tr-username">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="username-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td><label>Password</label></td>
                                        <td>
                                            <input type="password" placeholder="password" name="passwrd" id="passwrd" required>
                                            <br>
                                        </td>
                                    <tr>
                                        <td></td>
                                        <td>
                                            <p style="font-size: 14px">(8 or more characters are required including numerical values and special characters)</p>
                                        </td>
                                    </tr>
                                    </tr>
                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-passwrd">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="passwrd-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>

                                    <tr>
                                        <td><label>Confirm the Password</label></td>
                                        <td>
                                            <input type="password" placeholder="re-enter the password" name="repasswrd" id="repasswrd" required>
                                        </td>
                                    </tr>

                                    <!-- warning -->
                                    <tr classname="d-none" id="hide-tr-repasswrd">
                                        <td colspan="2" class="col-sm-9 col-md-6">
                                            <div class="container" padding:20px>
                                                <div id="repasswrd-warning" class="warning-div"></div>
                                            </div>
                                        </td>
                                    </tr>

                                    <tr>
                                        <td>
                                            <div class="form-button ">
                                            <input type="submit" class="ibtn " value="clear form" onclick="this.form.reset();">
                                            </div>
                                        </td>
                                        <td>
                                            <div class="form-button ">
                                            <input type="submit" class="ibtn" value="Register">
                                            </div>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </form>
                    </div>
                </div>
            </div>
        </div>
        </div>
    </section>

    <div>
        <?php include 'includes/footer.php' ?>
    </div>

    <script src="js/register_validations039.js"></script>
    <script src="js/jquery.min.js"></script>
    <script src="js/jpreLoader.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script src="js/bootstrap.bundle.min.js"></script>
    <script src="js/wow.min.js"></script>
    <script src="js/jquery.isotope.min.js"></script>
    <script src="js/easing.js"></script>
    <script src="js/owl.carousel.js"></script>
    <script src="js/jquery.magnific-popup.min.js"></script>
    <script src="js/enquire.min.js"></script>
    <script src="js/jquery.stellar.min.js"></script>
    <script src="js/jquery.plugin.js"></script>
    <script src="js/typed.js"></script>
    <script src="js/designesia.js"></script>

</body>

</html>

*************************logout************************
<html>
<head>

</head>
<body>
<?php
session_start();
session_destroy();
echo "<div style='margin:20% 30%;'><h4>You have been logged out.</h4></div>";
header('Refresh:1; URL=login039.php');
?>
</body>
</html>

***********************************account Profile***************
<?php
include('./dbConnection039.php');
session_start();

$conn = Database::getConnection();
// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
} else if (!isset($_SESSION["user"])) {
    header("location: login039.php");
}

$user = $_SESSION["user"];


//get the details of the logged user
$userdetails = "SELECT * FROM customer_details WHERE NIC='$user[NIC]'";
$userDetailsResult = mysqli_query($conn, $userdetails);

$userDetailsRow = mysqli_fetch_array($userDetailsResult);


?>
<!-- headerFooterS -->

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8" />

    <meta content="width=device-width, initial-scale=1.0" name="viewport" />

    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">

    <link href="css/jpreloader.css" rel="stylesheet" type="text/css">
    <link id="bootstrap" href="css/bootstrap.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-grid" href="css/bootstrap-grid.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-reboot" href="css/bootstrap-reboot.min.css" rel="stylesheet" type="text/css" />
    <link href="css/animate.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.carousel.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.theme.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.transitions.css" rel="stylesheet" type="text/css" />
    <link href="css/magnific-popup.css" rel="stylesheet" type="text/css" />
    <link href="css/jquery.countdown.css" rel="stylesheet" type="text/css" />
    <link href="css/style.css" rel="stylesheet" type="text/css" /> <!-- dan -->

    <link id="colors" href="css/colors/scheme-01.css" rel="stylesheet" type="text/css" />
    <link href="css/coloring.css" rel="stylesheet" type="text/css" />

    <link rel="stylesheet" type="text/css" href="css/bootstrap.min.css">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js"></script>
    <link rel="stylesheet" type="text/css" href="css/theme039.css">
    <link rel="stylesheet" type="text/css" href="css/style039.css">


</head>

<body>
    <?php include 'includes/header.php'; ?>
    

    <div class="form-body-hr on-top">
        <div class="row">
                <section style="width: 100%;">
            <img style="width:100%" src="images/background/account_profile_top_img039.jpg" alt="">
            </section>
            <hr>

            <div class="container">
                <h1 style="margin-block:0%">My Profile</h1>

                <div class="row">
                    <h3> Personal Details </h3>
                </div>
                <div class="row">
                    <div class="col">
                        <p><b>NIC Number : </b> <?php echo $userDetailsRow[0]; ?> </p>
                    </div>
                    <div class="col">
                        <p><b>Name : </b><?php echo $userDetailsRow[1];
                                            echo " . ";
                                            echo $userDetailsRow[2]; ?> </p>
                    </div>
                </div>
                <hr>
                <div class="row">
                    <h3> Contact Details </h3>
                </div>
                <div class="row">
                    <div class="col">
                        <div class="row">
                            <p><b>E-mail : </b><?php echo $userDetailsRow[4]; ?> </p>
                        </div>
                        <!-- Update email -->
                        <div class="update-row-btn">
                            <!-- Trigger the modal with a button -->
                            <button type="button" class="btn-update" id="up-email-modal">Update email</button>

                            <!-- Modal -->
                            <div class="modal" id="update-modal-email" role="dialog">
                                <div class="modal-dialog">

                                    <!-- Modal content-->
                                    <div class="modal-content">
                                        <div class="modal-header39" style="padding:35px 50px;">
                                            <h4> Update Contact Details</h4>
                                        </div>

                                        <form role="form" name="update-email-form" id="update-email-form" action="update_contact039.php" method="post">
                                            <div class="modal-body39" style="padding:40px 50px;">
                                                <div class="form-group">
                                                    <label for="email">Enter new Email</label>
                                                    <input type="email" class="form-control" id="email" name="email" placeholder="email@mail.com" required>
                                                </div>
                                                <button type="submit" class=" btn-modal-new btn-block btn-update"> Update</button>
                                            </div>
                                            <div class="modal-footer39">
                                                <button type="submit" class="btn btn-modal-new" data-dismiss="modal" onclick="this.form.reset();"><span class="icofont-ui-close"></span> Cancel</button>
                                            </div>
                                        </form>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>


                    <div class="col">
                        <div class="row">
                            <p> <b> Contact Number: </b> <?php echo $userDetailsRow[3] ?> </p>
                        </div>
                        <div class="update-row-btn">

                            <!-- Trigger the modal with a button -->
                            <button type="button" class="btn-update" id="up-number-modal">Update Contact Number</button>

                            <!-- Modal -->
                            <div class="modal" id="update-modal-number" role="dialog">
                                <div class="modal-dialog">

                                    <!-- Modal content-->
                                    <div class="modal-content">
                                        <div class="modal-header39" style="padding:35px 50px;">
                                            <h4> Update Contact Details</h4>
                                        </div>
                                        <form role="form" name="update-number-form" id="update-number-form" action="update_contact039.php" method="post">
                                            <div class="modal-body39" style="padding:40px 50px;">
                                                <div class="form-group">
                                                    <label for="number">Enter new contact Number</label>
                                                    <input type="text" class="form-control" id="number" name="number" placeholder="07XXXXXXXX" required>

                                                    <!-- Warning -->
                                                    <div classname="hidden-md" id="hide-number">
                                                        <label id="number-warning" style="color: #8a0000;"></label>
                                                    </div>
                                                </div>
                                                <button type="submit" class=" btn-modal-new btn-block btn-update"> Update</button>
                                            </div>
                                            <div class="modal-footer39">
                                                <button type="submit" class="btn btn-modal-new" data-dismiss="modal" onclick="this.form.reset();"><span class="icofont-ui-close"></span> Cancel</button>
                                            </div>
                                        </form>
                                    </div>

                                </div>

                            </div>
                        </div>
                    </div>
                </div>
                <hr>

                <!-- <div class="container"> -->
                <h3>My policies</h3>
                <div class="form-group">
                    <table width=100%>
                        <thead>
                            <tr>
                                <th>Policy Type</th>
                                <th>Policy Number</th>
                                <th>Premium of the policy</th>
                                <th>Active/not</th>
                            </tr>
                        </thead>
                        <tbody>
                            <?php
                            //$_POST[NICNumber]
                            $sql = "SELECT * FROM policyncustomer WHERE NIC='$user[NIC]'";
                            $result = mysqli_query($conn, $sql);

                            while ($row = mysqli_fetch_array($result)) {
                                //get the required type from the policy details table

                                $sqltype = "SELECT policy_name
                                     FROM policy_details
                                    WHERE policyNum_pre=$row[1]";

                                $resultype = mysqli_query($conn, $sqltype);
                                $type = mysqli_fetch_array($resultype);

                                echo "<tr>
                                <td>$type[0]</td>
                                 <td> $row[2] </td>
                                 <td> $row[3] </td>
                                 <td> $row[4] </td>
                                 </tr>";
                            }

                            ?>
                        </tbody>
                    </table>
                </div>
                <hr>
            </div>

            <section data-bgimage="url(images/background/account_profile_img039.png)">
                <div class="container">
                    <div class="row">
                        <div class="col-md-6 wow fadeInRight" data-wow-delay=".2s">
                            <h2>Begin a new journey with a new insurance Plan.
                                All is for you..</h2>
                            <a href="ApplyNewPolicy039.php" alt="" class="btn-custom invert med">Apply now..</a>
                        </div>
                    </div>
                </div>
            </section>


            <?php include 'includes/footer.php' ?>

        </div>
    </div>

    <script src="js/popper.min.js"></script>
    <script src="js/main.js"></script>
    <script src="js/update_contact039.js"></script>
    <script src="js/jquery.min.js"></script>
    <script src="js/bootstrap.min.js"></script>
    <script src="js/jpreLoader.min.js"></script>
    <script src="js/wow.min.js"></script>
    <script src="js/jquery.isotope.min.js"></script>
    <script src="js/easing.js"></script>
    <script src="js/owl.carousel.js"></script>
    <script src="js/jquery.magnific-popup.min.js"></script>
    <script src="js/enquire.min.js"></script>
    <script src="js/jquery.stellar.min.js"></script> <!-- dan -->

    <script src="js/jquery.plugin.js"></script>
    <script src="js/typed.js"></script>
    <script src="js/designesia.js"></script> <!-- dan -->



</body>

</html>

******************************application for a new policy**************
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8" />

    <meta content="width=device-width, initial-scale=1.0" name="viewport" />
    <link href="css/jpreloader.css" rel="stylesheet" type="text/css">
    <link id="bootstrap" href="css/bootstrap.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-grid" href="css/bootstrap-grid.min.css" rel="stylesheet" type="text/css" />
    <link id="bootstrap-reboot" href="css/bootstrap-reboot.min.css" rel="stylesheet" type="text/css" />
    <link href="css/animate.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.carousel.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.theme.css" rel="stylesheet" type="text/css" />
    <link href="css/owl.transitions.css" rel="stylesheet" type="text/css" />
    <link href="css/magnific-popup.css" rel="stylesheet" type="text/css" />
    <link href="css/jquery.countdown.css" rel="stylesheet" type="text/css" />
    <link href="css/style.css" rel="stylesheet" type="text/css" />


    <link id="colors" href="css/colors/scheme-01.css" rel="stylesheet" type="text/css" />
    <link href="css/coloring.css" rel="stylesheet" type="text/css" />


    <link rel="stylesheet" href="revolution/css/settings.css" type="text/css">
    <link rel="stylesheet" href="revolution/css/layers.css" type="text/css">
    <link rel="stylesheet" href="revolution/css/navigation.css" type="text/css">

    <link rel="stylesheet" type="text/css" href="css/bootstrap.min.css">
    <link rel="stylesheet" type="text/css" href="css/theme039.css">
    <link rel="stylesheet" type="text/css" href="css/style039.css">


</head>

<body>
    <?php include 'includes/header.php'; ?>
        <div class="form-body on-top">
            <div class="row">
                <div class=" apply col-md-6 wow fadeInRight" data-wow-delay=".2s">
                    <h1 >Apply for a insurance plan</h1>
                    <h5 >begin a new journey with a new hope..</h5>  
                </div>
                <section >

                <div class="form-holder">
                    <div class="form-content">
                        <div class="form-items">
                            <form action="applynew_includes.php" method="POST" id="apply" name="apply">
                                <div class="container wow fadeInRight" data-wow-delay=".4s">
                                    <p style="text-align: center;">Please fill the form to apply for a new insuarance plan. We response within 48 hours.</p>
                                    <div class="row">
                                        <div class="form-group">
                                            <div class="form check">
                                                <div class="container">
                                                    <div class="row row-cols-2 row-cols-lg-3">
                                                        <input class="col " class="form-check-input" type="radio" id="Mr" name="title" value="Mr">
                                                        <label for="Mr">Mr</label>
                                                        <input class="col " class="form-check-input" type="radio" id="Ms" name="title" value="Mr">
                                                        <label for="Ms">Ms</label>
                                                        <input class="col " class="form-check-input" type="radio" id="Rev" name="title" value="Mr">
                                                        <label for="Rev">Rev</label>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>

                                    <div class="row">
                                        <div class="form-group">
                                            <input type="text" class="form-control" placeholder="Full name" name="fullName" id="fullName">
                                        </div>
                                    </div>

                                    <div class="row">
                                        <div class="form-group">
                                            <input type="text" class="form-control" placeholder="NIC number" name="NICNum" id="NICNum">
                                            <!-- warning -->
                                            <div classname="hidden-md" id="hide-NIC">
                                                <div class="container">
                                                    <div id="NIC-warning" class="warning-div"></div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>

                                    <div class="row">
                                        <div class="form-group">
                                            <input type="text" class="form-control" placeholder="Age" name="age" id="age">
                                            <!-- warning -->
                                            <div classname="hidden-md" id="hide-age">
                                                <div id="age-warning" class="warning-div"></div>
                                            </div>
                                        </div>
                                    </div>

                                    <div class="row">
                                        <div class="form-group">
                                            <input type="text" class="form-control" placeholder="Address" name="address" id="address">
                                        </div>
                                    </div>

                                    <div class="row">
                                        <div class="form-group">
                                            <input type="email" class="form-control" placeholder="Email Address" name="email" id="email">
                                        </div>
                                    </div>

                                    <div class="row">
                                        <div class="form-group">
                                            <input type="text" class="form-control" placeholder="Contact Number" name="number" id="number">
                                            <!-- warning -->
                                            <div classname="hidden-md" id="hide-number">
                                                <div class="warning-div" id="number-warning"></div>
                                            </div>
                                        </div>
                                    </div>

                                    <div class="row">
                                        <div class="form-group">
                                            <select name="policy" class="form-control">
                                                <option selected="" value="Default">(Please select a policy name)</option>
                                                <option value="Medi_Plus">Medi_Plus</option>
                                                <option value="Surgical_Plan">Surgical_Plan</option>
                                                <option value="Hospital_Cash_Plan">Hospital_Cash_Plan</option>
                                                <option value="Future_Server">Future_Server</option>
                                                <option value="Saubhagya">Saubhagya</option>
                                                <option value="Medi60">Medi60</option>
                                                <option value="Car">Car</option>
                                                <option value="Motor_Cycle">Motor_Cycle</option>
                                                <option value="Three_Wheeler">Three_Wheeler</option>
                                                <option value="SUV">SUV</option>
                                            </select>
                                        </div>
                                    </div>
                                    <div class="row">
                                        <div class="row top-padding">
                                            <div class="form-button col">
                                                <input type="submit" class="ibtn less-padding" value="Apply">
                                            </div>
                                            <div class="form-button col">
                                                <input type="submit" class="ibtn less-padding" value="clear form" onclick="this.form.reset();">
                                            </div>
                                        </div>
                                        <p style="text-align: center;"><b>Thank you for selecting us !</b></p>
                                    </div>
                            </form>
                        </div>
                    </div>
                    </section>
                </div>
            </div>
            <div>
            <?php include 'includes/footer.php'; ?>
            </div>

    <script src="js/apply_validations039.js"></script>
    <script src="js/jquery.min.js"></script>
    <script src="js/jpreLoader.min.js"></script>
    <script src="js/bootstrap.min.js"></script> -->
    <script src="js/bootstrap.bundle.min.js"></script>
    <script src="js/wow.min.js"></script>
    <script src="js/jquery.isotope.min.js"></script>
    <script src="js/easing.js"></script>
    <script src="js/owl.carousel.js"></script>
    <script src="js/jquery.magnific-popup.min.js"></script>
    <script src="js/enquire.min.js"></script>
    <script src="js/jquery.stellar.min.js"></script>
    <script src="js/jquery.plugin.js"></script>
    <script src="js/typed.js"></script>
    <script src="js/designesia.js"></script>



</body>

</html>

******************************to update contact information******************
<?php
    require_once 'dbConnection039.php';
    session_start();

    $conn = Database::getConnection();

    
    $reqnic = isset($_POST["NICNum"]) ? $_POST["NICNum"] : null;
    $reqemail = isset($_POST["email"]) ? $_POST["email"] : null;
    $reqnumber = isset($_POST["number"]) ? $_POST["number"] : null;
  
  if($reqnic){
    $accountFound="SELECT *
    FROM customer_details
    WHERE NIC='$reqnic'";

$result = $conn->query($accountFound);

if ($result->num_rows == 1) {

    if( $reqemail ){
      $updateemail = "UPDATE customer_details SET email='$reqemail' WHERE NIC=$reqnic";

      if ($conn->query($updateemail) === TRUE) {
        echo "<div style='margin:20% 30%;'><h4>Record updated successfully</h4></div>";
      } else {
      echo "Error updating record: " . $conn->error;
      }
  }

 if( $reqnumber){

    $updatenumber = "UPDATE customer_details SET ContactNumber='$reqnumber' WHERE NIC=$reqnic";

      if ($conn->query($updatenumber) === TRUE) {
        echo "<div style='margin:20% 30%;'><h4>Record updated successfully</h4></div>";
      } else {
        echo "Error updating record: " . $conn->error;
      }
    }
  }
    else{
      echo "<div style='margin:20% 30%;'><h4>Invalid NIC</h4></div>";
    }
  
}
  else{
     echo "<div style='margin:20% 30%;'><h4>Values are not set</h4></div>";
}


header('Refresh:2 ; URL= account_profile039.php');

?>


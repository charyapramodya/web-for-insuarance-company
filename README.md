# web-for-insuarance-company
this is a group project to implement a web site for an insurance company 

The full project is in https://github.com/lahiruT97/GroupProject 

My contribution is in following pages

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


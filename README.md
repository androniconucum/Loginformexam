<!---
Login.php --------------------------------------------
 <?php
    session_start();
    include 'db.php';
    
    if(isset($_POST['submit'])) {
        $username = $_POST['username'];
        $password = $_POST['password'];
        
        if(empty($username) || empty($password)) {
            $error = "Username and password are required";
        } else {
            // Query to check if username exists
            $sql = "SELECT * FROM users WHERE username = ?";
            $stmt = mysqli_prepare($conn, $sql);
            mysqli_stmt_bind_param($stmt, "s", $username);
            mysqli_stmt_execute($stmt);
            $result = mysqli_stmt_get_result($stmt);
            
            if(mysqli_num_rows($result) === 1) {
                $row = mysqli_fetch_assoc($result);
                
                // Verify password
                if(password_verify($password, $row['password'])) {
                    // Set session variables
                    $_SESSION['user_id'] = $row['id'];
                    $_SESSION['username'] = $row['username'];
                    $_SESSION['logged_in'] = true;
                    
                    // Redirect to welcome page or dashboard
                    header("Location: welcome.php");
                    exit();
                } else {
                    $error = "Invalid password";
                }
            } else {
                $error = "Username not found";
            }
        }
    }
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login</title>
</head>
<body>
    <div>
        Login Form
    </div>
    
    <?php if(isset($error)) { ?>
        <div><?php echo $error; ?></div>
    <?php } ?>
    
    <?php if(isset($_SESSION['success'])) { ?>
        <div><?php echo $_SESSION['success']; unset($_SESSION['success']); ?></div>
    <?php } ?>
    
    <form method="post">
        <input type="text" name="username" id="username" placeholder="Enter username">
        <input type="password" name="password" id="password" placeholder="Enter password">
        <button type="submit" name="submit">Login</button>
    </form>
    
    <div>
        <p>Don't have an account? <a href="register.php">Register here</a></p>
    </div>
</body>
</html>




Register.php ----------------------------------------------------
  <?php
    session_start();
    include 'db.php';


    if(isset($_POST['submit'])) {
        $username = $_POST['username'];
        $password = $_POST['password'];
        $email = $_POST['email'];

        if(empty($username) || empty($password) || empty($email)) {
            $error = "All fields are required";
        }
        elseif(!preg_match("/^(?=.*[A-Z])(?=.*\d)/", $username)) {
            $error = "Username must have a capital letter and a number";
        }
        elseif(!preg_match("/^(?=.*[A-Z])(?=.*\d)(?=.*[-@_&.*\/])/", $password)){
            $error = "Password must have a capital letter, a number and a special character";
        }
        else {
            $checkUsername = mysqli_query($conn, "SELECT * FROM users WHERE username = '$username'");
            $checkEMail = mysqli_query($conn, "SELECT * FROM users WHERE email = '$email'");

            if(mysqli_num_rows($checkUsername) > 0) {
                $error = "Username already exist";
            }
            elseif(mysqli_num_rows($checkEMail) > 0) {
                $error = "Email already registered";
            }
            else {
                $hashPassword = password_hash($password, PASSWORD_DEFAULT);
                $sql = "INSERT INTO users(username, password, email) VALUES('$username', '$hashPassword', '$email')";

                if(mysqli_query($conn, $sql)) {
                    $_SESSION['success'] = "Congrats! Account Successfuly registered";
                    header("Location: register.php");
                    exit();
                }
                else {
                    $error = "Error: " . mysqli_error($conn);
                }
            }
        }
    }


?>




<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LoginForm</title>
</head>
<body>
    <div>
        Login Form
    </div>
        <?php
            if(isset($error)) {
         ?>
         <div><?php echo $error ?></div>
         <?php }?>

         <?php
            if(isset($_SESSION['success'])) {
          ?>
          <div><?php echo $_SESSION['success']; unset($_SESSION['success'])?></div>
          <?php }?>
    <form method="post">
        <input type="text" name="username" id="username" placeholder="Enter username">
        <input type="password" name="password" id="password" placeholder="Enter Password">
        <input type="email" name="email" id="email" placeholder="Enter email">
        <button type="submit" name="submit">Register</button>
    </form>
</body>
</html>






db.php --------------------------------------------------------------------
  <?php
$hostname = "localhost";
$username = "root";
$password = "december12";
$dbname = "practice";

$conn = mysqli_connect($hostname, $username, $password, $dbname);

if (!$conn) {
    die("Connection error" . mysqli_connect_error());
}


?> 
--->

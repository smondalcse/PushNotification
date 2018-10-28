# PushNotification

PHP Script

1. CREATE_DATABASE.TXT
====================

CREATE DATABASE fcm;

USE fcm; 

CREATE TABLE users (
 id int(20) NOT NULL AUTO_INCREMENT,
 Token varchar(200) NOT NULL,
 PRIMARY KEY (id),
 UNIQUE KEY (Token)
);

2. register
===============
<?php 

	if (isset($_POST["Token"])) {
		
		   $_uv_Token=$_POST["Token"];

		   $conn = mysqli_connect("localhost","root","","fcm") or die("Error connecting");

		   $q="INSERT INTO users (Token) VALUES ( '$_uv_Token') "
              ." ON DUPLICATE KEY UPDATE Token = '$_uv_Token';";
              
      mysqli_query($conn,$q) or die(mysqli_error($conn));

      mysqli_close($conn);

	}


 ?>
 
 3.push_notification
 =========================
 <?php 

	function send_notification ($tokens, $message)
	{
		$url = 'https://fcm.googleapis.com/fcm/send';
		$fields = array(
			 'registration_ids' => $tokens,
			 'data' => $message
			);

		$headers = array(
			'Authorization:key = AAAA8BKSCNA:APA91bHCSWGsXOZEn8fu81eXxzbg5ziYMv9UKXmFEK2OKIdxC2C-JdHaydeEzYS-qKiOasHDiI6w8fzteypZmZid4l3djteBuVR7nhpJVSL3Qru_XZQhbOmLYRH1SX4oDYJjQf2aF3zN',
			'Content-Type: application/json'
			);

	   $ch = curl_init();
       curl_setopt($ch, CURLOPT_URL, $url);
       curl_setopt($ch, CURLOPT_POST, true);
       curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
       curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
       curl_setopt ($ch, CURLOPT_SSL_VERIFYHOST, 0);  
       curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
       curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($fields));
       $result = curl_exec($ch);           
       if ($result === FALSE) {
           die('Curl failed: ' . curl_error($ch));
       }
       curl_close($ch);
       return $result;
	}
	

	$conn = mysqli_connect("localhost","root","","fcm");

	$sql = " Select Token From users";

	$result = mysqli_query($conn,$sql);
	$tokens = array();

	if(mysqli_num_rows($result) > 0 ){

		while ($row = mysqli_fetch_assoc($result)) {
			$tokens[] = $row["Token"];
		}
	}

	mysqli_close($conn);

	$message = array("message" => " 2. FCM PUSH NOTIFICATION TEST MESSAGE");
	$message_status = send_notification($tokens, $message);
	echo $message_status;



 ?>



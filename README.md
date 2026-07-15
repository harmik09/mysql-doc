# Flutter MySQL CRUD Complete Guide

## SQL

``` sql
CREATE DATABASE flutter_db;
USE flutter_db;
CREATE TABLE users(
 id INT AUTO_INCREMENT PRIMARY KEY,
 name VARCHAR(100),
 email VARCHAR(100)
);
```

## db.php

``` php
<?php
header("Content-Type: application/json");
$conn=new mysqli("localhost","root","","flutter_db");
if($conn->connect_error){die(json_encode(["error"=>$conn->connect_error]));}
?>
```

## get_users.php

``` php
<?php
include 'db.php';
$res=$conn->query("SELECT * FROM users");
$data=[];
while($r=$res->fetch_assoc()){$data[]=$r;}
echo json_encode($data);
?>
```

## add_user.php

``` php
<?php
include 'db.php';
$name=$_POST['name']; $email=$_POST['email'];
$stmt=$conn->prepare("INSERT INTO users(name,email) VALUES(?,?)");
$stmt->bind_param("ss",$name,$email);
echo json_encode(["success"=>$stmt->execute()]);
?>
```

## update_user.php

``` php
<?php
include 'db.php';
$id=$_POST['id']; $name=$_POST['name']; $email=$_POST['email'];
$stmt=$conn->prepare("UPDATE users SET name=?,email=? WHERE id=?");
$stmt->bind_param("ssi",$name,$email,$id);
echo json_encode(["success"=>$stmt->execute()]);
?>
```

## delete_user.php

``` php
<?php
include 'db.php';
$id=$_POST['id'];
$stmt=$conn->prepare("DELETE FROM users WHERE id=?");
$stmt->bind_param("i",$id);
echo json_encode(["success"=>$stmt->execute()]);
?>
```

## Flutter ApiService

``` dart
import 'dart:convert';
import 'package:http/http.dart' as http;

class ApiService{
 static const baseUrl="http://YOUR_IP/flutter_api/";

 Future<List<dynamic>> getUsers() async{
  final r=await http.get(Uri.parse(baseUrl+"get_users.php"));
  return jsonDecode(r.body);
 }

 Future<void> addUser(String name,String email) async{
  await http.post(Uri.parse(baseUrl+"add_user.php"),
   body:{"name":name,"email":email});
 }

 Future<void> updateUser(String id,String name,String email) async{
  await http.post(Uri.parse(baseUrl+"update_user.php"),
   body:{"id":id,"name":name,"email":email});
 }

 Future<void> deleteUser(String id) async{
  await http.post(Uri.parse(baseUrl+"delete_user.php"),
   body:{"id":id});
 }
}
```

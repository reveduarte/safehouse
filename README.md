## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/reveduarte/safehouse/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/reveduarte/safehouse/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.


<html>
<head>
<title>Safehouse_users </title>
<style>
#users {
    font-family: "Trebuchet MS", Arial, Helvetica, sans-serif;
    border-collapse: collapse;
    width: 100%;
}

#users td, #users th {
    border: 1px solid #ddd;
    padding: 8px;
}

#users tr:nth-child(even){background-color: #f2f2f2;}

#users tr:hover {background-color: #ddd;}

#users th {
    padding-top: 12px;
    padding-bottom: 12px;
    text-align: left;
    background-color: #4CAF50;
    color: white;
}
</style>
</head>

<center> <h1 id="users"> Safehouse Users </h1> </center>
<br>
<br>
<?php
error_reporting(0);
include("./vendor/autoload.php");

use Zendesk\API\HttpClient as ZendeskAPI;

/**
 * Connecting to db safehouse
 */
 
$conn = new mysqli('localhost', 'root', '', 'safehouse')
or die ('Cannot connect to db');
 
 
 $subdomain = "safehouse";
 $username  = "rev.eduarte@gmail.com";
 $token     = "PQzsmqGG3Djuy5wt3Vy6m7hciYTXXXpGRk9KVzZj";

 $client = new ZendeskAPI($subdomain);
 $client->setAuth('basic', ['username' => $username, 'token' => $token]);

try {
    $query = $client->users()->findAll();
    foreach ($query->users as $UserData) {
        echo "<pre>";
		//print_r($UserData);
	   $json_data = json_encode((array) $UserData);
		//print_r($json_data);

		$item = json_decode($json_data);
		$id = $item->id; 
		$name= $item->name; 
		$email=$item->email;
		$role= $item->role; 
		$active= $item->active; 
		
		$sql = "INSERT INTO `safehouse_users`(`id`, `name`, `email`, `role`, `active`)
			VALUES
             ('$id','$name','$email','$role','$active')";
		$result = $conn->query($sql);
					
        echo "</pre>";
    
	}
	
} catch (\Zendesk\API\Exceptions\ApiResponseException $e) {
    echo 'Please check your credentials. Make sure to change the $subdomain, $username, and $token variables in this file.';
}


//get from database
?>
<table id="users">
<tr>
	<th>ID</th>
	<th>Name</th>
	<th>Email</th>
	<th>Role</th>
	<th>Status</th>
<tr>

<?php
$show = $conn->query("select * from safehouse_users");

if ($show->num_rows > 0) {
				// output data of each row
				while($row = $show->fetch_assoc()) {
					if($row["active"]==1)
					{
						$status="Active";						
					}		
					else
					{
						$status="Inactive";						
					}
					
					echo "
					<tr>
						<td>".$row["id"]."</td>
						<td>".$row["name"]."</td>
						<td>".$row["email"]."</td>
						<td>".$row["role"]."</td>
						<td>".$status."</td>
					</tr>
					" ;					
				}
			} else {
				echo "0 results";
			}
			$conn->close();		
?>

</table>
</html>


   
   
   



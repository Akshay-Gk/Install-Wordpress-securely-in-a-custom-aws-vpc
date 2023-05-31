# **_Install Wordpress Securely in a custom aws vpc_**

# Description:

Database servers are the most important servers in any small or a big company because it contains all the data sets that can be of any user or employee of that company. The best practices of server security are therefore focused on protecting digital assets from cybercriminals. This approach is necessary because numerous hackers exploit existing vulnerabilities for financial gain. Here we set up a custom made secure setup for installing wordpress and protecting our server.

# Diagram:

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/77544d7c-f383-4347-a5b8-02b5e24c8616)


# Step 1: Create a VPC

> `Note: My region is Ohio`
 
a) **_Login to aws console > VPC >  In the VPC Dashboard, choose Create VPC_**

  - Add a name to vpc. Here i'm using **"my-vpc"**
  - Provide an IPv4 CIDR block. Here i'm using **"172.20.0.0/16"**

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/7ab35d37-f3de-4fb2-8744-db7faee26f1f)

b) **_We need to enable "DNS hostname" for the vpc so that, when an instances is launched inside VPC it will receive public DNS hostname that corresponds to the public IP address_**

- Right click on the VPC ID created and choose *vpc settings*
- Enable DNS hostnames

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/119a5e87-adfe-4455-982b-ee0fafd88e0b)

# Step 2: Create an internet gateway(igw)

- Add name. I'm using **"my-vpc-igw"**

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/79d7b2ce-1bbf-4d59-9623-b9c578e624d6)

# Step 3: Attach igw to vpc

* Click on the above "Attach to a VPC" option and select **"my-vpc"**

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/a94c5ef1-ad61-4318-a23e-ca897f386843)

# Step 4: Create Subnets

- Here i'm creating 3 subnets ,Two public and one private.
- Select the VPC id
- Add subnet name. Here i'm using name **"my-public-1"**
- Choose the AZ. Here i'm choosing **"us-east-2a"**
- Provide IPv4 CIDR block for the subnet. Here i'm using **"172.20.0.0/18"**

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/ba9dfd9a-3120-449f-a48d-1cdfe7ef044e)

- Second subnet name is **"my-public-2"**
- ip address **"172.20.64.0/18"**
- AZ is **"us-east-2b"**

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/98c3834d-6e91-41f4-9b2b-6a279b855fb7)

- Third  subnet name is **"my-private-1"**
- ip address **"172.20.128.0/18"**
- AZ is **"us-east-2c"**

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/dfb2b8dc-0578-4e42-92d0-ad7641bdb62a)

# Step 5: Enable public ipv4 

*  **_Right click on subnet ID and choose Edit "subnet settings"_**
*  Enable public ipv4 for **"my-public-1 & my-public-2"** subnets

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/01e61fa1-f55f-4d7f-8ed6-eaf4c9a1e42c)

# Step 6: Create NAT Gateway

- Add a name. Here i'm giving **"my-nat"**
- Select any of the public subnet
- Choose **"public"** on connectivity type
- Click on **"Allocate Elastic IP"** option and allocate a public ip fot the NAT gateway

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/b400dd55-9806-4086-8630-f40c0162f35d)

> `Note: Nat Gateway should be created on a public subnet and assign  elastic ip. We use this for the private subnet to access internet from inside`

# Step 7: Edit routing table


- **_Here we edit default routing table of **"my-vpc"**_**

* Select the default route and in "Action" option choose "Edit routes"

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/c445d47e-b0e8-4d92-a27f-157d91a0443a)

- **_Add a new route_**
- Choose Destination as "0.0.0.0/0" and target as "nternet gateway(my-vpc-igw)"

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/a05dd268-6f6b-4913-be6b-19a81dfb3093)

- Name default route as **"Public-route"**

# Step 8: Create a new Route table and Edit route

- Name it as **""private-route"**
- In VPC choose **"my-vpc"**

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/0922ad61-e94f-4339-8ade-47ae05d04d06)


- Choose Destination as "0.0.0.0/0" and target as "nat gateway(my-nat)"

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/f38cb333-83a0-41ce-b40b-312888802d7c)

> `Note: Do not edit or delete the private ip route you can see in the route table as it is for internal communication`

# Step 9: Subnet association

- **_Associate private subnet to private route_**
* Right click on private-route and choose "Edit subnet associations"

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/bb4c450f-5253-4ff4-867c-890c76a142ae)

- Select "my-private-1" subnet and save changes

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/01db47c6-d001-49d6-ac64-997f34f9d55d)

> `Note: By default all subnet will be associated with default route`

# Step 10: Create security group.


* **_- Here we are creating 3 instances with name frontend,bastion and backend so we need 3 security group(sg). F

> `Note: A bastion host is a special-purpose computer on a network specifically designed and configured to withstand attacks`

a) **_Security group for bastion server_**

- Add name. Here i'm giving "my-bastion"
- Change vpc to "my-vpc"

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/f2392c6a-4238-402a-a090-3fe71dca27b5)

- **Add inbound rules**
- Type will be "SSH" . Source i'm giving my ip
- In out bound rules "All traffic" is allowed

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/0c566f5d-046b-4cf6-88a4-d951e27c2dc6)


> `Note: Here in inbound i'm giving ip as "my ip" as i'm setting SSH access to the server only for me. You can aslo allow "any ipv4" as per your requirement. Remember when we choose "my ip" our ip can change and once our ip is chaged we should edit this security group ans set our new ip to get access`

b) **_Security group for frontend server_**

- Add name. Here i'm giving "my-frontend"
- Change vpc to "my-vpc"

- Add inbound rules

- Here we add three rules in inbound
1. ssh from "bastion security group id"
2. http from "any ipv4"
3. https from "any ipv4"
- In out bound rules "All traffic" is allowed

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/fa2fd2f8-2862-4dd3-bc39-5c3a4f20506b)


c) **_Security group for backend server_**

- Add name. Here i'm giving "my-backend"
- Change vpc to "my-vpc"

- Add inbound rules

- Here we add two rules in inbound
1. ssh from "bastion security group id"
2. Allow "3306(mysql/aurora)" from "my-frontend id"
- In out bound rules "All traffic" is allowed

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/3e84641e-8899-4221-9637-f21898a3af7d)

# Step 11: Create ec2 instance


- **_Here we are creating 3 instances with name bastion,frontend and backend_**

a) **bastion instance**

- Add name. Here i'm giving "my-bastion-server"
- Select "my-vpc"
- Select one of the public subnet
- Select "existing security group" and add "my-bastion" security group

> `Note: For instances in public subnet you can find "Auto-assign public ip" is in "enable". This happens as we enable "Auto-assign public ipv4 address" for public subnets. For private subnet it will be "disable" as we haven't enabled "Auto-assign public ipv4 address" for private subnets`

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/dfa7c115-2355-4a66-86a5-36a394864600)

b) **frontend instance**

- Add name .Here i'm giving "my-frontend-server"
- Select "my-vpc"
- select one of the public subnet
- Seect "existing security group" and add "my-frontend" security group

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/ffa2a98a-da1f-4ea0-8e4f-9c7b34ce4383)

c) **backend instance**

- Add name .Here i'm giving my-backend-server
- Select "my-vpc"
- select one of the private subnet
- Select "existing security group" and add "my-backend" security group

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/264f6a77-3cab-4ed7-a759-29e37b95ec3c)


> `Note: We cannot directly ssh to "frontend" or "backend" server.We can only access "frontend" and "backend" through "bastion" server'

# Step 12: SSH to server

* **_Now ssh to each server and try ping. You should be able to ping from all three servers. In case you are unable ping check the security group and route table_**

  ```ping -c 3 8.8.8.8```
  
# Step 13: Install database server in backend
  
* Here i'm using "mariadb"


   ```sudo yum install mariadb105-server -y
   sudo systemctl restart mariadb.service
   sudo systemctl enable mariadb.service```
      
     
* **_install "mysql"_**
- set passwrod for root

```sudo mysql_secure_installation```

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] n <<=========================
 ... skipping.

You already have your root account protected, so you can safely answer 'n'.

Change the root password? [Y/n] y
New password: mysqlroot123
Re-enter new password: mysqlroot123
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure

  * **_Now login as root user using the new password created_**
  
  ```mysql -u root -pmysqlroot123```
  
  * _create a database_
  * _Create a user_
  * _Grant all privileges to new user on database_
  * _Here i'm giving database name as "blogdb" and username as "wpuser"_
  
     ```
     create user wpuser@'%' identified by 'wpuser123';
     grant all privileges on blogdb.* to wpuser@'%';
     flush privileges; ```
     
  * _Login as "wpuser" and check database_
     
     ```mysql -u wpuser -pwpuser123
        show databases;```
 
 # Step 14: Install httpd,php and php-mysqlnd
        
 * _Install httpd,php and php-mysqlnd in frontend server_
 
 ```
    yum install httpd php php-mysqlnd
    systemctl restart php-fpm.service httpd.service
    systemctl enable php-fpm.service httpd.service
 ```
 
# Step 15: Install wordpress

* _Install wordpress in frontend server_

```
wget https://wordpress.org/wordpress-6.0.tar.gz
   tar -xvf wordpress-6.0.tar.gz
   ```
   
# Step 16: Copy wordpress extracted files to /var/www/html
   
   
   ```sudo cp -r wordpress/*  /var/www/html/```
   
# Step 17: Editing /var/www/html/wp-config.php file
   

* _Intially we rename "wp-config-sample.php" to "wp-config.php"_

```wp-config-sample.php wp-config.ph```

- _Edit wp-config.php file_

```vi wp-config.php```

- _Provide below details_
- _Database name, username,userpassword and host ip_

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/ec3751e8-b123-402d-a58d-660ac97506c2)

## Installation completed

* **_Now once you call the frontend server "public dns" name you should be able to access wordpress_**

> `Note: We haven't enabled https here so we need to call dns name with http://`

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/3247cfd5-4572-4475-b35a-fed462553e9c)


# Conclusion:

Here we created a set up for installing wordpress. The setup is highly secure and it prevents direct access to the servers.We keep the backend server protected from outside world.

     
     
   


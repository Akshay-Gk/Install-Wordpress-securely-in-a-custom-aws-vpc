# **_Install Wordpress Securely in a custom aws vpc_**

## Description:

Here we set up a custom made secure setup for installing wordpress.

### Steps:

1. Create a VPC
  - My region is Ohio
 
a) Login to aws console > VPC >  Create VPC
  - Add a name to vpc. Here im givibg my-vpc
  - provide an ip address. Here im using 172.20.0.0/16

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/7ab35d37-f3de-4fb2-8744-db7faee26f1f)

b) We need to allow DNS hostname so thatvinstances launched in the VPC receive public DNS hostnames that correspond to their public IP addresses.
- Right click on the VPC ID created and choose *vpc settings*
- Enable DNS hostnames

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/119a5e87-adfe-4455-982b-ee0fafd88e0b)

2. Create an internet gateway (igw)

-Add name. im giving my-vpc-igw

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/79d7b2ce-1bbf-4d59-9623-b9c578e624d6)

- Attach igw to vpc (my-vpc)

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/a94c5ef1-ad61-4318-a23e-ca897f386843)

3. Create Subnets

- Here im creating 3 subnets ,Two public and one private.
- Give your VPC id
- Subnet name. Here im giving my-public-1
- Choose the AZ. Here i chose us-east-2a
- Provide the ip address for the subnet. Here im giving 172.20.0.0/18

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/ba9dfd9a-3120-449f-a48d-1cdfe7ef044e)

- second subnet name is my-public-2
- ip address 172.20.64.0/18
- AZ is us-east-2b

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/98c3834d-6e91-41f4-9b2b-6a279b855fb7)

- Third  subnet name is my-private-1
- ip address 172.20.128.0/18
- AZ is us-east-2c

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/dfb2b8dc-0578-4e42-92d0-ad7641bdb62a)

4.  Enable public ipv4 for my-public-1 & my-public-2 subnets.

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/01e61fa1-f55f-4d7f-8ed6-eaf4c9a1e42c)







  
  
   


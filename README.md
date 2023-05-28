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

5. Create a NAT Gateway.

- Add a name. Here im giving my-nat
- Select any of the public subnet.
- choose public on connectivity type
- Allocate Elastic ip for nat

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/b400dd55-9806-4086-8630-f40c0162f35d)

> `Note: Nat Gateway should be created in one of the public subnet and assign an elastic ip. we use this for the private subnet to access internet from inside`

6. Edit routing table

- Here we edit default routing table of my-vpc
![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/c445d47e-b0e8-4d92-a27f-157d91a0443a)

- Add a new route to 0.0.0.0/0 and give target as internet gateway(my-vpc-igw)

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/a05dd268-6f6b-4913-be6b-19a81dfb3093)

- Name default route as Public-route

7. Create a new Route table and edit route

- Name it as private-route
- Choose my-vpc 
- Edit and add a new route to 0.0.0.0/0 and give target as nat gateway(my-nat)

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/f38cb333-83a0-41ce-b40b-312888802d7c)

> `Note: Do not edit or delete the private ip route you can see in the route table as it is for internal communication`

8. Subnet association

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/bb4c450f-5253-4ff4-867c-890c76a142ae)

- Associate private subnet to private route.
- select private subnet(my-private-1) and save changes

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/01db47c6-d001-49d6-ac64-997f34f9d55d)

> `Note: By default all subnet will be associated with default route`

9. Create security group.

- Here we are creating 3 instances with name frontend,bastion and backend so we need 3 security group(sg)
- For all 3 intstance we create different security rules to tighten security.

> `Note: A bastion host is a special-purpose computer on a network specifically designed and configured to withstand attacks`

a) Security group for bastion server

- Add name. Here im giving my-bastion
- change vpc to my-vpc

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/f2392c6a-4238-402a-a090-3fe71dca27b5)

- Add inbound rules
- Type will be ssh and ip here im giving as my ip 
- In out bound rules all traffic is allowed.

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/0c566f5d-046b-4cf6-88a4-d951e27c2dc6)


> `Note: Here in inbound i gave ip as my ip since im setting the server like i will be only onlu one who will be able to ssh. you can aslo allow any ipv4 as per your requirement. Remember that our ip can change and once our ip is chaged we should edit this security group ans set our new ip to get access`

b) Security group for frontend server

- Add name. Here im giving my-frontend
- change vpc to my-vpc
- Add inbound rules
- Here we add three rules in inbound
1. ssh from bastion security group id
2. http from any ipv4
3. https from any ipv4
- In out bound rules all traffic is allowed

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/fa2fd2f8-2862-4dd3-bc39-5c3a4f20506b)


c) Security group for backend server

- Add name. Here im giving my-backend
- change vpc to my-vpc
- Add inbound rules
- Here we add two rules in inbound
1. ssh from bastion security group id
2. Allow 3306(mysql/aurora) from my-frontend id
- In out bound rules all traffic is allowed.

![image](https://github.com/Akshay-Gk/Install-Wordpress-securely-in-a-custom-aws-vpc/assets/112197849/3e84641e-8899-4221-9637-f21898a3af7d)





  
  
   


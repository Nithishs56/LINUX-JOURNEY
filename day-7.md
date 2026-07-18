Today i am completed my first week of linux jounery and i complete my capstone project which is improved my practial skills

The project is about Deploying and managing a website using nginx

First i am have installed the nginx using sudo apt install nginx | sudo apt update nginx

and then edited the deafult page with my actual page i have changed the permissions for that file

after i created the backup file and i copied the content which is on nginx.conf

and then i used this command to list the file and store into my capestone folder ls -l /etc/nginx/ > list.txt

after i have checked for the errors by using cat /var/log/nginx/error.log | grep error

or we can use the this for counting error grep -i "error" /var/log/nginx/error.log | wc -l

challengers i faced during this project i was do everything correct but i my localhost the site is not running i was checking what i have done wrong but suddenly i released that i am using ec2 instance even i am using linux ubuntu my local ip address is different form the ec2 public ip adderess and then i used this http://my ec2 public ip address
boom the server is connected to me!!

this is about today learning

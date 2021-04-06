![image](https://user-images.githubusercontent.com/67410011/110039061-adbe5400-7d66-11eb-9c6f-f8fe64c70aa7.png)

First build a model using sklearn and linear regression using banglore home prices dataset from kaggle.com. Second step was to write a python flask server that uses the saved model to serve http requests. Third component is the website built in html, css and javascript that allows user to enter home square ft area, bedrooms etc and it will call python flask server to retrieve the predicted price. During model building I used almost all data science concepts such as data load and cleaning, outlier detection and removal, feature engineering, dimensionality reduction, gridsearchcv for hyperparameter tunning, k fold cross validation etc. Technology and tools wise this project covers,

1. Python
2. Numpy and Pandas for data cleaning
3. Matplotlib for data visualization
4. Sklearn for model building
5. Jupyter notebook, visual studio code and pycharm as IDE
6. Python flask for http server
7. HTML/CSS/Javascript for UI
8. AWS EC2 deployment

# Deploying app to cloud (AWS EC2)

1. Create EC2 instance using amazon console, also in security group add a rule to allow HTTP incoming traffic
2. Now connect to your instance using a command like this,
```
ssh -i "C:\Users\naman solanki\.ssh\bhp.pem" ubuntu@ec2-18-217-232-101.us-east-2.compute.amazonaws.com
```
3. nginx setup
   1. Installing nginx on EC2 instance using these commands,
   ```
   sudo apt-get update
   sudo apt-get install nginx
   ```
   2. Above will install nginx as well as run it. To check status of nginx
   ```
   sudo service nginx status
   ```
   3. Here are the commands to start/stop/restart nginx
   ```
   sudo service nginx start
   sudo service nginx stop
   sudo service nginx restart
   ```
   4. Now when we load cloud url in browser we will see a message saying "welcome to nginx" This means your nginx is setup and running.
4. Now we need to copy all your code to EC2 instance. You can do this either using git or copy files using winscp. I used winscp. 
5. Once you connect to EC2 instance from winscp (instruction in a youtube video), you can now copy all code files into /home/ubuntu/ folder. The full path of your root folder is now: **/home/ubuntu/Project1_realEst**
6.  After copying code on EC2 server now we can point nginx to load our property website by default. For below steps,
    1. Creating this file /etc/nginx/sites-available/bhp.conf. The file content looks like this,
    ```
    server {
	    listen 80;
            server_name bhp;
            root /home/ubuntu/Project1_realEst/client;
            index app.html;
            location /api/ {
                 rewrite ^/api(.*) $1 break;
                 proxy_pass http://127.0.0.1:5000;
            }
    }
    ```
    2. Create symlink for this file in /etc/nginx/sites-enabled by running this command,
    ```
    sudo ln -v -s /etc/nginx/sites-available/bhp.conf
    ```
    3. Remove symlink for default file in /etc/nginx/sites-enabled directory,
    ```
    sudo unlink default
    ```
    4. Restart nginx,
    ```
    sudo service nginx restart
    ```
7. Now install python packages and start flask server
```
sudo apt-get install python3-pip
sudo pip3 install -r /home/ubuntu/Project1_realEst/server/requirements.txt
python3 /home/ubuntu/Project1_realEst/client/server.py
```
Running last command above will prompt that server is running on port 5000.
8. Now just load your cloud url in browser (for me it was http://ec2-18-217-232-101.us-east-2.compute.amazonaws.com/) and this will be fully functional website running in production cloud environment



For phones, this will look something like this:
![image](https://user-images.githubusercontent.com/67410011/110040166-59b46f00-7d68-11eb-8fdb-e2c80ce476ef.png)

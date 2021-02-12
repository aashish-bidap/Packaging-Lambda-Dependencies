## Creating Python Dependencies Package for AWS Lambda :

Below is a walk through for packaging the Python Depencdencies for AWS Lambda on Ubuntu Server.

### Setting up the EC2 Instance-

- Start an ec2 instance with an Ubuntu AMI (free tier).
![AMI](https://github.com/aashish-bidap/Packaging-Lambda-Dependencies/blob/main/ami.png)
- Securely store the key pair to a directory in order to login via SSH client thorugh terminal.

- Connecting to the EC2 instance using the keypair file

~~~
  ssh -i <path-to-pem-file>/xyz.pem ubuntu@<ip>.compute-1.amazonaws.com
~~~

### Setting Python Environemnt on new instance:

- Updating the package lists to upgrade the packages that need upgrading.
~~~
	sudo apt-get update
~~~
- This Ubuntu AMI 18.04 comes with python 3.6 pre-installed we would be creating a new python instance/upgrading to python 3.7.
~~~
sudo apt-get install python3.7 -y
~~~
- Checking the python versions:
~~~
  ls /usr/bin/python*
~~~
- Updating the symbolic links to make 3.7 as the default python environment.
~~~
  sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 1
  sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.7 2
~~~
- Checking the default version.
~~~
  python3 -V
~~~
- Creating a directory structure
~~~
  mkdir -p build/python/lib/python3.7/site-packages
~~~


### Setting pip3 on the new instance before installing the required libraries:

- Installing pip3
~~~
  sudo apt install python3-pip
~~~
- Running pip3 to check the installation
~~~
  pip3
~~~
	
### Installing pandas and requests libraries

- Add the required the libraries to be installed in the command or create a requirements.txt file for bulk installation.
~~~
  pip3 install pandas requests -t build/python/lib/python3.7/site-packages/ --system
~~~

### Installing zip for zipping the dependencies

- Traversing to the site-packages folder
~~~
  cd build/python/lib/python3.7/site-packages/
~~~
- Checking if the zip package is installed
~~~
  zip
~~~
- If not installed installing as below
~~~
  sudo apt-get install zip
~~~

### Zipping the dependencies :
~~~
cd build/
zip -r pandas_requests.zip .
~~~

### Exporting the zip file to local with scp
- Exit the connection from the instance
- Export the zip file to local system.
~~~
scp -i <path-to-pem-file>/xyz.pem ubuntu@<ip>.compute-1.amazonaws.com:/home/ubuntu/build/pandas_requests.zip .
~~~

This zip file can now be loaded to Amazon S3 or AWS Lambda Layer in order to statisfy the required dependencies of the lambda functuion. 

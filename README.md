## Udacity FSWD - Linux Server Config project
This repository will only contain this readme file with information on the following:
1. Address of the server
2. Access information
3. What I have installed
4. What I have configured

#### Server address information
The server for "Tommys Pedal Show" is hosted on Amazon Webservices using Amaxon Lightsail.

- Public IP: 52.210.252.106
- URL: http://52.210.252.106.xip.io/
- SSH port: 2200

#### Access Information
To access the server using SSH, use the public IP and port 2200. The username is "grader" and access is only allowed by 
using SSH keys. The public key for is added to the "authorized_keys" file on the server under /home/grader/.ssh
The private key will be provided as instructed from Udacity.

From a command line you can run the following command:

````` ssh grader@52.210.252.106 -i <private key file location> -p 2200 `````

A passphrase is required with this key-based authentication and this will be provided along with the private key.

To access the Application simply open the URL in your favourite browser. I have not made this web site mobile friendly
as that is not the key to this project. If this was a real project for a customer I would of course scaled this for all
viewports.

#### What is installed
- Apache ``` sudo apt-get install apache2 ```
- mod_wsgi ``` sudo apt-get install libapache2-mod-wsgi ```
- postgreSQL ``` sudo apt-get install postgresql ```
- python pip ``` sudo apt-get install python-pip ```
- Flask ``` sudo pip install Flask ```
- SQLAlchemy ``` sudo pip install SQLAlchemy ```
- OAuth2Client ``` sudo pip install oauth2client ```
- requests  ``` sudo pip install requests ```
- psycopg2 ``` sudo pip install psycopg2 ```

All packages have been upgraded to latest versions using ``` sudo apt-get upgrade ```

#### Server configuration
##### Configure the firewall (UFW)
- Deny all incoming traffic ```sudo ufw default deny incoming ```
- Allow all outgoing traffic ``` sudo ufw default allow outgoing ```
- Allow incoming SSH on custom port ``` sudo ufw allow 2200/tcp ```
- Allow incoming Web traffic ``` sudo ufw allow www ```
- Allow Ntp ``` sudo ufw allow 123 ```
- Enable the FW ``` sudo ufw enable ```

##### Apache configuration
Copy the default config to use with our new site  
``` cd /etc/apache2/sites-available```  
``` sudo cp 000-default.conf catalog.conf ```  

The catalog.conf file was edited to serve the application out of the /srv/catalog/ directory as a wsgi application.

Once that was completed the default site was disabled  
``` sudo a2dissite 000-default.conf ```  
and the new was enabled  
``` sudo a2ensite catalog.conf ```

##### User configuration
- The "grader" user account was created and added to the sudoers file under /etc/sudoers.d/grader  
- The graders public key was added to the authorized_keys file under the users home directory /home/grader/.ssh
- Changed owner of files to be served to the www-data user/group, the user configured to be used in the Apache conf 
file catalog.conf

##### PostgreSQL configuration
- open the DB console using ``` sudo -u postgres psql ```
- Set the postgres user password using ``` ALTER USER postgres PASSWORD '<password>'; ```
- Created the database to use ``` CREATE DATABASE catalog; ```
- Create a user "websrv" to use with the application ``` CREATE USER websrv WITH PASSWORD '<password>'; ```
- Granted FULL access to the catalog database ``` GRANT ALL PRIVILEGES ON DATABASE catalog TO websrv ```
- Added websrv & password the connection strings in the python files and ran the /srv/catalog/lotsofcategories.py 
file to create the tables (with websrv as owner)and add some initial data.
- Revoked ALL websrv privileges from the database and only added SELECT, INSERT, UPDATE and DELETE on the three tables 
in the catagory database.
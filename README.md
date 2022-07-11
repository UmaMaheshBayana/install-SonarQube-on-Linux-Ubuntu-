# install-SonarQube-on-Linux-Ubuntu

# Step 1:

```sudo sysctl -w vm.max_map_count=262144```

```sudo sysctl -w fs.file-max=65536```

```ulimit -n 65536``` 

```ulimit -u 4096```

Increase the vm.max_map_count kernel ,file descriptor and ulimit permanently . Open the below config file 

```sudo nano /etc/security/limits.conf```

Copy and paste the below value at last line, 

```
   sonarqube   -   nofile   65536

   sonarqube   -   nproc    4096
```

Before installing, Lets update and upgrade System Packages

```sudo apt-get update```

```sudo apt-get upgrade```

Install wget and unzip package

```sudo apt-get install wget unzip -y```

Install OpenJDK and JRE 11 using following command,

```sudo apt-get install openjdk-11-jdk -y```

```sudo apt-get install openjdk-11-jre -y```

Check Java version

```Java -version```

# Step 2: Install and Setup PostgreSQL 10 Database For SonarQube Add and download the PostgreSQL Repo

```sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'```

```wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -```

Install the PostgreSQL database Server by using following command,

```sudo apt-get -y install postgresql postgresql-contrib```

Start PostgreSQL Database server

```sudo systemctl start postgresql```

Enable it to start automatically at boot time.

```sudo systemctl enable postgresql```

Change the password for the default PostgreSQL user.

```sudo passwd postgres```

Switch to the postgres user.

```su - postgres```

Create a new user by typing:

```createuser sonar```

Switch to the PostgreSQL shell.

```psql```

Set a password for the newly created user for SonarQube database.

```ALTER USER sonar WITH ENCRYPTED password 'sonar';```

Create a new database for PostgreSQL database by running:

```CREATE DATABASE sonarqube OWNER sonar;```

grant all privileges to sonar user on sonarqube Database.

```grant all privileges on DATABASE sonarqube to sonar;```

Exit from the psql shell:

```\q```

Switch back to the sudo user by running the exit command.

```exit```

# Step 3: How to Install SonarQube on Ubuntu 20.04 LTS

Download sonaqube installer files archieve To download latest version of visit SonarQube

```sudo https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.1.44547.zip```

Output:

```
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.1.zip

https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.1.zip```

Resolving binaries.sonarsource.com (binaries.sonarsource.com)... 91.134.125.245

→Connecting to binaries.sonarsource.com (binaries.sonarsource.com)|91.134.125.245|:443... connected.

HTTP request sent, awaiting response... 200 OK

Length: 209531101 (200M) [application/zip]

Saving to: ‘sonarqube-8.9.1.zip’

sonarqube-8.9.1.zip       	         100%[==========================================================================>] 199.82M  1.31MB/s	in 34s

 ‘sonarqube-8.9.1.zip’ saved [209531101/209531101] 
 ```
 
Unzip the archive setup to /opt directory

```sudo unzip sonarqube-8.9.1.zip -d /opt```

Move extracted setup to /opt/sonarqube directory

```sudo mv /opt/sonarqube-8.9.1 /opt/sonarqube```

# Step 4: Configure SonarQube

We can’t run Sonarqube as a root user , if you run using root user it stops automatically. We have found a solution to this to create a separate group and user to run sonarqube.
1. Create Group and User:

Create a group as sonar

```sudo groupadd sonar```

Now add the user with directory access

```sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar```

```sudo chown sonar:sonar /opt/sonarqube -R```

Open the SonarQube configuration file using your favorite text editor.

```sudo nano /opt/sonarqube/conf/sonar.properties```

Uncomment and Type the PostgreSQL Database username and password which we have created in above steps and add the postgres connection string.

```
#--------------------------------------------------------------------------------------------------
# DATABASE
#
# IMPORTANT:
# - The embedded H2 database is used by default. It is recommended for tests but not for
#   production use. Supported databases are Oracle, PostgreSQL and Microsoft SQLServer.
# - Changes to database connection URL (sonar.jdbc.url) can affect SonarSource licensed products.
# User credentials.
# Permissions to create tables, indices and triggers must be granted to JDBC user.
# The schema must be created first.

sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube

```

Edit the sonar script file and set RUN_AS_USER

```sudo nano /opt/sonarqube/bin/linux-x86-64/sonar.sh```

```
# If specified, the Wrapper will be run as the specified user.
# IMPORTANT - Make sure that the user has the required privileges to write
#  the PID file and wrapper.log files.  Failure to be able to write the log
#  file will cause the Wrapper to exit without any way to write out an error
#  message.
# NOTE - This will set the user which is used to run the Wrapper as well as
#  the JVM and is not useful in situations where a privileged resource or
#  port needs to be allocated prior to the user being changed.

 RUN_AS_USER=sonar

```
Type CTRL+X to save and close the file.

Start SonarQube:

Now to start SonarQube we need to do following: Switch to sonar user

```sudo su sonar```

Move to the script directory

```cd /opt/sonarqube/bin/linux-x86-64/```

Run the script to start SonarQube

```./sonar.sh start```

Output:

Starting SonarQube...

Started SonarQube


We can also add this in service and can run as a service.

Check SonarQube Running Status:

To check if sonaqube is running enter below command,

```./sonar.sh status```

Output:

sonar@fosstechnix:~/bin/linux-x86-64$ ./sonar.sh status

SonarQube is running (9490).

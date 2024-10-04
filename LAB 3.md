## Configuring Jenkins server and Installing Tomcat onto Jenkin's Server for Deploying our Application.

### Task 1: Configure Jenkins Server

Initially, Copy the **private key** from **Jump Server** to the **Jenkins Server** & **Docker Server**. so, that we can SSH from **Jenkins Server** to **Docker Server** and viseversa.
```
cd ~
```
```
ansible jenkins-server -m copy -a "src=/home/ubuntu/.ssh/id_rsa dest=/home/ubuntu/.ssh/id_rsa" -b
```
```
ansible jenkins-server -m copy -a "src=/home/ubuntu/.ssh/id_rsa.pub dest=/home/ubuntu/.ssh/id_rsa.pub" -b
```
  
   <details>
     <summary>Here's a breakdown of the command:</summary>
     
   - `ansible`: The Ansible command-line tool.
   - `jenkins-server`: The target machine specified in your inventory file.
   - `-m copy`: Specifies the Ansible module to use, in this case, the `copy` module.
   - `-a "src=/home/ubuntu/.ssh/id_rsa dest=/home/ubuntu/.ssh/id_rsa"`: Specifies the arguments for the module, indicating the source and destination paths for the file copy.
   - `-b`: Run the Ansible command with elevated privileges.
   
   </details>

```
ansible docker-server -m copy -a "src=/home/ubuntu/.ssh/id_rsa dest=/home/ubuntu/.ssh/id_rsa" -b
```

   <details>
     <summary>Here's a breakdown of the command:</summary>
     
   - `ansible`: The Ansible command-line tool.
   - `docker-server`: The target machine specified in your inventory file.
   - `-m copy`: Specifies the Ansible module to use, in this case, the `copy` module.
   - `-a "src=/home/ubuntu/.ssh/id_rsa dest=/home/ubuntu/.ssh/id_rsa"`: Specifies the arguments for the module, indicating the source and destination paths for the file copy.
   - `-b`: Run the Ansible command with elevated privileges.
   
   </details>

Now `SSH` from `Jenkins Server` ---> `Docker Server` and from `Docker Server` ---> `Jenkins Server`
```
ssh ubuntu@<Public IP of Docker>
```
```
ssh ubuntu@<Public IP of Jenkins>
```


1. Go to the **Web Browser** and open a new tab then enter the URL as shown:

   **http://< Jenkin's Public IP>:8080/** (It requests the **InitialAdminPassword** during the setup.)
   
2. To obtain the **InitialAdminPassword**, access the Jenkins Server by SSHing from the Jump Server, utilizing Jenkins' Public IP.
```
ssh ubuntu@xx.xx.xx.xx
```
From Jenkins execute the below command.
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
   (**Example:** "afbe8d33e25b4b908c0b9f91546f09e6")

#### Step-02:

1. Now, go back to jenkin's landing page in the **Web Browser**:
   
   (Enter the Jenkins URL as shown: **http://< Jenkin's Public IP>:8080/**)

2. Under Unlock Jenkins, enter the above **initialAdminPassword** & click **Continue**.
3. Click on **Install suggested Plugins** on the Customize Jenkins page.
4. Once the plugins are installed, it gives you the page where you can create a New **Admin User**. 
5. Enter the **User Id** and **Password** followed by **Name** and **E-Mail ID** then click on **Save & Continue**.
6. In the next step, on the Instance Configuration Page, verify your **Jenkins Public IP** and **Port Number** then click on **Save and Finish**

#### Step-03: Configuring Maven in Jenkins

Do the below in Jenkin's Dashboard:

1. Click on **Manage Jenkins** > **Plugins** > **Available Plugins** tab and search for **Maven**.
2. Select the "**Maven Integration**" Plugin and click **Install** (without restart).
3. Once the installation is completed, click on **Go back to the top page**.
4. On Home Page select **Manage Jenkins** > **Tool**.
5. Inside Tool Configuration, look for **Maven installations**, click **Add Maven**. 
6. Give the Name as **"Maven"**, Version-Default one (Latest), and **Save** the configuration.

#### Step-04:

1. Create a new project for your application build by selecting **New Item** from the Jenkins homepage.
2. Enter an item name as **hello-world** and select the project as **Maven Project** and then click **OK.**
   ( You will be prompted to the configure page inside the hello-world project.)
3. Go to the "**Source Code Management**" tab, and select Source Code Management as **Git**, Now you need to provide the GitHub Repository's **Master Branch URL** and **GitHub Account Credentials**.
4. In the Credentials field, you have to click **Add** and then click on **Jenkins**.
5. Then you will be prompted to the **Jenkins Credentials Provider** page. Under Add Credentials, you can add your **GitHub Username**, **Password**, and **Description**. Then click on **Add**.
6. In the Source Code Management page, navigate to Credentials, and select your GitHub credentials.
7. Leave all other values as default, navigate to the "**Build**" tab, and in the "Goals and options" section, input "**clean package**". Save the configuration.

   #### Note: 
   
   The 'clean package' command clears the target directory, Builds the project, and packages the resulting WAR file into the target directory.

8. Return to the Maven project "**hello-world**" and click on "**Build Now**" to initiate the build process for your application's **.war** file.

      * You can go to **Workspace** > **dist** folder to see that the **.war** file is created there.
      * war file will be created in **/var/lib/jenkins/workspace/hello-world/target/**

---------------------------------------------------------------------
### Task-2: Installing and Configuring Tomcat for Deploying our Application on the same Jenkins Server

![Deploy Code to Tomcat Server](https://github.com/janjiralakirankumar/DevOpsEssentials/assets/137407373/0bf912eb-86b9-4271-ac58-79f582175632)

* SSH into the Jenkins server as the root user and install the Tomcat web server.
  **Note:** (If you are already in Jenkins Server, again SSH is not needed.)

#### Step-01: Install Tomcat on to Jenkins Server

```
sudo apt update
```
```
sudo apt install tomcat9 tomcat9-admin -y
```
```
ss -ltn
```
**Note:** when you run `ss -ltn` command you'll see a list of `TCP sockets` that are in a listening state, and the output will also include information such as the `local address,` `port,` and the `state of each socket.`
```
sudo systemctl enable tomcat9
```

#### Step-02: Navigate to **server.xml** and change the Tomcat port number from **8080 to 9999** since port 8080 is already in use by Jenkins

1. Navigate to **server.xml** and change the port number
```
sudo vi /etc/tomcat9/server.xml
```
To Change the port from `8080` to `9999`
   * press esc & Enter **":"** and copy paste below code and then hit Enter
```
g/8080/s//9999/g
```
Save the file using `ESCAPE+:wq!`

2. To Verify whether the Port is changed, execute the below Command.
```
cat /etc/tomcat9/server.xml
```
**(Optional step):** If you are unable to open the file then change the permissions by using the below command.
```
sudo chmod 766 /etc/tomcat9/server.xml
```
3. Now Install Default JDK tomcat requires it.
```
sudo apt-get install default-jdk
```
Now restart the system for the changes to take effect
```
sudo service tomcat9 restart
```
```
sudo service tomcat9 status
```
**To exit**, press **Ctrl+C**

4. Once the Tomcat service restart is successful, go to your web browser and enter **Jenkins Server's Public IP address** followed by **9999** port.

   (Example: **http://< Jenkins Public IP >:9999**     (Or)     **http://184.72.112.155:9999**)

   * Now you can check the Tomcat running on **port 9999** on the same machine and it shows the WebPage.
  
#### Step-03: Copy the previously built **.war**

1. Copy the previously built **.war** file from the Jenkins workspace to the Tomcat webapps directory to deploy the web content.
```
sudo cp -R /var/lib/jenkins/workspace/hello-world/target/hello-world-war-1.0.0.war /var/lib/tomcat9/webapps
```

   <details>
     <summary>Here's a breakdown of the command:</summary>
     
   The above command is copying a `WAR (Web Application Archive)` file from the Jenkins workspace to the Tomcat web apps directory. Let's break down the command:
   
   - `sudo`: Run the command with superuser (root) privileges, as copying files to system directories often requires elevated permissions.
   
   - `cp`: The copy command in Linux.
   
   - `-R`: Recursive option, used when copying directories. It ensures that the entire directory structure is copied.
   
   - `/var/lib/jenkins/workspace/hello-world/target/hello-world-war-1.0.0.war`: Source path, specifying the location of the WAR file in the Jenkins workspace.
   
   - `/var/lib/tomcat9/webapps`: Destination path, indicating the Tomcat webapps directory where the WAR file is being copied.
   
   </details>

   **Note:** Assuming your Jenkins job generated `hello-world-war-1.0.0.war` in the workspace, this command copies it to Tomcat's webapps directory, enabling deployment and execution.

2. Access your browser and enter the Jenkins Public IP address, followed by port 9999 and the path (URL: **http://< Jenkins Public IP >:9999/hello-world-war-1.0.0/**).
3. Confirm Tomcat is serving your web page.
4. Stop tomcat9 and remove it to prevent slowing down the Jenkins server.
```
sudo service tomcat9 stop
```
```
sudo apt remove tomcat9 -y
```

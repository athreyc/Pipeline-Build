# Pipeline-Build: 

#Build stage setup instructions:


##Build


&nbsp;&nbsp;&nbsp;&nbsp;The following sub-sections describe how to create and configure the build server and the development environment.

Note: nd4j project has been used as the application  

###1. Environment and tools setup:

AWS EC2 instance has been used for the project and Jenkins is used as the build server. The relevant details are:  
EC2 Machine info: ubuntu-trusty-14.04-amd64-server-20150123 (ami-84562dec)  
Jenkins version: 1.597  

####Steps:
<ol>
<li> Create an EC2 instance on AWS via the AWS management console and start the instance </li>
<li> <p> SSH into the instance using the pem file for login and run the following commands to install java,maven and git: </p> 
<ul>
<li> sudo add-apt-repository ppa:webupd8team/java  </li>
<li> sudo apt-get update  </li>
<li> sudo apt-get install oracle-java7-installer maven git-core </li>
</ul>
</li>
<li> Follow the first three steps as in https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu for installing Jenkins </li>
<li> For the fourth step in installation, download the jenkins war file for version 1.597 and unpack it as sudo dpkg -i </p>  jenkins_1.597_all.deb </li>
<li> Set up the Apache proxy as in the same page to enable accessing the Jenkins server over the web </li>
<li> Acccess the Jenkins server via the web by typing the DNS hostname of the EC2 instance </li></li>
<li> <p> Download the following plugins via Manage Jenkins -> Manage Plugins -> Available tab  </p>
<ul>
<li> Amazon EC2 plugin  </li>
<li> GitHub API Plugin  </li>
<li> GitHub plugin   </li>
<li> Node and Label parameter plugin   </li>
<li> SSH plugin   </li>
<li> Workspace Cleanup Plugin  </li>
</ul>
</li>
<li> Specify the Jenkins URL via Manage Jenkins -> Configure System page to be the hostname of the EC2 instance </li>
<li> Create a new Maven job on Jenkins  </li>
</ol>  

###2. Github hook setup:
To set up the Github hook, do the following:  

####Steps:
<ol>
<li> Go to the newly created Maven job's configuration page and choose Git as the SCM tool </li>
<li> Specify the Repo URL as the SSH URL as obtained for https://github.com/athreyc/nd4j/ project </li>
<li> Choose the Build trigger as "Build when a change is pushed to GitHub" in addition to the default option. </li>
<li> Go to Github and set up a service hook for the repo by choosing Jenkins as the service and specifying the Jenkins URL as in the tool setup </li>
<li> Test the service by clicking the Test Service button on the same page to ensure the payload is delivered successfully </li>
<li> Do a test commit to the README file and check on Jenkins to see if a build has been triggered. </li>
<li> Check the Git Hook log for the Maven job to see the cause of the trigger </li>
</ol>  

###3. Dependency and Workspace clean-up:

####Steps:   

<ol>
<li> For dependency, edit the pom.xml file and delete all the dependencies. Then trigger a build manually on Jenkins to see if the build fails </li>
<li> Restore the pom.xml file to its original state and trigger the build again on Jenkins to see if the dependencies are picked up and the build proceeds normally </li>  
<li> For restoring to a clean state before every build, go to the Build Environment section in the job configure page and choose "Delete workspace before build starts" option </li>  
</ol>   

###4. Build Script:


&nbsp;&nbsp;&nbsp;&nbsp;For executing a shell script in say pre-build stage, go to Pre Steps section in the job configuration page and Choose Execute Shell in the listdown. Specify the desired shell script in the freeform text area </p>   

###5. Multiple node build:   

####Steps:   

<ol>
<li> Go to Configure System page on Jenkins and scroll down to the Cloud section. Choose Amazon EC2 from the listdown </li>
<li> Specify the access credentials as obtained from the AWS EC2 management console </li>
<li> Add the AMI details as desired for all the AMIs to be deployed as slaves. </li>
<li> For each of the slaves, specify a common label name to be used for running the build job. Save the configuration  </li>
<li> Go to the Maven job configuration page and choose the "This build is parameterized" option. Specify a name for the label and specify the value from the previous step. Choose the "Run on all nodes matching the label" and Node Eligibilty as "All Nodes" </li>
<li> Choose the "Restrict where this project can be run" option and specify the LabelName as from the previous step </li>
</ol>

###6. Status via HTTP:   

As mentioned in the environment setup, we can access the status of the build via HTTP by specifying the EC2 instance hostname on a browser.
















<h3>How to Enable X11 Forwarding setup in EC2 Instance</h3>

In this post, I discuss enabling X11 forwarding from Red Hat Enterprise Linux (RHEL), Amazon Linux, SUSE Linux, Ubuntu servers running on Amazon EC2. This is helpful for system and database administrators, and application teams that want to perform software installations on Amazon EC2 using GUI method. This blog provides detailed steps around SSH and x11 tools, various network and operating system (OS) level settings, and best practices to achieve the X11 forwarding on Amazon EC2 when installing databases like Oracle using GUI.

There are several techniques to connect Amazon EC2 instances to manage OS level configurations. Typically, you use SSH clients (such as PuTTY or SSH client) to establish the connection from the Windows OS-based bastion or jump servers to connect with Amazon EC2 instances running linux-based OS. Most commonly, database administrators use a common Database Management, bastion host, or jump servers to connect database servers. They do this instead of directly using their laptops connecting to the database servers. They can install all the needed tools in one server to perform database administrative or support activities. During the application installation or configuration, you might need to install software such as an Oracle database or a third-party database using GUI methods. This blog talks about steps that must be done in order to forward the X11 screen to your highly secure Windows OS-based bastion hosts. You can consider using NICE DCV as an alternative option for running GUI-based applications. Please refer to the prior link for more details and steps to enable NICE DCV.

Prerequisites
To complete this walkthrough the following is required:

Ensure that you have a bastion host running on Amazon EC2 with Windows OS for this blog. This OS must have access to the EC2 machines running Linux such as RHEL, Amazon Linux, SUSE Linux, and Ubuntu servers. If not, please configure a bastion host using Windows operating system with needed SSH access via port 22 to EC2 instance running linux-based operating systems. You can use any OS-based systems as a bastion host as long you have corresponding client tools installed or X11 supported by that OS.
I recommend having bastion hosts in the same Availability Zone or Region as the EC2 Linux hosts that you plan to connect and forward X11 to. This is to avoid any high latency in X11 forwarding during your application installations.
Install tools such as PuTTY and Xming on the Windows-based bastion host from which you want to SSH to Linux EC2 host and X11 forwarding.
In order to securely configure or install PuTTY, refer to the section Configuring ssh-agent on Windows in the blog post Securely Connect to Linux Instances Running in a Private Amazon VPC.
You may need sudo permission to run X11 forwarding commands as a root user in order to complete the setup.
Solution
Connect to your EC2 instance using SSH client, and perform following setup as needed.

Step 1: Install required X11 packages
Install X11 packages with following command based on your operating system release and version:

Installing xclock or xterm packages are optional as this is installed in this post to test the X11 forwarding using xclock or xterm commands.

 

Amazon Linux 2:

To install X11 related packages:

$ sudo yum install xorg-x11-xauth

To install X11  testing tools:

$ sudo yum install xclock xterm

 

Red Hat Enterprise Linux 8:

To install X11 related packages:

$ sudo yum install xorg-x11-xauth

To install X11 testing tools:

$ sudo yum install xterm

Note: The xorg-x11-apps package has been provided in the CodeReady Linux Builder Repository for RHEL8. So, I skipped installing this package, which has xclock and I used only xterm to test the X11 forwarding.

 

SUSE Linux Enterprise Server 15 SP1:

To install X11 related packages:

$ sudo zypper install xauth

To install X11 testing tools:

$ sudo zypper install xclock

 

Ubuntu Server 18:

To install X11 related packages and tools:

$ sudo apt install x11-apps

Step 2: configure X11 forwarding
To enable X11 Forwarding, change the “X11Forwarding” parameter using vi editor to “yes” in the /etc/ssh/sshd_config file if either commented out or set to no.

$ sudo vi /etc/ssh/sshd_config

 

To Verify X11Forwarding parameter:

$ sudo cat /etc/ssh/sshd_config |grep -i X11Forwarding

You should see similar output as the following:

X11Forwarding yes

To restart ssh service if you changed the value in /etc/ssh/sshd_config:

 

Amazon Linux 2, RHEL 8 and SUSE Linux OS:

$ sudo service sshd restart

 

Ubuntu Servers:

$ sudo service ssh restart

 

Step 3: Configure putty and Xming to perform X11 forwarding connect and verify X11 forwarding
Log in to your Windows bastion host. Then, open a fresh PuTTY session, and use a private key or password-based authentication per your organization setup. Then, test the xclock or xterm command to see x11 forwarding in action.

Click the xming utility you installed on Windows bastion host and have it running.

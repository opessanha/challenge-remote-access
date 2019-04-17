# Accessing a remote service

In this challenge, you will propose a way to access a service running on a remotely blocked port.

Consider the architecture proposed on the figure below:
 
![alt text](https://github.com/intelie/challenge-remote-access/raw/master/example%20network%20infrastructure.png "Example network infrastructure")

The only allowed connection between Client A and Server A is via SSH.
The only allowed connection between Server A and Server B is via SSH.

We need to access, from Client A and using HTTP, a service running on port 8000 of Server B.

Note:
> There is a service running on port 8000 of Server A.

Both client and servers run CentOS 7 without X.

We expect you to:
* Provide the set of commands that allow the service to be accessed
* Provide a document, in english, explaining each of the commands, their possible outputs, including failures, and why you chose them

## Solve this challenge

To solve this challenge, you may fork this repository, then 
send us a link with your implementation. Alternatively, if you do not want to have this repo on
your profile (we totally get it), send us a 
[git patch file](https://www.devroom.io/2009/10/26/how-to-create-and-apply-a-patch-with-git/) 
with your changes.

There is no unique solution to this challenge. The intent is to evaluate candidate's ability and familiarity with tools and best practices.

If you are already in the hiring process, you may send it to 
 whoever is your contact at Intelie. If you wish to apply for a job at 
 Intelie, please send your solution to [trabalhe@intelie.com.br](mailto:trabalhe@intelie.com.br).
 
 ###
 
  • In order we have to assume Network Configuration is configured and running for this task, and as the NOTE say´s we have the service running in Server A on the 8000 port.
      
    • Then we have to secure SSH in both server´s A and B and set up public key authentication to increase the security of your server with a private SSH key to log in, first generate a SSH Key Pair from Servers, with a following command below:
             # ssh-keygen -t RSA -b -2048
    • Then we have to check if it´s created, Type:
          # ls ~/.ssh
           must show you these 3 files:
                  id_rsa  id_rsa.pub  known_hosts

    • After the SSH key pairs had been generated, copy the key to the Servers A and B to connect to, by issuing the below command. Initially, enter your remote SSH user password in order to copy the public key.
      # ssh-copy-id ClientA@SSH_SERVER_A
      # ssh-copy-id ClientA@SSH_SERVER_B
      
    • You can copy manually to both servers A and B , to do this you must do the following comands:

             # scp ~/.ssh/id_rsa.pub <IP For Client A>:~/.ssh/key.pub 


    • After the SSH public key has been copied to Server´s A and B, you must login from the Server to check the SSH with the following command. 

            # ssh <IP For Client A>
       (Note:  you must type the IP Address assigned to Client A to connect.)

     # cat ~/.ssh/key.pub >> ~/.ssh/authorized_keys 

      
    • By now, we have to arrange for manipulate Services on the Server´s A and B ,and for this task we are going to use SystemCTL utility, by the command below:

 #
             # systemctl list-units


    •  To List all services (including enabled and disabled) :
       
      # systemctl list-unit-files –type=service

            must show as below example:
               UNIT FILE                                   STATE   
                arp-ethers.service                          disabled
                auditd.service                              enabled 
                autovt@.service                             disabled
                blk-availability.service                    disabled
                brandbot.service                            static  
                collectd.service                            disabled
                console-getty.service                       disabled
                console-shell.service                       disabled
                cpupower.service                            disabled
                crond.service                               enabled 
                



    • Firewalld is the main firewall utility that uses interacts with in order to manage iptables rules. To enable and start and verify the firewall we must execute the following commands:


                # systemctl enable firewalld   (thats´s to Enable Firewalld)

                # systemctl start firewalld     (that´s to Start Firewalld)

                # systemctl status firewalld   (to check the status of Firewalld)


    • Now we have to open the Port 8000 on Server ´s A and B to Client A Access the Service, doing the set of commands:
      
       (Note : In order to open a specific service to incoming connections, first verify if the application is already present in firewalld rules and, then, add the rule for the service, as shown in the below example which allows SSH incoming connections. Use “--permanent switch to add the rule permanently.

                # firewall-cmd --add-service=[tab]  #List services 
                     (listing the services already setup on Firewalld)
                # firewall-cmd –add-service=ssh
                     (to add the service SSH on Firewalld)
                # firewall-cmd --add-service=ssh –permanent
                    (to permanently add the service SSH on Firewalld)

    • In case the service is now already defined in firewalld rules, you can manually add the service port, as in the below:
      # firewall-cmd –add-port=8000/tcp --permanent
      (to add the Port 8000 on SSH permanently)
      # firewall-cmd --reload
       
      (to check if it´s applied we must reload Firewalld )
and for last we can do another time the list of services on firewalld again by:

            # firewall-cmd --add-service=[tab]  #List services 

            (listing the services already setup on Firewalld)

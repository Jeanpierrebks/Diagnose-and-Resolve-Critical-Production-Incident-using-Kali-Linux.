# Diagnose-and-Resolve-Critical-Production-Incident-using-Kali-Linux.

## Objective
As a Junior Security Analyst, the objective of this project is to diagnose and resolve a critical production incident in which a business-critical application has become inaccessible to end users. Using a Kali Linux environment, this project demonstrates the practical application of Linux-based troubleshooting methodologies to identify the root cause of the service disruption.
The analyst will systematically investigate the application server by leveraging core Linux command-line utilities to perform log analysis, process inspection, directory traversal, file discovery, and permission auditing. Corrective actions will be applied to restore service availability, ensuring the application returns to a fully operational state.

### Skilled Learned
- Applied structured Linux-based incident response techniques to diagnose and remediate a production service outage, simulating real-world SOC and sysadmin workflows.
- Utilized core CLI utilities (ls, cd, find, chmod, chown, ps, grep, etc.) to navigate the filesystem, locate misconfigurations, and inspect running processes under operational pressure.
- Conducted file permission auditing to identify unauthorized or misconfigured access controls contributing to application failure, and applied least-privilege remediation principles.
- Performed log analysis and process inspection to trace the root cause of service disruption, reinforcing a evidence-based, methodical troubleshooting approach.
- Demonstrated proficiency in Linux filesystem navigation and file management within a security-focused environment, reinforcing foundational skills critical to Blue Team operations.
- Applied root cause analysis (RCA) methodology to move beyond surface-level symptoms and identify the underlying failure point driving the incident.
- Practiced service restoration procedures by validating corrective changes and confirming application availability post-remediation, mirroring production incident resolution workflows.

### Tools Used
| Tools / Command      | Purpose                           |
|------------|---------------------------------------------|
|ls / ls -la | Directory listing and permission inspection |
|cd / pwd    | Filesystem navigation and path verification |
|find        | Locating files and directories across the system |
|chmod / chown | Modifying file permissions and ownership |
|cat / less / grep | Log reading and pattern-based output filtering |
|ps / top    | Process inspection and service status monitoring |
|systemctl   | Managing and restarting application services |
|Vim         | Text editor                                  |
|Kali Linux  | Primary operating environment for investigation |

## Steps
While attempting to download a Linux image, I ran into the following error.

<img width="1038" height="82" alt="image" src="https://github.com/user-attachments/assets/39a01e2e-ce30-4ee2-a32b-d873bff7bb8c" />

To remediate this situation, I proceeded with an attempt to update Kali's repository signing key as it is erroring out as outdated.
Given the unsuccessful attempt, with the terminal showing that the kali-archiving-keyring is already the newest version available, I manually imported the key with the command below.

<img width="927" height="253" alt="image" src="https://github.com/user-attachments/assets/2d258b15-1450-4985-b8ea-afff5de24bf9" />

I updated, then installed docker.io

<img width="915" height="568" alt="image" src="https://github.com/user-attachments/assets/0e671ab1-efeb-4a77-96d6-d54283782d01" />

After the successful installation, I started docker.io, and ran a status command to ensure it was running without error.

<img width="860" height="337" alt="image" src="https://github.com/user-attachments/assets/5f276e4c-8296-4477-b684-edc3f7a71f9d" />

Using docker, I've pulled the Linux image below for this project lab.

<img width="495" height="311" alt="image" src="https://github.com/user-attachments/assets/360a0386-d02f-4cf3-9770-116be1804434" />

I added myself as a user to avoid having to use sudo everytime. Then, I ran the image as a container with the -it command to have the Linux terminal inside a Linux container.

<img width="495" height="105" alt="image" src="https://github.com/user-attachments/assets/9460c5b9-9cb8-4366-997d-7c74385bc150" />

To validate the Linux environment we are on, I ran the uname command.

<img width="487" height="84" alt="image" src="https://github.com/user-attachments/assets/8504564b-a7bf-4693-aff7-a3b8253e879c" />

Now that we are running the Linux image, I reviewed the application logs to see whether there were any errors.
To do that, I first needed to find where the application logs were in the file system.
First, I ran the pwd command to see where we are currently located, which is the /root.
Then, I changed directory using cd /var/log/application.
Once in the directory, I ran the ls command to display the files available.

<img width="369" height="133" alt="image" src="https://github.com/user-attachments/assets/88a418a1-7bf3-4cf3-aa1d-729d71255eb4" />

I ran the ls -l command to show files details like permissions, owner, size and modification date.

<img width="412" height="100" alt="image" src="https://github.com/user-attachments/assets/4c67de35-2d2c-408d-b8c6-0e9f3d82a5e0" />

I reviewed each file by using the concatenate command.
After running the command, I have noticed multiple log error status on which database connections had been refused on port 5433.

<img width="719" height="294" alt="image" src="https://github.com/user-attachments/assets/4ec2c886-29d3-4bb7-b79f-299b089f72b3" />

Now that I know what is causing the error, I used the pipe and grep command to only display the line containing the error.

<img width="711" height="147" alt="image" src="https://github.com/user-attachments/assets/d83f5ddc-129e-499b-b425-04a1bb3b36cd" />

Before I proceed further, I saved the result of the above command in a separate file so it can be referenced later if needed. This will be achieved by using the previous command cat error.log | grep Database > database_errors.txt. 
I ran the ls command to confirm the creation of the new file and I can see the file named cat error.log | grep Database > database_errors.txt listed.

<img width="735" height="67" alt="image" src="https://github.com/user-attachments/assets/c864d21b-26ce-4eda-aada-b29b25f79c78" />

To confirm that the file above was created with its content, I ran the ls -l command to see whether the file contains data, but also confirm the modification date and time.
Furthermore, I ran the file itself cat dababase_errors.txt to review its content.

<img width="713" height="242" alt="image" src="https://github.com/user-attachments/assets/4c3550a1-1778-47d3-91d4-34608967fb25" />

For redundancy, I've made another copy of the same file in a different folder. Then ran the ls command to confirm the copy was successfully made into /root.
As previously, I've ran the cat command to review the file and ensure that it did copy with its content.

<img width="712" height="224" alt="image" src="https://github.com/user-attachments/assets/038853f1-a7f9-4b3e-976a-32b9d9cf339c" />

Now that I've made a copy, I ran a new command to filter out all database connections that had been refused using the grep command as follows, cat database_errors.txt | grep "connection refused".
I then ran another command to count the number of times the connection has been refused.

<img width="804" height="151" alt="image" src="https://github.com/user-attachments/assets/b8fb79db-3d06-4906-9f07-cd386ab011a8" />

PostgreSQL's default post is always 5432, however, the application is trying to connect to post 5433. To confirm this, I will need to look into the database configuration file to determine which port the app should be connected to, using the find command.

<img width="591" height="726" alt="image" src="https://github.com/user-attachments/assets/6babd1ca-868f-4245-8235-434d20a74430" />

I then sorted through the result to find a file containing db using the grep command.

<img width="586" height="100" alt="image" src="https://github.com/user-attachments/assets/2f61d1a4-9faf-4a48-9552-ce1fa7ee9488" />

Using the result above, I changed directory using the cd command to review the files. While reviewing this file, I've noticed that the database port is set to 5433.

<img width="483" height="151" alt="image" src="https://github.com/user-attachments/assets/2d4b94db-5175-4f96-abf7-95055726db06" />

I reviewed the difference between the db.conf anf db.conf.backup files using the diff command.

<img width="518" height="82" alt="image" src="https://github.com/user-attachments/assets/eaf04dc4-2912-441d-9c4a-7c3c46b15749" />

Looking at the result above, I can confirm that the database port changed from 5432 to 5433. That is an issue because port number 5432 is used for file transfers to and from the database. Having the wrong port number prevents the app from communicating with the database. 
I reviewed whether the database is running, and confirmed that it is running on port 5432 or 5433.

<img width="528" height="68" alt="image" src="https://github.com/user-attachments/assets/7d815c74-fa6e-446f-b229-ddcc16d75ac9" />

After running the command above, I confirmed that the database is currently running on port 5432. This confirms that the issue is caused by the application attempting to connect to the wrong port.
To correct this issue, I updated the db.conf file by entering the correct port number. I started by showing the content of the file. Then using the vim text editor command, I proceeded to update the port number.
I first ran the ls -l command to review the permission. To prevent future errors, I've changed the permissions to rw for the root user and no permission for everyone else.
I performed the same change for the backup file but for read only for the root user and no access for everyone else.

<img width="497" height="163" alt="image" src="https://github.com/user-attachments/assets/733f7ce6-35d7-47d2-bf74-c888e91bc0f0" />

I attempted to use the vim command to correct the port number on the db.conf file, then press I for insert.

<img width="1042" height="1037" alt="image" src="https://github.com/user-attachments/assets/40aaaa8a-0679-4db5-8db2-0def1687b2c3" />

Using my keyboard, I navigated to database_port=5433 and updated the port number to 5432, then ESC to return to normal mode, and :wq for save and quit.
After saving the change, I ran the following cat db.conf command to confirm that the change is effective in the db.conf file.
I can indeed confirm that the change made was successful as shown below.

<img width="390" height="115" alt="image" src="https://github.com/user-attachments/assets/5facfa3b-e2cd-4d8d-854f-ad549f2d1e1e" />

Now that the port has been updated, the application should connect to the database without error once restarted.

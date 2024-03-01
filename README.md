

  <h1>SOC Attack and Detection with Lima Charlie, Sigma, & Sliver</h1>

  <h2>Introduction</h2>
  <p>Recently, to optimize telemetry storage costs for my organization, I began researching alternatives to traditional SIEMs, which led me to look into Lima Charlie. Lima Charlie is a solution that offers endpoint detection and response but with free storage for up to one year's worth of data. 
</p>
<p>To evaluate my understanding of Lima Charlie, I followed the SOC Analyst home lab instructions from Eric Capuano's blog series, which can be found here. Eric's blog series was easy to follow and taught many hands-on practical cybersecurity skills used in the field.
</p>

  <h2>Skills Covered</h2>
  <ul>
    <li>Simulate cybersecurity attack and defense roles</li>
    <li>Set up a virtualized environment in VMware Workstation with Windows and Linux</li>
    <li>Disabling Windows Defender via registry and system settings</li>
    <li>Utilize Command Prompt, PowerShell, and Linux Terminal</li>
    <li>Remote accessing VMs using SSH</li>
    <li>Establish a C2 server with Sliver, deploying a payload on the victim machine</li>
    <li>Analyze telemetry and EDR with Sysmon and LimaCharlie for detection and defense against Sliver</li>
    <li>Detection & Response rule creation</li>
    <li>Sigma rule creation and implementation</li>
  </ul>


  <h2>Project Workflow</h2>
  <p>Below is a summary of my steps with this SOC Analyst lab. Much of it mirrors Eric's blog series and incorporates any issues I encountered. If you want a complete step-by-step tutorial, follow along with the original blog. </p>

  <h3>Environment Setup</h3>
      <ul>
        <li>I set up a SOC environment utilizing Linux Ubuntu Server as the attack machine as it is lighter weight than Kali to conserve resources and for practice using Terminal. A static IP was implemented for simplicity's sake and to ensure the SOC environments function in the future.  A test for functionality with the static IP can be seen below:</li>
<br/>
        <img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture1.jpg>
<br />
<br />
 <li>For the target machine, a Windows 10 box was utilized. Windows Defender was disabled entirely via Windows Registry, as shown here: </li>
      <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture2.jpg>
<br />
<br />
        <li>Next, SysMon and a Lima Charlie EDR sensor were installed via PowerShell:</li>
        <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture3.jpg>
<br />
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture4.jpg>
<br />
<br />
<li>After that, a rule was created in LimaCharlie to integrate SysMon telemetry alongside the LimaCharlie telemetry. This is needed as Sigma rules that we will implement later heavily rely on SysMon logs. Rule creation is shown here: </li>
        <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture5.jpg>
<br />
<br />
<h3>Sliver Attack Setup</h3>
<li>From here, I used SSH to remotely connect to the Linux server and install Sliver, which I will use for C2 attacks. After installation, I generated the C2 payload as shown here: </li>  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture6.jpg>
<br />
<br />
<li>To transfer the payload from the attack machine to the victim, I created a temporary server via Python3. Remember in Linux the file name is case-sensitive and make sure you are still in Terminal as root, otherwise, you will get the error shown in the first attempt below:</li>  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture7.jpg>
<br />
<li>After executing the payload file as an administrator on the Windows 10 box, a C2 session was established. If you are having issues with your session, make sure you are in the Linux Terminal as root. Otherwise, you will not be able to communicate over port 80. </li>  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture8.jpg>
<br />
<br />  
<li>Next, I confirmed the session and then checked privileges (if your privilege list is much shorter than this, you must execute the payload on the victim's machine as an Administrator):  </li>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture9.jpg>
<br />
<br />
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture10.jpg>
<br />
<br />
<li>Sliver can also easily detect what security measures a system has in place by using the ps -T command (security measures will be highlighted in red) as shown here: </li>  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture11.jpg>
<br />
<br />
        <h3>LimaCharlie IoC Investigation</h3>
<li>The process for the file has not been signed:</li>  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture12.jpg>
<br />
<br />
<li>Network connections for the file: </li>  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture13.jpg>
<br />
<br />
<li>When checking a hash in VirusTotal, if nothing is returned, it is an important lesson for a SOC analyst to learn. This is because any newly created malware file will not show up in VirusTotal until it has been identified, which means that the absence of results may indicate a suspicious file. Since VirusTotal has scanned almost everything, the lack of results may indicate that the threat is a highly targeted and custom-crafted sample, which is a major concern for the security of the system.</li>
 <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture14.jpg>
<br />
<li>Searching for when file first appeared:</li>  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture15.jpg>
<br />
<br />
<li>Timeline analysis after pinpointing the file’s introduction:</li>
 <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture16.jpg>
<br />
<br />
        <h3>Rule Creation</h3>
<li>To generate attack activity, I will utilize a common technique of dumping the lsass.exe process from memory. This tactic is used to steal credentials. </li> <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture17.jpg>
<br />
<br />
<li>Telemetry generated from Procdump:</li>  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture18.jpg>
<br />
<br />
<li>Now that I know what this traffic looks like, I can create a D&R rule for it and test it. </li>
  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture19.jpg>
<br />
<br />
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture20.jpg>
<br />
<br />
<li>After dumping the lsass.exe file again our detection alert was successful: </li>  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture21.jpg>
<br />
        <h3>Blocking Attacks</h3>
<li>The ultimate objective is to prevent an attack from happening rather than just detecting it. However, implementing such a rule in a production environment requires proper baselines and tuning for false positives, which can take several days or weeks. This is important to avoid any potential problems that may arise in your environment. Nonetheless, we can create the rule now since this is a lab environment. </li> 

<li>Next, I will create a rule to block a common tactic ransomware attackers use, which involves deleting Volume Shadow Copies. These copies help restore files on a system to a previous state. However, their deletion is crucial for a successful ransomware attack. A common command that would accomplish the deletion on these volumes is: 
</li> 
 <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture22.jpg>
<br />
<br />
<li>This command is also one that is not commonly run in healthy environments, making it a prime candidate for a blocking rule as it has low false positive prevalence and high threat activity. </li> 
<li>Deleting the Volume Shadow Copies generated in event activity in LimaCharlie. Sigma Rules also contain references (as seen on the right); these can be a great help in quickly understanding why a detection exists in the first place:</li> <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture23.jpg>
<br />
<br />
<li>Now to create a rule from this event. Using LimaCharlie’s event template, I will craft a response action that will take place when this activity is observed. </li> 
<ol>
  <li>-	“action: report” is what fires the detection report to the “Detections” tab</li>
  <li>-	“action: task” is responsible for killing the parent process responsible with deny_tree for the “vssadmin delete shadows /all” command</li>
</ol><br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture24.jpg>
<br />
<br />
<li>After running the attack again, the whoami command hangs and does not return anything as the parent process was terminated. This is effective because terminating the parent process typically contains the ransomware payload or is the lateral movement tool in a real ransomware attack.</li>
  <br/>
<img src=https://github.com/Dlacey1/Sigma-LimaCharlie-SOC-C2-Instance/blob/main/images/Picture25.jpg>
<br />
<br />
  

  <h2>Recommendations and Conclusion</h2>
  <p>This lab was well-designed and comprehensive. I highly recommend it. After completing the lab, I can confirm that Lima Charlie is a powerful tool, thanks to its extensive EDR functionality and long-term storage that is free of cost. </p>

  <p>After the lab, Lima Charlie would be a great solution to reduce telemetry overhead for my organization. However, it is essential to note that Lima Charlie is an EDR solution and not as powerful as traditional SIEMs. Therefore, implementing a hybrid solution combining Splunk and Lima Charlie would be the best approach. This approach would allow us to use Splunk for initial monitoring and utilize Lima Charlie for longer-term telemetry - giving us the best of both worlds. </p>

  <hr>


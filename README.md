**Platform:** LetsDefend

**Lab Name:** SOC138 - Deteced Suspicious Xls File

**Difficulty:** Medium

**Date Completed:** Jul 6, 2026

**Type:** Malware/Phishing

**Target:** Sofia (172.16.17.56), user account "Sofia2020"

---
<h3 align =center> GOAL </h3>

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Determine whether the flagged Excel macro file is malicious, trace any resulting network activity, contain the endpoint if compromised, and document the full incident for future reference.
<br>
<br>

---
<h3 align =center> Walkthrough </h3>

***Alert Review:***

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Opening the alert in the Monitoring tab under the Investigation Channel reveals all the critical details. 
<br>
<br>

<img width="1025" height="349" alt="1" src="https://github.com/user-attachments/assets/b85ab0f6-e159-4904-a663-9fb4d7d46131" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Notice the file extension: .xlsm, not .xlsx. The "m" means the Excel file can contain macros (small programs). Macros can run code on your computer, and attackers often use them to spread malware. This immediatly raises my suspicion before I've even analyzed the file. Now, look at Device Action: Allowed.  because this was Allowed, I have to assume the file may have actually landed and executed on SusieHost. let's analyze this alert deeper by creating playbook.
<br>
<br>

---
***Starting the Playbook:***

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Once you've reviewed the alert, head to Case Management and start the playbook. Think of the playbook as your structured checklist — it makes sure you don't skip steps under pressure.
<br>
<br>

<img width="567" height="245" alt="2" src="https://github.com/user-attachments/assets/bb4009cf-8b55-457e-8a2e-c8b9b0e015d4" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; In this case, the appropriate selection is "Unknown or unexpected outgoing internet traffic". Becase a malicious macro's whole purpose is download the pay load from outside or make C2 server.
<br>
<br>

---
***Log Management and Endpoint Investigation:***

<img width="590" height="275" alt="3" src="https://github.com/user-attachments/assets/c7328715-701a-4a43-b4db-8f746f8a2924" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Let's move to Log Management and Investigat the traffic.
<br>
<br>

<img width="860" height="281" alt="4" src="https://github.com/user-attachments/assets/542eb010-8d4d-442f-af2a-230a7574d058" />

<img width="834" height="284" alt="5" src="https://github.com/user-attachments/assets/357610bd-40c6-4fcd-ad09-04e5e12efcd2" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Here the raw logs are encoded, So we can't read as a plaintext. What is matters here is, there was a successful C2 callback to an extenal IP on port 443 immediately after a malicious marco executed.Now let's go to the endpoint itself to look for evidence.
<br>
<br>

<img width="750" height="532" alt="6" src="https://github.com/user-attachments/assets/dfee9827-34c7-4ede-97f1-e12b531d209e" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; I can see the Host's last login was nearly five moths before the actual attack. It means the sesion that opened the file didn't register as a fresh ineraction login. Because of this we didn't get any evidence from the Endpoint. but due to the extenal IP connection, we can say it as "Not Quarantined".
<br>
<br>

---
***Malware Analyze:***

<img width="568" height="321" alt="7" src="https://github.com/user-attachments/assets/023f0d5f-bbbb-4b6b-ab9d-340a9b7bf6fd" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Let's get the URL for the file from the alert by right click the Download button and copy the link. Run the link on AnyRUN to analyze.
<br>
<br>

<img width="1278" height="699" alt="8" src="https://github.com/user-attachments/assets/ee12f2eb-618f-4ee6-8f2a-c644733d63ed" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The sandbox explicitly tags the sample phishing, phish-doc, and arch-doc. Once the malware is active, it established a unauthorized network connections to external. let's also conform with VirusTotal (I mean adding evidence). Copy the hash of the malicious file and search in VirusTotal.
<br>
<br>

<img width="1264" height="703" alt="9" src="https://github.com/user-attachments/assets/d1472fbf-a299-4025-90a3-d3d7dd354719" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The detection ratio is the number that matters most here: 46 out of 64 security vendors flagged this file as malicious. So the Answer is "Malicious".
<br>
<br>

---
***Containment:***

<img width="590" height="308" alt="10" src="https://github.com/user-attachments/assets/3df0144e-b68f-4339-818e-9f2fc7faf677" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; As we know from own Log Investigation, the malware file made a C2 server connection. So, the Answer is "Accessed".
<br>
<br>

<img width="598" height="279" alt="11" src="https://github.com/user-attachments/assets/760c0192-7046-4b27-ad3b-6d6f3f6e68d1" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Since the malicious macro executed, successfully contacted a C2 server, we need to isolate the machine. let's go to the Endpoint and Containment the Host.
<br>
<br>

<img width="756" height="526" alt="12" src="https://github.com/user-attachments/assets/3eccaa8e-b160-483d-8b50-dfafcc26e672" />

---
***Documentation:***

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; This was the area, where we log our IOCs so they can be used for future detection and threat hunting across the environment. We add two artifacts, Malicious hash of the Source IP, Destination IP and Source IP.
<br>
<br>

<img width="564" height="393" alt="13" src="https://github.com/user-attachments/assets/2bd24c34-ce37-4782-9620-96f6f34a0877" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; The Analyst Note is where I write the full story in plain language, as if explaining it to someone who wasn't there for any of the investigation.
<br>
<br>

<img width="584" height="332" alt="14" src="https://github.com/user-attachments/assets/24435ad1-2808-49d1-8aac-d850399a85fc" />

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; Close the alert as a True Positive.
<br>
<br>

---
<h3 align =center> Summary </h3>

<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp; This alert closes as a True Positive. The file ORDER SHEET & SPEC.xlsm was a macro-enabled phishing attachment and because the Device Action was Allowed rather than blocked, the macro executed freely on host Sofia. It successfully called back to a command-and-control server at 177.53.143.89 over port 443. The host has been contained, and all the Artifacts are documented for future detection.
<br>
<br>

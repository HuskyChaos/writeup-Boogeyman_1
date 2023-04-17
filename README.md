# Boogeyman 1  
![Logo](./Screenshot%202023-04-17%20at%205.42.33%20PM.png)
## Room Link => [Boogeyman 1](https://tryhackme.com/room/boogeyman1)  

### Task 2  
1. What is the email address used to send the phishing email?  
Ans : `cat dump.eml | grep From`  

2. What is the email address of the victim?  
Ans : `cat dump.eml | grep To`  

3. What is the name of the third-party mail relay service used by the attacker based on the DKIM-Signature and 
List-Unsubscribe headers?  
Ans : `cat dump.eml | grep DKIM`  
*Pay attention to the answer format for this one.*  

4. What is the name of the file inside the encrypted attachment?  
Ans : `cat dump.eml`  
*You will see a zip file in base64 format.*  
`echo {base64} | base64 -d > Invoice.zip`  
`unzip Invoice.zip`  
*The password is also there in the email.*

5. What is the password of the encrypted attachment?  
Ans : `cat dump.eml | grep code`  

6. Based on the result of the lnkparse tool, what is the encoded payload found in the Command Line Arguments 
field?  
Ans : `cat {unziped file}`  
*The answer is the base64 encoded text after `-enc`*  

### Task 3  
1. What are the domains used by the attacker for file hosting and C2? Provide the domains in alphabetical order. 
(e.g. a.domain.com,b.domain.com)  
Ans : `cat powershell.json | jq .ScriptBlockText | grep -i '$s'`  
*All the commands executed by the attacker is in `.ScriptBlockText` so i am using jq filter for that and i am 
using grep to mak eit easy for you. You can ignore that part if you want to.*  

2. What is the name of the enumeration tool downloaded by the attacker?  
Ans : `cat powershell.json | jq .ScriptBlockText`  
*Look for a github link and pay attention to the answer format as well.*  

3. What is the file accessed by the attacker using the downloaded sq3.exe binary? Provide the full file path with 
escaped backslashes.  
Ans : `cat powershell.json | jq .ScriptBlockText`  
*You will find the answer for this one in two parts. The answer format is looking for the absolute path of the 
file. The first part will be from `C` to users directory and second will be from there to the file name.*  

4. What is the software that uses the file in Q3?  
Ans : `cat powershell.json | jq .ScriptBlockText | grep -i 'q3'`  
*Again, pay attention to the answer format. Your answer exists without spaces.*  

5. What is the name of the exfiltrated file?  
Ans : `cat powershell.json | jq .ScriptBlockText`  
*Find the attackers IP and you will have your answer.*

6. What type of file uses the .kdbx file extension?  
Ans : *Google the file extenssion and your answer will be on top.*  

7. What is the encoding used during the exfiltration attempt of the sensitive file?  
Ans : `cat powershell.json | jq .ScriptBlockText`  
*You can guess this one from the answer format but in case you can't, go through the output and you will find the 
file being converted into the encoding format and then split into parts and further getting transfered.*  

8. What is the tool used for exfiltration?  
Ans : `cat powershell.json | jq .ScriptBlockText | grep {encoding format}`  
*The first line has the name of that tool.*  

### Task 4  
1. What software is used by the attacker to host its presumed file/payload server?  
Ans : *Which tool do you use to start a server to transfer file when playing a CTF ?*  

2. What HTTP method is used by the C2 for the output of the commands executed by the attacker?  
Ans : *Answer for this one is in the same line as the answer for Task 3 Q1 is.*  

3. What is the protocol used during the exfiltration activity?  
Ans : *Since you have the answer for Task 3 Q8, You can easity answer this.*  

4. What is the password of the exfiltrated file?  
Ans : To answer this, we need all the output from the attackers commands.  
    1. `tshark -r capture.pcapng --export-objects http,./exports`  
    2. `cd exports`
    3. `touch script.py`
    4. ```
        f = open('27fe2489', 'r')
        for i in f:
            i2 = i.split()
            for k in i2:
                print(chr(int(k)), end='', sep='')
        for i in range(1,84):
            f = open(f'27fe2489({i})', 'r')
            for j in f:
                if "OK" not in j:
                    j2 = j.split()
                    for k in j2:
                        print(chr(int(k)), end='', sep='')
        ```  
    5. `python3 script.py > res.txt`
    6. `mousepad res.txt`
    7. Use Find and look for the word `Password`.

5. What is the credit card number stored inside the exfiltrated file?  
Ans : We know how the data was split and transfered over so we just need to put the pieces back together and use 
the password we found to get our final answer.  
    1. `tshark -r capture.pcapng | grep 0x0002 | grep response > res.txt`  
    2. `touch script.py`  
    3. ```
        f = open('res.txt', 'r')
        for i in f:
            print(i.split()[12].split('.')[0],end='',sep='')
        ```  
    4. Copy the output in cyberchef with hex decoder.
    5. Save the output to a file with the extenssion `.kdbx`  
    6. `keepass2 {filename}.kdbx`
    7. Enter the password from previous question.
    8. Right click on `company Card` > Copy Field > Account Number.

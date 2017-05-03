---
layout: notes
title: Guest Lecture on Computer Fraud and Abuse Act
scribe: Konstantino Sparakis
---

## Class Presentation: Swift attack on Bangladesh bank

#### Quick Overview
- $81 Million was stollen from the banking system
- $951 Million could have potentially been stollen but the messages were blocked, They misspelled Foundation "Shalika Fandation" and were found out.
- Happened in Early February of 2016
- They determined it was the smaller lower banks that put the whole system at risk.
- Hackers executed the attack by gaining access to machines inside the local banks that use the swift messaging protocol to fake messages that wired money to random accounts in the Philippines. 
- FBI claims North Korea was involved, there are also Russian variable names in a trojan found in the system. But overall the trojan code is shared online so any one could have carried this out.

- There is no information on the actual authentication primitives that it uses.

#### What is swift?
Swift is a "secure" messaging service, built for banks

##### Bangladesh Central bank vs NY Federal Reserve Lawsuit
- This lawsuit makes a lot of evidence and data on how this hack and the current state of affairs private, for the moment

#### How was the attack done?
- The attackers needed access to the SWIFT machines running locally in the banks
- There was evidence that there might have been correspondence with an employee to gain acess
- Old switches and routers could have potentially allowed for this to happen.
- Once hackers had access to the SWIFT machines, they were able to modify the .dll that sends msgs in order to by pass the employees monitoring the messages

#### Avoiding this attack
- Moving things to the cloud since they have bad hardware and old systems
- antivirus on the hardware systems, as they used a trojan to gain access. 
- Could check for the swift ack msg which was missing from the protocol.


---



## Guest Lecture: The Computer Fraud and Abuse ACT
### by Andy Sellars (BU/MIT Technology & Cyberlaw Clinic)


#### Intro To Andy Sellars
- Teacher at BU's Law School
- Runs Technology & Cyberlaw Clinic
	- Goal is to protecting inovation by supporting student research and discovery


#### Computer Fraud and abuse Act (CFAA) is the specific law that we will cover this lecture.
- Came into affect in 1982/83
- The movie WarGames spawned the CFAA as seen on articles from congress, as it raised concerns to everyday citizens.
- CFAA draws on several areas of the law
	* "Theft" crimes
	* "Fraud" crimes
		* pretending to be someone else
	* "Trespass" civil torts
	* Being somewhere you are not suppose to be

- Question asked is it theft if its copying?
 - Answer: there was an early case in the 1970's where an employee copied data and started a competing company. Claimed it wasn't stealing. Turns out court decided it is.

- There is an interesting case where ebay was claiming that another company was violating them because there electrons where crossing their network

### The CFA Today:
##### 18 U.S.C. 1030(a)

For the full legal code: https://www.law.cornell.edu/uscode/text/18/1030

#### In detail important 7 section:
1. Access a computer without authorization or exceeding authorized acess and obtain classified or atomic energy information, with reason to believe that information could be used to injure the United States.
2. Access a computer without authorization or exceeding authorized access, and obtain "information from any protected computer"
3. Access without authorization any nonpublic computer of an agency of the United States Gov.
4. With intern to defraud, access a computer without authorization exceeding authorized access, and by doing so further the intended fraud and obtain a thing of value
5. 
	* (A.) Knowingly cause transmission of a program, and intentionally cause damage.
	* (B.) Intentionally access computer without authorization, and as a result, recklessly cause damage.
	* (C.) Intentionally access a computer without authorization and as a result cause damage and loss.
6. Trafficking in passwords through which a computer may be accessed without authorization
7. With an intent to extort, transmit a threat to cause damage to a computer or obtain information from a computer without authorization.

#### Quick summary:
1. The obtaining classified/atomic energy information one
2. The "obtaining information" one
3. The access to nonpublic fed. computers one
4. The "fraud, but with computers" one
5. The three "damage" crimes
6. Password Trafficking
7. The "extortion, but with computers one
	* Ransomware is an example of this one

Where students tend to get in trouble often are in the following three sections:

* (2) The "obtaining information" one
* (4) The "fraud, but with computers" one
* (5) The three "damage" crimes

#### Mens Rea
Intentions in law are very important. lawyers call this **Mens Rea** which is the intention behind things.

* "Intentionally" - The outcome is why you are doing it
* "Knowingly" - You are aware that this will be the outcome of your actions
* "Recklessly" - you consciously disregard the risk that this will happen because of your actions
* "Negligently" - a reasonable person would have known that this would happen because of your actions
* "strict liability" - doesn't matter what you intended you are liable if something bad happens


#### Definitions: 

The definition of a **"Computer"** legally includes the following:

* smart watch is a computer
* smart phone is a computer
* smart tv is a computer
* Full definition:
	- The term "computer" means an electronic, magenetic, optical, electrochemical, or other high speed data processing device performing logical, arithmetic or storage functions, and includes any data storage facility or communications facility directyl related to or operating in conjunction with such device, but such term does not include an automated typewriter or typesetter, a portable hand held calculator, or any other similar device;

The definition of **"Protected Computer"** is defined as follows:

1. Exclusively for use of a financial institution or the United States Government or
2. Which is used in or affecting interstate or foreign commerce, or communication, including a computer located outside
	* the reason for this is because of how the constitution limits the power of the federal government.

Side Note:

* There have been cases in Mass. that because a computer was not connected to the internet and want used for financial someone could not be charged with the (CFAA). But Mass. is one of the few states that are lenient on this.



#### Determining authorization:

So what does Authorization mean exactly:

- it is the most important to understand
- it is also very debated


**"exceeds authorized access"**
- the definition is lacking

#### Exercises in determining authorization:

- You are at work, and you check the march madness scores on espn.com are you authorized to do so?
	- Most people say no, because your not forbidden to go to this website a.
	- we could look to employee manuals or network policies you agreed to.
	- Vote in class people guessed 50/50 that this is authorized or unauthorized

- What if at your job you had access to a database of peoples personal information. It is authorized to check your friends and neighbors to do so?
	- most people think this is unauthorized

- Lets say I have access to a shared drive or private storage. I decide to store a bunch of pictures that I lawfully own on this drive. Is this unauthorized?
	- if the space is not needed is it causing damage?
	
- Lets say my company has a list of clients. My friend is starting a company, can an employee share this with a the friend to have a list of potential customers?


These are all examples taken from social science text that measured computer norms.
	- asks if access was authorized, is it blameworthy, what should the punishment should be.


##### Interesting notes:
* Nosal case, things like checking espn at work should not be a felony.
authorization should only matter for code based authorization for the act.
* An interesting note, since you visit a website with a terms of service courts are not entirely set on whether if you violate the terms of service if you are not authorized to acess the website.
*	how about scraping, and theres a captcha, are you authorized to bypass it?
	- courts are fighting over this right now.
* CFAA can be a crime but it can also be a civil lawsuit which you would have to prove that it caused a loss of $5,000 dollars or more.
* Early case with Morris says that jury can interperete what authorization means because it is so vague
	* Morris teaches at MIT, but is also the first person to put a worm on the internet




#### If you are doing a project where you could potentially be violating this law feel free to contact Andy.




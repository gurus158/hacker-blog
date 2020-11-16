Last year I organized a **Capture The Flag competition** called **CBMCTF** which was successful. More than 500 teams all over the world registered and around 100 teams successfully submitted at-least 1 flag. 

It was 24 hrs of high traffic monitoring, maintaining challenge servers  ,solving queries of participants and many more fun. I learn a lot form  this experience and want to share.

If you are a tech person and ever tried to learn computer hacking , you  might already aware of CTF competitions. CTF competitions  are great way to start learning hacking for beginners or  to sharp hacking skills for professionals. 

These competitions are like simulations of real world hacking but in secure  way. Participants are given real hacking problems  which includes  finding bugs in  websites, reverse engineering some computer application and many more. The main goal of participants are to find flags , hence called Capture The  Flag, by exploiting and find bugs in given challenges in a certain ways  for which those challenges are designed for.

**Almost every big company like Google, Facebook, Microsoft and others regularly organize CTF competitions to hire hackers.** 

I am a regular CTF player for the last 3 years and love to solve  challenges given in these competitions. I remember those long and  frustrating nights when solving these challenges. 

However My team have finished at Top 10 in many CTF competitions organized by companies and  universities all over the world. 

When you compete for this long , you start thinking about how these  competitions are organized and  what it takes to organize one by own.

I started gathering information and requirements to host a successful CTF competition which break down as follows:

1. **Types of CTF**

There are two types of CTF : a) **Jeopardy style** b) **Attack and Defense** 

Jeopardy includes  directly hand-over of the challenges to participants through a web application, mostly a websites, which they have to solve to find  flag and submit it to score. 

However in Attack-Defense style , participants are given a server (virtual  machine) with some bugs in it. Their goal is to find those bugs and  exploit them in competitors server while patching them on their own.  That is why it is called Attack-Defense. 

**I organized the Jeopardy style CTF.**

2. **A Website for the Competition**

I also required A website from where teams can registered and login to  find and solve challenges. After spending 3-4 days and about 20 hrs of  coding I finally created a web-application for this purpose. 

**Website include signup/login system, challenges, flag submission , scoreboard and details about rules and prizes**.

3. **Challenges to solve**

   As it was a competition , the main requirement was good and interested  challenges. This part took most of the time of the whole process . I had to read latest security generals , bug-bounty programs, new  vulnerabilities and their exploits and a lot of research to create  quality challenges. 

I not only have to create challenges but also step to step solutions of  each problem. Problems should not be too obvious and too complex at the  same time. 

My goal was to create challenges so that no team can solve all but every  team can solve at-least one. After around 3 months I finally designed 28 problems in 7 different categories **Reverse engineering, Crypto, Forensic , web , Misc, pwn and scripting.**

4. **Cloud server for hosting the website**

Now I have basic requirement for the competition . Only things left were a  cloud server to host the website and platform where I can share  information about this competition so that teams can visit register. 

For the server i used **Google Cloud Platform** as it provides free hosting of around 300 USD for one year and I only  need to host website for 15-20 days. After hosting the website in GCP, I shared the information about the competition on **ctftime.org** website which is actually made for only this purpose. 

5. **Chat server for participant to contact organizers**

I also need a platform where participants can reach me during competition and ask any queries. And also I may had to release some hints in case  participants struggling for a challenge. 

For that purpose I used **Discord app** and created a server in it. I also made challenge which include talking to  discord bot programmatically which was one of my favorite challenge. 

With in 2 days teams started registering to my website and with in a week I  already have 200 teams registered. As this was a hacking competition and participants are hackers , many of them start DDOS attack to my server  and they tried to pass payloads in username and password fields which i  didn't expected. 

However the competition is not yet started but I was amazed by looking at the  activities on server. I was now aware that when competition would start , these issues will rise. 

So I need to fix this as soon as possible. After spending whole day I  finally found some tools and technique to identify the DDOS attacks  (which was main problem at that time). I also used cloudflare which help to reduce DDOS and patched some minor bugs on platform itself. And  finally the competition was online as planned. 

It almost took 4 months of hard work to successfully host that CTF and  experience was priceless. Participants gave very good reviews (also some bad reviews) which was worthy payback. I feel very proud when i search  CBMCTF on Google search and saw links to blogs written by participants  about solutions to the problems I designed . 

I get amazed by their solution which even I didn't thought.  Now  every-time I solved a challenge in other CTF , I always write about my  solution because I'm sure the creator of that challenge feel the same as i felt for mine.

Now when I look back, I realized I learned to develop a complete website,  security challenges, read many research papers(which I usually didn't),  handling DDOS, and fixing things on live servers only from this single  event. 



This change my way of working and helped me develop some of my key skills in the industry. The most important thing  was I delivered what I thought  and planned.  It was a great experience for me.

So if you are a person with a thought or idea which you want to implement, my advice is not to delay and to start making it a reality from a  thought or idea. 

**May be you  fail or get success but the prize you get from trying is priceless.**   
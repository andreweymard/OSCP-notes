Experience of a Brazilian: Certified Junior Associate in Cybersecurity (HTB CJCA)
Rodrigo Kawase
10 min read
Nov 7, 2025

Topic 1 - Previous Experiences

I made sure to include as much of this report as possible in Brazilian Portuguese. Those of us here know how difficult and tedious it is to search for and translate content in another language. I would like to have support for our language for Hack The Box content. There is a growing community here that appreciates its materials.

This is my second year studying cybersecurity. In my first year, I studied the content of Desec Security and its 2 experience projects.

I have been studying the Hack The Box modules since March 2025, as I envisioned having enough knowledge to take one of the platform's exams: CWES (~70%), CPTS (~70%) or CDSA (~40%); and retake the DCPT exam. In addition to having obtained the CRTA from CyberWarfare Labs.

I collected some free cubes and was doing the modules that cost 10 cubes, I got a monthly subscription to get 200 cubes per month and did everything from the fundamentals to the possible ones within each path.

It so happened that in the meantime the platform launched the CJCA certification and, having done many fundamental modules, I already had about 70% of the path completed.

Since it was an entry-level exam, it interested me.

I had just taken a break from studying security and then came the promotion along with the certification where, by getting the annual silver plan, I received a voucher for this specific exam and another to be used on the others I mentioned.

The conversion came to around R$ 2100.00. For me it was an opportunity and I decided to resume my studies.

Topic 2 - About the Exam
Press enter or click to view image in full size
https://www.hackthebox.com/blog/Start-cybersecurity-with-HTB-CJCA

As shown in the image, there are 20 modules. All focus on fundamental skills ranging from networking to SIEM and Nmap, for example.

I found the proposal interesting and even unique in the certification market. After seeing some analyses and information about the exam, it seemed to me that it had a similar level to the eJPT (red) and BTL 1 (blue) but without going into as much depth in each area, even though I haven't taken those.

I would recommend the exam for those who are beginners in cybersecurity and don't know if they want to attack or defend. For those who are advanced or already have CPTS, CDSA doesn't make much sense. It depends on each individual.

Furthermore, the price of the content is quite accessible compared to some courses and exams around the world. It's worth evaluating the [value of an annual plan + certification + quality + access time + cost of a new attempt] of each platform you're considering.

The exam lasts 5 days and tests your penetration testing skills, security alert analysis, and, before the end of the 5 days, you must submit a report according to the template provided by HTB when starting the exam, i.e., write the report during the exam. You must achieve the minimum points in penetration testing, correctly analyze 70% of the SIEM security alerts, and the report must be of professional quality to pass.

If you are unsuccessful, it is important that you write and submit the report anyway, so that the team can analyze it and give you feedback on what you can improve. This also gives you another chance to retake the exam. If you don't submit the report, you won't have this second chance.

I found this very good, considering that most people have limited resources, both financial and time-related. [Something other platforms should adopt].

https://academy.hackthebox.com/paths/jobrole
Topic 3 - Exam Experience

I reserved my 5 days, anticipating the start of the exam on Wednesday (October 22nd) and finishing on Sunday/Monday.

I took Wednesday and Thursday off work to start the exam.

I started at 9:30 AM. On the first day, I tried to do everything I could on the machines, enumerating as much as possible to gather all the information. I got the first few points. I felt a bit desperate because it reminded me of the DCPT.

Then on the second day, I realized I didn't need to rush to finish the exam in 24 hours. I revisited my notes, what I could improve, which tool could work in each scenario. After some valuable insights, in the morning I got a few more points and by the end of the day I reached the minimum points for approval.

I couldn't believe it. One part was completed. But still, I stayed up all night trying to explore the last target. I went to sleep at 5 AM.

The third day was long. I started transferring everything I had noted in Joplin about each machine to my report. I added the complete commands with results, printouts, and everything else. I adjusted the SysReptor, added a printout, checked how it looked, and repeated this cycle endlessly. From the moment I started making the report, my windows had this layout:
Press enter or click to view image in full size
All the information that appears is from the pure models; there are no responses.
In the first window I had Joplin, where I added EVERYTHING IN DETAIL that I found alongside the test, without worrying about formatting, since it's also Markdown.

In the middle window was the SysReptor editing page, to edit, paste text and images into the report's skeleton, and a small preview of the resulting Markdown.

In the third window was the SysReptor Publish page, because after each major modification I pressed Refresh PDF to reload my report with the information I had added and see its final layout.

And so the report was created, extending into another night [...] until 9 am on the fourth day (Saturday). I woke up around 1 pm, did some housework, and resumed the test at 5 pm. From this point on, the penetration testing part was already fully documented in the report format. My concern was to start searching for alerts and classifying them.

It's important to understand what a timestamp is. I wasted some time analyzing and classifying events with the wrong timestamp.

I started to understand (some things you'll only understand during the exam), and after that, the agility in searching for events, terms, and commands helped me be more precise in my task. Another sleepless night doing these tasks and playing around in SysReptor, looking at the preview, hunting for another event… I went to sleep at 11 am. I don't recommend it; I couldn't even remember a simple command because I was so sleepy.

After showering [important] and sleeping [also important], I woke up at 3 pm on the fifth day. I had some time because my exam wouldn't end until 9:30 am on Monday. Up to this point, I had completed the report with all the screenshots, commands, classified alerts, screenshots of the evidence, and their respective links. The remaining time was manageable, and I tried again to hack the last host to get 100% of the points. I couldn't, unfortunately. The host I tried the most, but I missed seeing some small detail. So, around 10:20 PM, I added what I had found, including from the last host, generated the report which ended up being 129 pages long, and sent it.

After about 10 business days of pressing F5 on the keyboard, the following email response arrived:

Result email

Finally, relief:

CJCA Certificate

My opinion after taking the exam

I thought the whole experience was very good. It gave me a broader view of security after learning fundamental topics from the blue team.

The exam blends the exploration of a system and the verification of how it appears as an alert in a SIEM, in this case Elastic.

It made me realize that for those aspiring to enter the practical field, knowing how to perform both penetration tests and log analysis gives them a different perspective on the two areas. Whether you like it or not, you will learn in practice how an attacker and a security analyst think.

This certification certainly fulfills that function without you having to pay for introductory certifications that cost over US$200.00 each.

I believe it's very worthwhile. Starting with the CJCA, you'll have a background that will support your choices of certifications in penetration testing, whether you're on the red team or the blue team. This applies not only to the HTB platform itself, but to any other platform as well.

I confess that I'm looking more closely at blue team certifications, something I hadn't considered before.

Topic 4 - Preparation and Execution Tips

For my Parrot OS VM, I created a workspace for each machine, and I even made a tutorial on how to customize Parrot OS with KDE for penetration testing: https://www.linkedin.com/posts/rodrigo-tob-kawase_customiza%C3%A7%C3%A3o-do-parrot-os-em-ambiente-kde-activity-7312706217225682944-H8fG?utm_source=share&utm_medium=member_desktop&rcm=ACoAADGkvIkB4viTClsNSbnDXKjBOeOdSTQ87Hk. It has a few bugs, but it serves to give a general idea. So I'll make a "2.0".

No time, brother.

You'll see that you'll need about 3 terminals (tabs or windows) per machine, besides the browser window. This separation should also apply to creating folders for each machine, notes, in my case Joplin, for each machine, including for the security event analysis environment.

These separations kept me focused and I knew exactly what to look for and where to look.

Get Rodrigo Kawase’s stories in your inbox

Join Medium for free to get updates from this writer.

Another important point: for every command that returns a result, I strongly advise saving the output to a text file. For example: if you run the nmap command, take all the results, including your username and machine name, and save them to this file. Do this for every important command, and it will save you time and you won't need to run the command again.

For the analysis part, when you think a piece of evidence is from a particular event, copy the link that Elastic customizes in the navigation bar. Trust me, you'll look for the same visualization several times.
To be sure.

For those who are starting out, all modules are important, but I need to highlight my personal list (the order doesn't matter):

Fundamentals

Penetration Testing Process: off the schedule, but I found it [extremely important] for structuring the steps of a penetration test. It ended up being my reference for reorganizing my notes.

Introduction to Web Applications

Web Requests

Linux Fundamentals

Windows Fundamentals

Offensive

Footprinting

Hacking WordPress

Pentest in a Nutshell

Defensive

Introduction to Threat Hunting & Hunting With Elastic

Security Monitoring & SIEM Fundamentals

Incident Handling Process

Reporting

Documentation & Reporting: off the schedule, to get an idea of \u200b\u200bwhat is important when documenting a penetration test.

Security Incident Reporting: off the schedule, to get an idea of \u200b\u200bwhat is important when documenting a security incident.

For the report, I used SysReptor (https://github.com/Syslifters/HackTheBox-Reporting), a system recommended by Hack The Box itself. This GitHub account has report templates for all their certifications; you just need to spend an hour or two exploring to get familiar with it. I installed it in a local container and accessed it through the browser.

External Resources for Practice

It's important to emphasize, and this is strongly reinforced by the creators' live stream, that all the content of the CJCA path is sufficient for its execution.

But because of the fear I had of failing the exam and having already had this experience, I preferred to prepare and practice a little more. After all, spending another 5 days or more, around R$ 564.00, isn't funny, especially for someone with a family to take care of.

Because I have an annual subscription to THM and recently got an HTB subscription due to CPTS and CWES, I used the following rooms and boxes for my preparation:

*The THM ones follow this order because it's progressive in learning and difficulty. The WordPress machine is very similar to MetaTwo.*
TryHackMe

[BLUE] Investigating With ELK 101: https://tryhackme.com/room/investigatingwithelk101

[BLUE] Slingshot: https://tryhackme.com/room/slingshot

[BLUE] ItsyBitsy: https://tryhackme.com/room/itsybitsy

[BLUE] Hunt Me I: https://tryhackme.com/room/paymentcollectors

[BLUE] Hunt Me II: https://tryhackme.com/room/typosquatters

[BLUE] Boogeyman 3: https://tryhackme.com/room/boogeyman3

[RED] Wordpress: CVE-2021\u201329447: https://tryhackme.com/room/wordpresscve202129447

Hack The Box

[RED] Active: https://app.hackthebox.com/machines/148

[RED] MetaTwo: https://app.hackthebox.com/machines/504

Again, I used these add-ons to practice different scenarios, mainly when using Elastic. I can guarantee that doing them forced me to understand the security alert tool better, which gave me a lot of agility during the exam.

These machines don't train you on a specific point of the exam or give you any answers, they just made me much more familiar with it and gave me confidence.

If you can't get these extra subscriptions [it's not necessary], you can redo the exercises for each module (I did all the exercises from the sessions and Skills Assessments for each module at least twice, once learning and once for real). But honestly, I would do it up to 3 times to solidify the learning.

Topic 5 - Reference Materials

I would like to express my gratitude to the creators of the following materials I found online while researching a new exam that was rarely discussed:

A Thai person's account of the CJCA: Your First Definitive Guide and Review on HackTheBox CJCA Certification Exam | by Chicken0248 | Aug, 2025 | System Weakness
Account from a Chinese person about the CJCA: https://manesec.github.io/2025/10/05/2025/68-hackthebox-academy-cjca/
Comparison made by this LinkedIn profile: https://www.linkedin.com/feed/update/urn:li:activity:7353848417137152005?updateEntityUrn=urn%3Ali%3Afs_updateV2%3A%28urn%3Ali%3Aactivity%3A7353848417137152005%2CFEED_DETAIL%2CEMPTY%2CDEFAULT%2Cfalse%29
Analysis of some 2025 certifications: How to use SysReptor for the CPTS: https://dollarboysushil.com/posts/cpts-report-writing-guide/

Live with the creators of the CJCA: https://www.youtube.com/live/HyXu4NM3BtU?si=9FmWzBYA_YQVq13S

How to take notes for the CPTS: https://youtu.be/7LU6m_CF3cQ?si=HunL3PlX7BTInpy9

How to organize study notes: https://www.brunorochamoura.com/posts/cpts-tips

Training machines for the blue team: https://github.com/ChickenLoner/Awesome-Splunk-and-Elastic-SIEM-Practice-Labs

Special thanks to my wife for her patience and care.

Nam-Myoho-Renge-Kyo
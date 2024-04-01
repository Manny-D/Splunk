# Splunk

![dummy](https://github.com/Manny-D/Splunk/assets/99146530/6a072a1c-7c98-46f7-8f12-887eee48f1a4)

## Description 
This Project is based on version 2 of the 2017 Boss of the SOC (BOTS) competition by Splunk. 

The BOTSv2 Dataset utilized was generated in August of 2017 by members of Splunk's Security Specialist team.

In this exercise, we have assumed the persona of Alice Bluebird, an analyst who was recommended to Grace Hoppy at Frothly (a beer company) to assist them with their recent issues.


## Questions: 100 Series

1: Amber Turing was hoping for Frothly to be acquired by a potential competitor which fell through, but visited their website to find contact information for their executive team. What is the website domain that she visited?

Thoughts: 
We could initially use the following command <b><i>index="botsv2" amber</i></b> to find Amber, but over 56k events are returned. 
![Screenshot 2024-04-01 at 11 06 16 AM](https://github.com/Manny-D/Splunk/assets/99146530/4d7daada-e978-484e-8801-d1b2fd374f38)

To try and reduce the events and find Amber's IP, we can use the following: <br>
<b><i>index="botsv2" sourcetype="pan:traffic" src_user="frothly\\amber.turing"</i></b> <br>
and when viewing the INTERESTING FIELD src or src_ip, we get <b><i>10.0.2.101</i></b>.

We can use the follwing search query to only return the site field while removing duplicate entries as well as displaying the results in a table: <br>
<b><i>index=botsv2 10.0.2.101 sourcetype="stream:http" * beer * | dedup site | table site</i></b>

![Screenshot 2024-04-01 at 11 24 50 AM](https://github.com/Manny-D/Splunk/assets/99146530/ef3fc55e-92a5-40b9-8e4b-b7f72804e535)

Answer: www.berkbeer.com


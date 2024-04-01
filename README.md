# Splunk

![splunk-botsv2-2017](https://github.com/Manny-D/Splunk/assets/99146530/9033aa06-a882-4708-b183-214922ad8090)

## Description 
This Project is based on version 2 of the 2017 Boss of the SOC (BOTS) competition by Splunk. In it, we will assume the persona of Alice Bluebird, an analyst who was recommended to Grace Hoppy at the Frothly beer company, to assist them with their recent issues.

<b>Note:</b> the BOTSv2 Dataset utilized was generated in August of 2017 by members of Splunk's Security Specialist team.

<br>

## Questions: 100 Series

### <b>Question 1:</b> <br>
Amber Turing was hoping for Frothly to be acquired by a potential competitor which fell through, but visited their website to find contact information for their executive team. What is the website domain that she visited?

<b>Thoughts:</b> <br>
We could initially use the following command <b><i>index="botsv2" amber</i></b> to find Amber, but over 56k events are returned. 
![Screenshot 2024-04-01 at 11 06 16 AM](https://github.com/Manny-D/Splunk/assets/99146530/4d7daada-e978-484e-8801-d1b2fd374f38)

To try and reduce the events and find Amber's IP, we can use the following: <br>
<b><i>index="botsv2" sourcetype="pan:traffic" src_user="frothly\\amber.turing"</i></b> <br>
and when viewing the INTERESTING FIELD <b><i>src</i></b> or <b><i>src_ip</i></b>, we get <b><i>10.0.2.101</i></b>.

We can use the follwing search query to only return the site field while removing duplicate entries as well as displaying the results in a table: <br>
<b><i>index=botsv2 10.0.2.101 sourcetype="stream:http" * beer * | dedup site | table site</i></b>

![Screenshot 2024-04-01 at 11 24 50 AM](https://github.com/Manny-D/Splunk/assets/99146530/ef3fc55e-92a5-40b9-8e4b-b7f72804e535)

<b>Answer:</b> <br>
www.berkbeer.com

<br>

### <b>Question 2:</b> <br>
Amber found the executive contact information and sent him an email. What image file displayed the executive's contact information?

<b>Thoughts:</b> <br>
We can modify the query used in question 1, by adding the website we found (www.berkbeer.com), then parse through the results: <br>
<b><i>index=botsv2 10.0.2.101 sourcetype="stream:http" www.berkbeer.com</i></b> <br>
![Screenshot 2024-04-01 at 11 53 49 AM](https://github.com/Manny-D/Splunk/assets/99146530/69a3fbe3-7b7e-4ed9-84ee-ff72235b9c0a)

It returns 12 events and by using the Search feature in the browser (using the word images), you can see one that stands out: <br>
![Screenshot 2024-04-01 at 11 43 41 AM](https://github.com/Manny-D/Splunk/assets/99146530/03899f59-10cb-4fc2-b8d0-ecb7f2623232)

<b>Answer:</b> <br>
ceoberk.png

<br>

### <b>Question 3:</b> <br>
What is the CEO's name?

<b>Thoughts:</b> <br>
We can surmize their name contains Berk. Whether that's the first or last name is irrelevant at the moment but we have a place to start: <br>
<b><i>index=botsv2 sourcetype="stream:smtp" * berk * </i></b> <br>
This returns 22 events! <br>

When looking throught the results, it looks like we can focus on the 4th event due to the following email: <br>
![Screenshot 2024-04-01 at 1 02 25 PM](https://github.com/Manny-D/Splunk/assets/99146530/998b1bdf-b7df-42e0-845d-ab93d225d66a) <br>

If we expand the <b><i>content_body</i></b> field, we can see the CEO's full name is: <br>
![Screenshot 2024-04-01 at 1 01 45 PM](https://github.com/Manny-D/Splunk/assets/99146530/3c0a38c3-6794-403b-b2b5-ba6500a0cba0) <br>
 
<b>Answer:</b> <br>
Martin Berk

<br>

<b>Noteworthy items:</b> <br>
Amber's last name and email address: <br>
Turing | aturing@froth.ly

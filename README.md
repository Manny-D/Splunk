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
![Screenshot 2024-04-01 at 2 07 28 PM](https://github.com/Manny-D/Splunk/assets/99146530/9a528401-e0cc-4f12-b2d6-15b29a47c513) <br>
This returns 22 events! <br>

When going throught the results, we should focus on the 4th event due to the following email: <br>
![Screenshot 2024-04-01 at 1 02 25 PM](https://github.com/Manny-D/Splunk/assets/99146530/998b1bdf-b7df-42e0-845d-ab93d225d66a) <br>

If we expand the <b><i>content_body</i></b> field, we can locate the CEO's full name: <br>
![Screenshot 2024-04-01 at 1 01 45 PM](https://github.com/Manny-D/Splunk/assets/99146530/3c0a38c3-6794-403b-b2b5-ba6500a0cba0) <br>
 
<b>Answer:</b> <br>
Martin Berk

<br>

<b>Noteworthy items:</b> <br>
Amber's last name and email address: <br>
Turing | aturing@froth.ly

<br>

### <b>Question 4:</b> <br>
What is the CEO's email address?

<b>Thoughts:</b> <br>
See the second screenshot in Question 3. 

<b>Answer:</b> <br>
mberk@berkbeer.com

<br>

### <b>Question 5:</b> <br>
After the initial contact with the CEO, Amber contacted another employee at this competitor. What is that employee's email address?

<b>Thoughts:</b> <br>
Using the same query / events from question 3, the email is listed in the second and third events. <br>
![Screenshot 2024-04-01 at 2 11 51 PM](https://github.com/Manny-D/Splunk/assets/99146530/44154068-4669-4ef9-a244-500588994b01) <br>


<b>Answer:</b> <br>
hbernhard@berkbeer.com

<br>

### <b>Question 6:</b> <br>
What is the name of the file attachment that Amber sent to a contact at the competitor?

<b>Thoughts:</b> <br>
Again, using the same query / events from question 3 we can answer question 6 and 7. <br>
The very first event seems to indicate an attachment. If we expand the <b><i>attach_filename</i></b> field, we expose it's name: <br>
![Screenshot 2024-04-01 at 1 45 35 PM](https://github.com/Manny-D/Splunk/assets/99146530/c03f7e3b-5301-4164-a672-b119a965bc75) <br>

<b>Answer:</b> <br>
Saccharomyces_cerevisiae_patent.docx

<br>

### <b>Question 7:</b> <br>
What is Amber's personal email address?

<b>Thoughts:</b> <br>
When looking through the 22 events, only her corporate email address is listed.  <br> 
However, if we re-examine the first event returned and expand the <b><i>content_body</i></b> field, it looks to be gibberish: <br>
![Screenshot 2024-04-01 at 1 51 24 PM](https://github.com/Manny-D/Splunk/assets/99146530/e3767517-5b05-4fcf-be8a-05ca27d60b16) <br>

Upon further investigation, the following field of <b><i>content_transfer_encoding</i></b> indicates that it is encoded in Base64: <br>
![Screenshot 2024-04-01 at 1 55 07 PM](https://github.com/Manny-D/Splunk/assets/99146530/fd22845b-41db-4cb1-abed-a063743e390d) <br>

If we take the gibberish / Base64 encoded text and paste it into [CyberChef](https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true,false)&input=ICBWR2hoYm10eklHWnZjaUIwWVd0cGJtY2dkR2hsSUhScGJXVWdkRzlrWVhrc0lFRnpJR1JwYzJOMWMzTmxaQ0JvWlhKbElHbHpJSFJvClpTQmtiMk4xYldWdWRDQkpJSGRoY3lCeVpXWmxjbkpwYm1jZ2RHOHVJQ0JRY205aVlXSnNlU0JpWlhSMFpYSWdkRzhnZEdGclpTQjAKYUdseklHOW1abXhwYm1VdUlFVnRZV2xzSUcxbElHWnliMjBnYm05M0lHOXVJR0YwSUdGdFltVnljM1JvWldKbGMzUkFlV1ZoYzNScApaV0psWVhOMGFXVXVZMjl0UEcxaGFXeDBienBoYldKbGNuTjBhR1ZpWlhOMFFIbGxZWE4wYVdWaVpXRnpkR2xsTG1OdmJUNE5DZzBLClJuSnZiVG9nYUdKbGNtNW9ZWEprUUdKbGNtdGlaV1Z5TG1OdmJUeHRZV2xzZEc4NmFHSmxjbTVvWVhKa1FHSmxjbXRpWldWeUxtTnYKYlQ0Z1cyMWhhV3gwYnpwb1ltVnlibWhoY21SQVltVnlhMkpsWlhJdVkyOXRYUTBLVTJWdWREb2dSbkpwWkdGNUxDQkJkV2QxYzNRZwpNVEVzSURJd01UY2dPVG93T0NCQlRRMEtWRzg2SUVGdFltVnlJRlIxY21sdVp5QThZWFIxY21sdVowQm1jbTkwYUM1c2VUeHRZV2xzCmRHODZZWFIxY21sdVowQm1jbTkwYUM1c2VUNCtEUXBUZFdKcVpXTjBPaUJJWldsdWVpQkNaWEp1YUdGeVpDQkRiMjUwWVdOMElFbHUKWm05eWJXRjBhVzl1RFFvTkNraGxiR3h2SUVGdFltVnlMQTBLRFFwSGNtVmhkQ0IwWVd4cmFXNW5JSGRwZEdnZ2VXOTFJSFJ2WkdGNQpMQ0JvWlhKbElHbHpJRzE1SUdOdmJuUmhZM1FnYVc1bWIzSnRZWFJwYjI0dUlFUnZJSGx2ZFNCb1lYWmxJR0VnY0dWeWMyOXVZV3dnClpXMWhhV3dnU1NCallXNGdjbVZoWTJnZ2VXOTFJR0YwSUdGeklIZGxiR3cvRFFvTkNsUm9ZVzVySUZsdmRRMEtEUXBJWldsdWVpQkMKWlhKdWFHRnlaQTBLYUdWeWJtaGhjbVJBWW1WeWEySmxaWEl1WTI5dFBHMWhhV3gwYnpwb1pYSnVhR0Z5WkVCaVpYSnJZbVZsY2k1agpiMjArRFFvNE5qVXVPRGc0TGpjMU5qTU5DZzBLCgogICAgIFBHaDBiV3dnZUcxc2JuTTZkajBpZFhKdU9uTmphR1Z0WVhNdGJXbGpjbTl6YjJaMExXTnZiVHAyYld3aUlIaHRiRzV6T204OUluVnkKYmpwelkyaGxiV0Z6TFcxcFkzSnZjMjltZEMxamIyMDZiMlptYVdObE9tOW1abWxqWlNJZ2VHMXNibk02ZHowaWRYSnVPbk5qYUdWdApZWE10YldsamNtOXpiMlowTFdOdmJUcHZabVpwWTJVNmQyOXlaQ0lnZUcxc2JuTTZiVDBpYUhSMGNEb3ZMM05qYUdWdFlYTXViV2xqCmNtOXpiMlowTG1OdmJTOXZabVpwWTJVdk1qQXdOQzh4TWk5dmJXMXNJaUI0Yld4dWN6MGlhSFIwY0RvdkwzZDNkeTUzTXk1dmNtY3YKVkZJdlVrVkRMV2gwYld3ME1DSStEUW84YUdWaFpENE5Danh0WlhSaElHaDBkSEF0WlhGMWFYWTlJa052Ym5SbGJuUXRWSGx3WlNJZwpZMjl1ZEdWdWREMGlkR1Y0ZEM5b2RHMXNPeUJqYUdGeWMyVjBQWFYwWmkwNElqNE5Danh0WlhSaElHNWhiV1U5SWtkbGJtVnlZWFJ2CmNpSWdZMjl1ZEdWdWREMGlUV2xqY205emIyWjBJRmR2Y21RZ01UVWdLR1pwYkhSbGNtVmtJRzFsWkdsMWJTa2lQZzBLUEhOMGVXeGwKUGp3aExTME5DaThxSUVadmJuUWdSR1ZtYVc1cGRHbHZibk1nS2k4TkNrQm1iMjUwTFdaaFkyVU5DZ2w3Wm05dWRDMW1ZVzFwYkhrNgpTR1ZzZG1WMGFXTmhPdzBLQ1hCaGJtOXpaUzB4T2pJZ01URWdOU0EwSURJZ01pQXlJRElnTWlBME8zME5Da0JtYjI1MExXWmhZMlVOCkNnbDdabTl1ZEMxbVlXMXBiSGs2SWtOaGJXSnlhV0VnVFdGMGFDSTdEUW9KY0dGdWIzTmxMVEU2TUNBd0lEQWdNQ0F3SURBZ01DQXcKSURBZ01EdDlEUXBBWm05dWRDMW1ZV05sRFFvSmUyWnZiblF0Wm1GdGFXeDVPa05oYkdsaWNtazdEUW9KY0dGdWIzTmxMVEU2TWlBeApOU0ExSURJZ01pQXlJRFFnTXlBeUlEUTdmUTBLTHlvZ1UzUjViR1VnUkdWbWFXNXBkR2x2Ym5NZ0tpOE5DbkF1VFhOdlRtOXliV0ZzCkxDQnNhUzVOYzI5T2IzSnRZV3dzSUdScGRpNU5jMjlPYjNKdFlXd05DZ2w3YldGeVoybHVPakJwYmpzTkNnbHRZWEpuYVc0dFltOTAKZEc5dE9pNHdNREF4Y0hRN0RRb0pabTl1ZEMxemFYcGxPakV4TGpCd2REc05DZ2xtYjI1MExXWmhiV2xzZVRvaVEyRnNhV0p5YVNJcwpjMkZ1Y3kxelpYSnBaanQ5RFFwaE9teHBibXNzSUhOd1lXNHVUWE52U0hsd1pYSnNhVzVyRFFvSmUyMXpieTF6ZEhsc1pTMXdjbWx2CmNtbDBlVG81T1RzTkNnbGpiMnh2Y2pvak1EVTJNME14T3cwS0NYUmxlSFF0WkdWamIzSmhkR2x2YmpwMWJtUmxjbXhwYm1VN2ZRMEsKWVRwMmFYTnBkR1ZrTENCemNHRnVMazF6YjBoNWNHVnliR2x1YTBadmJHeHZkMlZrRFFvSmUyMXpieTF6ZEhsc1pTMXdjbWx2Y21sMAplVG81T1RzTkNnbGpiMnh2Y2pvak9UVTBSamN5T3cwS0NYUmxlSFF0WkdWamIzSmhkR2x2YmpwMWJtUmxjbXhwYm1VN2ZRMEtjQzV0CmMyOXViM0p0WVd3d0xDQnNhUzV0YzI5dWIzSnRZV3d3TENCa2FYWXViWE52Ym05eWJXRnNNQTBLQ1h0dGMyOHRjM1I1YkdVdGJtRnQKWlRwdGMyOXViM0p0WVd3N0RRb0piWE52TFcxaGNtZHBiaTEwYjNBdFlXeDBPbUYxZEc4N0RRb0piV0Z5WjJsdUxYSnBaMmgwT2pCcApianNOQ2dsdGMyOHRiV0Z5WjJsdUxXSnZkSFJ2YlMxaGJIUTZZWFYwYnpzTkNnbHRZWEpuYVc0dGJHVm1kRG93YVc0N0RRb0pabTl1CmRDMXphWHBsT2pFeExqQndkRHNOQ2dsbWIyNTBMV1poYldsc2VUb2lRMkZzYVdKeWFTSXNjMkZ1Y3kxelpYSnBaanQ5RFFwemNHRnUKTGtWdFlXbHNVM1I1YkdVeE9BMEtDWHR0YzI4dGMzUjViR1V0ZEhsd1pUcHdaWEp6YjI1aGJEc05DZ2xtYjI1MExXWmhiV2xzZVRvaQpRMkZzYVdKeWFTSXNjMkZ1Y3kxelpYSnBaanNOQ2dsamIyeHZjanAzYVc1a2IzZDBaWGgwTzMwTkNuTndZVzR1UlcxaGFXeFRkSGxzClpUSXdEUW9KZTIxemJ5MXpkSGxzWlMxMGVYQmxPbkJsY25OdmJtRnNMWEpsY0d4NU93MEtDV1p2Ym5RdFptRnRhV3g1T2lKRFlXeHAKWW5KcElpeHpZVzV6TFhObGNtbG1PdzBLQ1dOdmJHOXlPbmRwYm1SdmQzUmxlSFE3ZlEwS0xrMXpiME5vY0VSbFptRjFiSFFOQ2dsNwoKICAgICBiWE52TFhOMGVXeGxMWFI1Y0dVNlpYaHdiM0owTFc5dWJIazdEUW9KWm05dWRDMXphWHBsT2pFd0xqQndkRHQ5RFFwQWNHRm5aU0JYCmIzSmtVMlZqZEdsdmJqRU5DZ2w3YzJsNlpUbzRMalZwYmlBeE1TNHdhVzQ3RFFvSmJXRnlaMmx1T2pFdU1HbHVJREV1TUdsdUlERXUKTUdsdUlERXVNR2x1TzMwTkNtUnBkaTVYYjNKa1UyVmpkR2x2YmpFTkNnbDdjR0ZuWlRwWGIzSmtVMlZqZEdsdmJqRTdmUTBLTFMwKwpQQzl6ZEhsc1pUNDhJUzB0VzJsbUlHZDBaU0J0YzI4Z09WMCtQSGh0YkQ0TkNqeHZPbk5vWVhCbFpHVm1ZWFZzZEhNZ2RqcGxlSFE5CkltVmthWFFpSUhOd2FXUnRZWGc5SWpFd01qWWlJQzgrRFFvOEwzaHRiRDQ4SVZ0bGJtUnBabDB0TFQ0OElTMHRXMmxtSUdkMFpTQnQKYzI4Z09WMCtQSGh0YkQ0TkNqeHZPbk5vWVhCbGJHRjViM1YwSUhZNlpYaDBQU0psWkdsMElqNE5Danh2T21sa2JXRndJSFk2WlhoMApQU0psWkdsMElpQmtZWFJoUFNJeElpQXZQZzBLUEM5dk9uTm9ZWEJsYkdGNWIzVjBQand2ZUcxc1Bqd2hXMlZ1WkdsbVhTMHRQZzBLClBDOW9aV0ZrUGcwS1BHSnZaSGtnYkdGdVp6MGlSVTR0VlZNaUlHeHBibXM5SWlNd05UWXpRekVpSUhac2FXNXJQU0lqT1RVMFJqY3kKSWo0TkNqeGthWFlnWTJ4aGMzTTlJbGR2Y21SVFpXTjBhVzl1TVNJK0RRbzhjQ0JqYkdGemN6MGlUWE52VG05eWJXRnNJajVVYUdGdQphM01nWm05eUlIUmhhMmx1WnlCMGFHVWdkR2x0WlNCMGIyUmhlU3dnUVhNZ1pHbHpZM1Z6YzJWa0lHaGxjbVVnYVhNZ2RHaGxJR1J2ClkzVnRaVzUwSUVrZ2QyRnpJSEpsWm1WeWNtbHVaeUIwYnk0bWJtSnpjRHNnVUhKdlltRmliSGtnWW1WMGRHVnlJSFJ2SUhSaGEyVWcKZEdocGN5QnZabVpzYVc1bExpQkZiV0ZwYkNCdFpTQm1jbTl0SUc1dmR5QnZiaUJoZEEwS1BHRWdhSEpsWmowaWJXRnBiSFJ2T21GdApZbVZ5YzNSb1pXSmxjM1JBZVdWaGMzUnBaV0psWVhOMGFXVXVZMjl0SWo1aGJXSmxjbk4wYUdWaVpYTjBRSGxsWVhOMGFXVmlaV0Z6CmRHbGxMbU52YlR3dllUNE5Danh2T25BK1BDOXZPbkErUEM5d1BnMEtQSEFnWTJ4aGMzTTlJazF6YjA1dmNtMWhiQ0krUEc4NmNENG0KYm1KemNEczhMMjg2Y0Q0OEwzQStEUW84WkdsMlBnMEtQR1JwZGlCemRIbHNaVDBpWW05eVpHVnlPbTV2Ym1VN1ltOXlaR1Z5TFhSdgpjRHB6YjJ4cFpDQWpSVEZGTVVVeElERXVNSEIwTzNCaFpHUnBibWM2TXk0d2NIUWdNR2x1SURCcGJpQXdhVzRpUGcwS1BIQWdZMnhoCmMzTTlJazF6YjA1dmNtMWhiQ0krUEdJK1JuSnZiVG84TDJJK0lEeGhJR2h5WldZOUltMWhhV3gwYnpwb1ltVnlibWhoY21SQVltVnkKYTJKbFpYSXVZMjl0SWo1b1ltVnlibWhoY21SQVltVnlhMkpsWlhJdVkyOXRQQzloUGlCYlBHRWdhSEpsWmowaWJXRnBiSFJ2T21oaQoKICAgICBaWEp1YUdGeVpFQmlaWEpyWW1WbGNpNWpiMjBpUG0xaGFXeDBienBvWW1WeWJtaGhjbVJBWW1WeWEySmxaWEl1WTI5dFBDOWhQbDBOCkNqeGljajROQ2p4aVBsTmxiblE2UEM5aVBpQkdjbWxrWVhrc0lFRjFaM1Z6ZENBeE1Td2dNakF4TnlBNU9qQTRJRUZOUEdKeVBnMEsKUEdJK1ZHODZQQzlpUGlCQmJXSmxjaUJVZFhKcGJtY2dKbXgwT3p4aElHaHlaV1k5SW0xaGFXeDBienBoZEhWeWFXNW5RR1p5YjNSbwpMbXg1SWo1aGRIVnlhVzVuUUdaeWIzUm9MbXg1UEM5aFBpWm5kRHM4WW5JK0RRbzhZajVUZFdKcVpXTjBPand2WWo0Z1NHVnBibm9nClFtVnlibWhoY21RZ1EyOXVkR0ZqZENCSmJtWnZjbTFoZEdsdmJqeHZPbkErUEM5dk9uQStQQzl3UGcwS1BDOWthWFkrRFFvOEwyUnAKZGo0TkNqeHdJR05zWVhOelBTSk5jMjlPYjNKdFlXd2lQanh2T25BK0ptNWljM0E3UEM5dk9uQStQQzl3UGcwS1BIQWdZMnhoYzNNOQpJazF6YjA1dmNtMWhiQ0krUEhOd1lXNGdjM1I1YkdVOUltWnZiblF0YzJsNlpUbzVMakJ3ZER0bWIyNTBMV1poYldsc2VUb21jWFZ2CmREdElaV3gyWlhScFkyRW1jWFZ2ZERzc2MyRnVjeTF6WlhKcFppSStTR1ZzYkc4Z1FXMWlaWElzSm01aWMzQTdQRzg2Y0Q0OEwyODYKY0Q0OEwzTndZVzQrUEM5d1BnMEtQR1JwZGo0TkNqeHdJR05zWVhOelBTSk5jMjlPYjNKdFlXd2lQanh6Y0dGdUlITjBlV3hsUFNKbQpiMjUwTFhOcGVtVTZPUzR3Y0hRN1ptOXVkQzFtWVcxcGJIazZKbkYxYjNRN1NHVnNkbVYwYVdOaEpuRjFiM1E3TEhOaGJuTXRjMlZ5CmFXWWlQanh2T25BK0ptNWljM0E3UEM5dk9uQStQQzl6Y0dGdVBqd3ZjRDROQ2p3dlpHbDJQZzBLUEdScGRqNE5Danh3SUdOc1lYTnoKUFNKTmMyOU9iM0p0WVd3aVBqeHpjR0Z1SUhOMGVXeGxQU0ptYjI1MExYTnBlbVU2T1M0d2NIUTdabTl1ZEMxbVlXMXBiSGs2Sm5GMQpiM1E3U0dWc2RtVjBhV05oSm5GMWIzUTdMSE5oYm5NdGMyVnlhV1lpUGtkeVpXRjBJSFJoYkd0cGJtY2dkMmwwYUNCNWIzVWdkRzlrCllYa3NJR2hsY21VZ2FYTWdiWGtnWTI5dWRHRmpkQ0JwYm1admNtMWhkR2x2Ymk0Z1JHOGdlVzkxSUdoaGRtVWdZU0J3WlhKemIyNWgKYkNCbGJXRnBiQ0JKSUdOaGJpQnlaV0ZqYUNCNWIzVWdZWFFnWVhNZ2QyVnNiRDhtYm1KemNEczhienB3UGp3dmJ6cHdQand2YzNCaApiajQ4TDNBK0RRbzhMMlJwZGo0TkNqeGthWFkrRFFvOGNDQmpiR0Z6Y3owaVRYTnZUbTl5YldGc0lqNDhjM0JoYmlCemRIbHNaVDBpClptOXVkQzF6YVhwbE9qa3VNSEIwTzJadmJuUXRabUZ0YVd4NU9pWnhkVzkwTzBobGJIWmxkR2xqWVNaeGRXOTBPeXh6WVc1ekxYTmwKY21sbUlqNDhienB3UGladVluTndPend2Ynpwd1Bqd3ZjM0JoYmo0OEwzQStEUW84TDJScGRqNE5DanhrYVhZK0RRbzhjQ0JqYkdGegpjejBpVFhOdlRtOXliV0ZzSWo0OGMzQmhiaUJ6ZEhsc1pUMGlabTl1ZEMxemFYcGxPamt1TUhCME8yWnZiblF0Wm1GdGFXeDVPaVp4CgogICAgIGRXOTBPMGhsYkhabGRHbGpZU1p4ZFc5ME95eHpZVzV6TFhObGNtbG1JajVVYUdGdWF5QlpiM1U4Ynpwd1Bqd3ZienB3UGp3dmMzQmgKYmo0OEwzQStEUW84TDJScGRqNE5DanhrYVhZK0RRbzhjQ0JqYkdGemN6MGlUWE52VG05eWJXRnNJajQ4YzNCaGJpQnpkSGxzWlQwaQpabTl1ZEMxemFYcGxPamt1TUhCME8yWnZiblF0Wm1GdGFXeDVPaVp4ZFc5ME8waGxiSFpsZEdsallTWnhkVzkwT3l4ellXNXpMWE5sCmNtbG1JajQ4Ynpwd1BpWnVZbk53T3p3dmJ6cHdQand2YzNCaGJqNDhMM0ErRFFvOEwyUnBkajROQ2p4a2FYWStEUW84Y0NCamJHRnoKY3owaVRYTnZUbTl5YldGc0lqNDhjM0JoYmlCemRIbHNaVDBpWm05dWRDMXphWHBsT2prdU1IQjBPMlp2Ym5RdFptRnRhV3g1T2laeApkVzkwTzBobGJIWmxkR2xqWVNaeGRXOTBPeXh6WVc1ekxYTmxjbWxtSWo1SVpXbHVlaUJDWlhKdWFHRnlaRHh2T25BK1BDOXZPbkErClBDOXpjR0Z1UGp3dmNENE5Dand2WkdsMlBnMEtQR1JwZGo0TkNqeHdJR05zWVhOelBTSk5jMjlPYjNKdFlXd2lQanh6Y0dGdUlITjAKZVd4bFBTSm1iMjUwTFhOcGVtVTZPUzR3Y0hRN1ptOXVkQzFtWVcxcGJIazZKbkYxYjNRN1NHVnNkbVYwYVdOaEpuRjFiM1E3TEhOaApibk10YzJWeWFXWWlQanhoSUdoeVpXWTlJbTFoYVd4MGJ6cG9aWEp1YUdGeVpFQmlaWEpyWW1WbGNpNWpiMjBpUG1obGNtNW9ZWEprClFHSmxjbXRpWldWeUxtTnZiVHd2WVQ0OGJ6cHdQand2Ynpwd1Bqd3ZjM0JoYmo0OEwzQStEUW84TDJScGRqNE5DanhrYVhZK0RRbzgKY0NCamJHRnpjejBpVFhOdlRtOXliV0ZzSWo0OGMzQmhiaUJ6ZEhsc1pUMGlabTl1ZEMxemFYcGxPamt1TUhCME8yWnZiblF0Wm1GdAphV3g1T2laeGRXOTBPMGhsYkhabGRHbGpZU1p4ZFc5ME95eHpZVzV6TFhObGNtbG1JajQ0TmpVdU9EZzRMamMxTmpNbWJtSnpjRHM4CmJ6cHdQand2Ynpwd1Bqd3ZjM0JoYmo0OEwzQStEUW84TDJScGRqNE5DanhrYVhZK0RRbzhjQ0JqYkdGemN6MGlUWE52VG05eWJXRnMKSWo0OGMzQmhiaUJ6ZEhsc1pUMGlabTl1ZEMxemFYcGxPamt1TUhCME8yWnZiblF0Wm1GdGFXeDVPaVp4ZFc5ME8waGxiSFpsZEdsagpZU1p4ZFc5ME95eHpZVzV6TFhObGNtbG1JajQ4Ynpwd1BpWnVZbk53T3p3dmJ6cHdQand2YzNCaGJqNDhMM0ErRFFvOEwyUnBkajROCkNqd3ZaR2wyUGcwS1BDOWliMlI1UGcwS1BDOW9kRzFzUGcwSw&oeol=CRLF) using the From Base64 Recipe, we can get her email address in the first lines of the encoded email: <br>
![Screenshot 2024-04-01 at 1 56 30 PM](https://github.com/Manny-D/Splunk/assets/99146530/5ebbfd88-7a48-4809-9598-ccc59756b09e)

<b>Answer:</b> <br>
ambersthebest@yeastiebeastie.com

<br>

## Questions: 400 Series

### Question 1: <br>
A Federal law enforcement agency reports that Taedonggang often spear phishes its victims with zip files that have to be opened with a password. What is the name of the attachment sent to Frothly by a malicious Taedonggang actor?

<b>Thoughts:</b> <br>
From question 3 in the 100 series, we used the following query but it returned over 500k events: <br>
<b><i>index=botsv2 sourcetype="stream:smtp"</i></b> <br>
![Screenshot 2024-04-01 at 2 48 41 PM](https://github.com/Manny-D/Splunk/assets/99146530/d8439f1a-c742-48eb-86a7-59401373e6e7) <br>

We need something that will filter them down. Since zip was mentioned, we can try filtering with that using: <br>
<b><i>index="botsv2" sourcetype="stream:smtp" *.zip</i></b> <br>
![Screenshot 2024-04-01 at 2 52 57 PM](https://github.com/Manny-D/Splunk/assets/99146530/ff4ee76d-e595-496d-b61c-1a1130084a3a) <br>
6 events returned! We can work with these!!

There is one entry in the INTERESTING FIELD section named <b><i>attach_filename</i></b> that looks to be it:
![Screenshot 2024-04-01 at 2 56 02 PM](https://github.com/Manny-D/Splunk/assets/99146530/91df89ad-6e53-415f-88fe-2864fa6f4a96) <br>

<b>Answer:</b> <br>
invoice.zip

<br>

### Question 2: <br>
What is the password to open the zip file?

<b>Thoughts:</b> <br>
This is likely communicated by the text of an email. Of the 6 events, 5 have the <b><i>content_body</i></b> field. <br>
After reviewing each one, it is contained in the 5 event: <br>
![Screenshot 2024-04-01 at 3 02 55 PM](https://github.com/Manny-D/Splunk/assets/99146530/00b36fe2-0aef-4533-aaec-901b0a58f8a4) <br>

<b>Answer:</b> <br>
912345678

<br>

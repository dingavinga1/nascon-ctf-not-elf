# !ELF
Official write-up for a challenge in NasCon CTF '23.

## Challenge
<b>Name</b><br/>
!ELF<br/><br/>
<b>Description</b><br/>
Someone asked me to tell them where this file is usually located. Can you find that out for me? (I need the complete path)<br/><br/>
<b>File</b><br/>
[random](./random)<br/><br/>
<b>Hint</b><br/>
Try enumerating the services in /etc and maybe you'll find magic.

## Solve

#### Identifying the problem
The challenge specifies that this file is "usually located" somewhere. This immedietely makes us think that it's probably a file that exists within Linux/Kali itself.
#### Going through the file
First, we can check what the file is with the command: ```file random```. This gives us the following output:<br/>
```
random: magic text file for file(1) cmd, ASCII text
```
This provides us enough information to tell us that it is a text file and we can output the contents using ```cat```.<br/>
![image](https://user-images.githubusercontent.com/88616338/226199660-bbcacabc-50ef-4a4d-b21b-7ca1b8a942ea.png)
After going through this file, we can see that it maps some file types to their magic bytes. Now, we can go about looking for this file in a few ways...

##### Route 1 (Raw)
First, we need to look for a pseudo-unique identifier in this file. We notice a few names and email addresses, eg.
```
# by Steve McIntyre (stevem@chiark.greenend.org.uk)
# <doj@cubic.org> added title printing on 2003-06-24
```
We can now utilise the ```grep``` command to search our system for a similar string with the command:<br/>
```grep -r doj@cubic.org / 2>/dev/null```<br/><br/>
This gives us the following output:
```
/etc/apache2/magic:# <doj@cubic.org> added title printing on 2003-06-24
```
We notice how grep gives us the location of a file in which that particular string exists and that is ```/etc/apache2/magic```. We can now double-check if the rest of the contents match with that file.<br/>
![image](https://user-images.githubusercontent.com/88616338/226200471-2988e26a-c0ae-459e-b006-cba01463363a.png)
This confirms that both those files are the same, hence giving us the flag ```/etc/apache2/magic```

##### Route 2 (ChatGPT)
Since ChatGPT does not accept large files like this, we can provide it with a few lines.<br/>
![image](https://user-images.githubusercontent.com/88616338/226200721-5d7d72a7-6b2d-4fbd-8729-e39037c97a6a.png)<br/>
40 lines might work...<br/>
![image](https://user-images.githubusercontent.com/88616338/226200928-0016879e-bdea-4f18-b543-ab9983bc62e7.png)
<br/>
Now that we know the file is the ```mod_mime_magic``` file used by <b>Apache</b>, we can ask ChatGPT where the file is usually located.<br/>
![image](https://user-images.githubusercontent.com/88616338/226201023-33c8b573-de04-4e63-80a1-e7a320877f2e.png)
<br/>
We can check both the directories for the file. However, ```/etc/apache2/``` is a more familiar directory to me, so I go ahead and search that directory with the command:<br/>
```ls -la /etc/apache2 | grep magic```<br/>
This returns the following output:
```
-rw-r--r--   1 root root 31063 Aug  8  2020 magic
```
We can confirm that this is our file by printing the top 40 lines. This gives us the flag ```/etc/apache2/magic```.

##### Route 3 (Using the hint)
The hint narrows down the subdirectory in which the file can be found and that is the ```/etc``` directory. It also hints at the name 'magic'. We can utilise the ```find``` command to recursively search for a file called magic in the ```/etc/``` directory.<br/>
```find /etc/ -name magic 2>/dev/null```<br/>
This gives us the following output:
```
/etc/magic
/etc/apache2/magic
```
We can check what each of these files contains.<br/><br/>
<b>/etc/magic</b><br/>
![image](https://user-images.githubusercontent.com/88616338/226203217-7322fe3a-29e6-4da2-9db9-85f940fadb71.png)
<br/>This is definitely not out file as it contains only 2 lines.<br/><br/>
<b>/etc/apache2/magic</b><br/>
![image](https://user-images.githubusercontent.com/88616338/226203315-6c59b7da-785c-442e-bda4-67f3b529aca1.png)
<br/>There we have it!

## Conclusion
Grep is an important utility when it comes to searching for flags or just important information.



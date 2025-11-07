# Firewall Log Review

In this lab, we’ll be diving into a log file from a Cisco ASA firewall using r-base-core for analysis.
And wow… let’s just say these logs aren’t exactly friendly to work with. Fortunately, with a bit of Bash scripting magic, we can turn that chaos into something meaningful.

Let’s get started by firing up a Kali Linux instance, and let’s begin.

### Step 1: Create the directory

``` bash 
mkdir -p /opt/firewall_log
cd /opt/firewall_log
``` 

### Step 2: Install R-base-core (for statistical analysis)

```bash 
sudo apt update
sudo apt install r-base-core
```
<img width="999" height="454" alt="Install " src="https://github.com/user-attachments/assets/47be6ae4-c3a0-4ba6-804b-d12f3eb764e8" />

### The First Look (Brace Yourself)
Let's start investigating. We're going to search for activity from internal IP 192.168.1.6, but we want to exclude traffic to our edge router at 24.230.56.6—that's just routine noise:
```bash 
bashgrep 192.168.1.6 ASA-syslogs.txt | grep -v 24.230.56.6 | less
```

<img width="978" height="611" alt="First-Look" src="https://github.com/user-attachments/assets/15f2760f-fb29-40fa-81ff-4bbb40ea36d9" />

Overwhelming, right? We're staring at a wall of text that would make anyone's eyes glaze over. Don't panic if you feel stuck in the terminal—just hit "q" to escape back to your command prompt.

### Cleaning Up The Mess
Let's refine this chaos into something we can actually work with:

```bash
grep 192.168.1.6 ASA-syslogs.txt | grep -v 24.230.56.6 | grep FIN | cut -d ' ' -f 1,3,4,5,7,8,9,10,11,12,13,14
```
<img width="994" height="186" alt="Cleaning Up" src="https://github.com/user-attachments/assets/fe559f42-d379-4b6e-920a-78af465a58a4" />

What we just did: We filtered for closed connections (FIN flag) and extracted only the specific fields we care about. The cut command with -d ' ' tells it to split on spaces, then we grab columns 1, 3, 4, 5, 7, 8, 9, 10, 11, 12, 13, and 14. Much cleaner output, right?


### Finding The Pattern
Look closely at your output. Notice something? We're seeing connections to two external addresses: 13.107.237.38 and 18.160.185.174. Let's investigate each one separately.
First, let's isolate traffic to 13.107.237.38:

```bash
grep 192.168.1.6 ASA-syslogs.txt | grep -v 24.230.56.6 | grep FIN | grep 13.107.237.38 | cut -d ' ' -f 1,3,4,5,7,8,9,10,11,12,13,14
```
<img width="976" height="78" alt="Pattern" src="https://github.com/user-attachments/assets/d70d3301-9777-46fd-aa77-ee93703cff69" />


Now let's check out connections to 18.160.185.174:
```bash
grep 192.168.1.6 ASA-syslogs.txt | grep -v 24.230.56.6 | grep FIN | grep 18.160.185.174 | cut -d ' ' -f 1,3,4,5,7,8,9,10,11,12,13,14
```
<img width="975" height="178" alt="Partern 1" src="https://github.com/user-attachments/assets/4d885b0c-d26e-43ae-abf8-679c328d560b" />


### The Moment Of Truth
Take a hard look at that last field in your output. See anything repeating? Let's zoom in on just that column:

```bash
grep 192.168.1.6 ASA-syslogs.txt | grep -v 24.230.56.6 | grep FIN | grep 18.160.185.174 | cut -d ' ' -f 14
```
<img width="973" height="201" alt="Moments" src="https://github.com/user-attachments/assets/8f59aa8b-7d7b-40e3-a827-573af7bc84e9" />

We're seeing a pattern, aren't we? Consistent values appearing over and over. That's interesting—potentially very interesting.

### Let The Numbers Tell The Story
Now we're going to run some statistical analysis on that field. This is where we separate the real analysts from the button-clickers:
``` bash
grep 192.168.1.6 ASA-syslogs.txt | grep -v 24.230.56.6 | grep FIN | grep 18.160.185.174 | cut -d ' ' -f 8,14 | tr : ' ' | tr / ' ' | cut -d ' ' -f 4 | Rscript -e 'y <-scan("stdin", quiet=TRUE)' -e 'cat(min(y), max(y), mean(y), sd(y), var(y), sep="\n")'
```
<img width="982" height="155" alt="Numbers" src="https://github.com/user-attachments/assets/4f13322e-d3ec-4bfe-a712-41faa85d6c24" />

 
#### What just happened? 

We extracted specific fields, cleaned up the delimiters (those tr commands swap colons and slashes for spaces), then piped everything into R for statistical analysis. We're calculating:

  ✅ Minimum value: The smallest data transfer size
  ✅ Maximum value: The largest data transfer size
  ✅ Mean: The average
  ✅ Standard deviation: How much variation exists
  ✅ Variance: Another measure of spread

#### What is the goal? 

The goal of this lab is to show us how we can use `grep` and `cut`, and also how we can use our tools to take that data, convert it into information, because right now we have very good information that we can turn into full-on knowledge.

#### Continuous with Labs? 
- [Next Lab]()

#### Would you like to go back to the previous Lab?
- [Previous Lab](https://github.com/Danychr1/The-Cyber-Defense-Playbook-Project/blob/main/Topics/Tools/Labs/DeepBlueCLI/DeepBlueCLI.md)

Dany Christel 👨🏽‍💻👨🏽‍💻👨🏽‍💻

---
layout: post
title: Four useful bash tricks!
tags: [ bash, github, alias, screensharing, macos ]
excerpt_separator: <!--more-->
---

So much to do, so little time. These four bash tricks should help improve that efficiency!

1. Running Bash commands in Subshell
2. Alias to pull/update multiple GitHub projects without changing current directory
3. Bash Repeat a Command x times
4. Open Screen Sharing (macos) from command line

<!--more-->

## 1. Running Bash commands in Subshell

So [what is a subshell](whatissubshell)?
Taken directly from cyberciti.biz:
- Whenever you run a shell script, it creates a new process called subshell and your script will get executed using a subshell.
- A Subshell can be used to do parallel processing.
- If you start another shell on top of your current shell, it can be referred to as a subshell. Type the following command to see subshell value:

```bash
echo $BASH_SUBSHELL
```
OR
```bash
echo "Current shell: $BASH_SUBSHELL"; ( echo "Running du in subshell: $BASH_SUBSHELL" ;cd /tmp; du 2>/tmp/error 1>/tmp/output)
```

- Any commands enclosed within parentheses are run in a subshell.


## 2. Alias to pull/update multiple GitHub projects without changing current directory
### Scenario 1 - Update multiple GitHub projects
I created Bash Alias to update a few GitHub repos in different folders.
Problem: It always ended me in the last folder it went to pull.
Solution: Run alias in subshell by adding `()` around the command.

**Example:**
```bash
alias gitpullall='(echo "gitproject1";
	cd ~/repofolder/gitproject1;git pull;
	echo "Pulling gitproject2";
	cd ~/repofolder/gitproject2;git pull;
	echo "Pulling gitproject3";
	cd ~/repofolder/gitproject3;git pull;
	echo "Pulling gitproject4";
	cd ~/repofolder/gitproject4;git pull;)'
```

**Output:**
```bash
[jon@macos:~]$gitpullall
Pulling gitproject1
Enter passphrase for key 'myproject1privatekey':
Already up to date.
Pulling gitproject2
Enter passphrase for key 'myproject2privatekey':
Already up to date.
Pulling gitproject3
Enter passphrase for key 'myproject3privatekey':
Already up to date.
Pulling gitproject4
Enter passphrase for key 'myproject4privatekey':
Already up to date.
```

**BONUS:** If you're feeling really brave and have your computer on all the time, you can setup a `Cron Job` to run in the dead of night to keep all your projects up to date.

### Scenario 2 - Update multiple GitHub projects
I've updated multiple GitHub projects. Now I need to update all of them instead of going into each folder to update.

**Solution:**

This code updates all files in your project directory and commits with a comment _**"batch update"**_ and pushes the update to GitHub.

```bash
alias gitupdateall='(echo "Updating Project1";
  cd ~/repofolder/gitproject1;
	git add .;
	git commit -m "batch update";
	git push;
	echo "------------------------------------------------------------------------------------";
  echo "Updating Project2";
  cd ~/gitproject2;
  git add .;
  git commit -m "batch update";
  git push;
)'
```

**Output:**

```bash
[jon@macos:~]$gitupdateall
Updating Project1
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
Enter passphrase for key 'myproject1privatekey':
Everything up-to-date
---------------------------------------------
Updating Project2
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
Enter passphrase for key 'myproject2privatekey':
Everything up-to-date
```

### Scenario 3 - Quick update of your current projects

Say you want to quickly update only your current project. Set this up in your `.bashrc` or `.bash_profile` as a bash function.

**What this does:**
1. A `Git Diff`
2. Asks you for validation (if valid press `y`)
  1. Note: if the diff is more than 1 page, at the end you can press `q` to get the validation prompt
3. Pushes changes out to GitHub

```bash
gitquick() {
        git diff;
        echo ""
        echo "Will commit with comment:" $@
        read -p "Is this ok? (y/n) " input
        if [[ $input == 'y' ]]
        then
          git add .;
          git commit -m "$@" ;
          git push ;
        else
          echo "Cancelled"
        fi
}
```

**Syntax:**
`gitquick "<your comment when committing>"`

**Example:**
`gitquick "thisismycomment"`

**Output:**
```bash
[jon@macos:~/mygitrepofolder]gitquick "thisismycomment"
diff --git a/folder/fileupdate.txt b/folder/fileupdate.txt
index h623gsa..a3g2g2 100644
--- a/folder/fileupdate.txt
+++ b/folder/fileupdate.txt
@@ -1,7 +1,7 @@

<-- output ommitted -->

Will commit with comment: thisismycomment
Is this ok? (y/n) y
[master 42hjak7] thisismycomment
 1 file changed, 71 insertions(+), 6 deletions(-)
Enter passphrase for key 'myproject1privatekey':
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 12 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 1.33 KiB | 1.33 MiB/s, done.
Total 4 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), completed with 3 local objects.
To github.com:project/project.git
   7syerh3..42hjak7  master -> master
[jon@macos:~/mygitrepofolder]
```

## 3. Bash Repeat a Command x times

Often I find myself looking to check something several times in a short period of time. I'd send a command, get a result, press up, press enter, and do it several more times until I see a trend in what I'm looking for. This becomes tedious especially when I need to do this several times. Here are 2 quick methods to solving this problem.

_Note:_ I had added in a pause in case the command you run would look like a DOS (Denial of Service) attack. I had accidentally done this by accident and triggered a temporary ban on my IP address. Oops. :P

### Method 1 - Run in single line
**Syntax:**
`for i in {1..<number of iterations>}; do <command>; sleep 0.5; done`

**Example:**
`for i in {1..10}; do echo "asdf"; sleep 0.5; done`
**Output:**
```
[jon@macos:~]$for i in {1..10}; do echo "asdf"; sleep 0.5; done
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
```


### Method 2 - Run as an alias
Add into `.bashrc` (linux) or `.bash_profile` (macos)

```bash
#function run
run() {
    number=$1
    shift
    for i in `seq $number`; do
      $@
	sleep 0.5
    done
}
```
**Syntax** `run <number of times to run> "<command>"`

**Example:** `run 10 "echo 'asdf'"`

**Output:**
```bash
[jon@macos:~]$run 10 "echo 'asdf'"
'asdf'
'asdf'
'asdf'
'asdf'
'asdf'
'asdf'
'asdf'
'asdf'
'asdf'
'asdf'
```

## 4. Open Screen Sharing (macos) from command line

Okok, this isn't really a bash trick, but rather using a bash alias to map to a user/password for a particular host.
_**NOTE:** It is not recommended for you to program your username/password into bash, as it becomes a security risk. I have included it as such for educational purposes._

If you need to enable screen share for the first time, [check out a previous post](enablescreenshare) for steps to enable screen sharing!

### Method 1 - Direct on bash

**Syntax (Safe)** `open vnc://<username>:@<remotehost>` <-- This opens Screen Sharing with username pre-filled and has a password prompt.

**Syntax (NOT Safe)** `open vnc://<username>:<password>@<remotehost>` <-- This opens Screen Sharing without password prompt.


**Example (Safe):** `open vnc://jtsui@192.168.1.10`

**Example (NOT Safe):** `open vnc://jtsui:mysecretpassword@192.168.1.10`



### Method 2 - Run as Alias

Basically the same as running in bash, except you are adding `alias <aliasname>=` and putting the command in single quotes (`' '`)

**Syntax (Safe)** `alias <aliasname>='open vnc://<username>:@<remotehost>'` <-- This has password prompt

**Syntax (NOT Safe)** `alias <aliasname>='open vnc://<username>:<password>@<remotehost>'` <-- No password prompt


**Example (Safe):** `alias remotehostscreenshare='open vnc://jtsui@192.168.1.10'`

**Example (NOT Safe):** `alias remotehostscreenshare='open vnc://jtsui:mysecretpassword@192.168.1.10'`


If you find any errors or have suggestions to improve this article, please feel free to contact Jon at <blog@tekrx.ca>

---

_All applications mentioned in this article are not endorsed by TekRx Solutions and are to be used at your own discretion. TekRx Solutions does not take responsibility for any liability in the use of any application mentioned in the article._



[whatissubshell]: https://bash.cyberciti.biz/guide/What_is_a_Subshell%3F
[enablescreenshare]: https://blog.tekrx.ca/allow-screen-share-macos/

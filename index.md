# Lab 2 Servers and SSH Keys
<div style="page-break-after: always"></div>

## Background and Code

For this lab we created a chat server which has a message board where various users can post messages to the board. The code for the server is below:

```
import java.io.IOException;
import java.net.URI;
import java.util.ArrayList;

class Handler implements URLHandler {
    // The one bit of state on the server: a number that will be manipulated by
    // various requests.
    ArrayList<String> messageBoard = new ArrayList<String>();
    public String handleRequest(URI url) {
        if (url.getPath().equals("/")) {
            String returnString = new String();
            for(int i = 0; i < messageBoard.size();i++){
                returnString = returnString + messageBoard.get(i);
            }
            return String.format("Message Board:\n\n"+ returnString);
        }
        else {
            if (url.getPath().contains("/add-message")) {
                String[] parameters = url.getQuery().split("&");
                String[] message = parameters[0].split("=");
                String[] user = parameters[1].split("=");
                messageBoard.add(user[1] + ": " + message[1] + "\n");
                String returnString = new String();
                for(int i = 0; i < messageBoard.size();i++){
                    returnString = returnString + messageBoard.get(i);
                }
                return String.format("Message Board:\n\n"+ returnString);
            }
            return "404 Not Found!";
        }
    }
}

class ChatServer {
    public static void main(String[] args) throws IOException {
        if(args.length == 0){
            System.out.println("Missing port number! Try any number between 1024 to 49151");
            return;
        }
        int port = Integer.parseInt(args[0]);
        Server.start(port, new Handler());
    }
}
```
## Demonstration of ChatServer
First message being sent:
![alt text](https://github.com/PierceNguyen/cse15l-lab-reports/blob/main/Images/Lab2/addMessage-ex1.png?raw=true)
When going to this url the method handleRequest is called with the url: http://localhost:4000/add-message?s=Hello&user=jpolitz as its parameter. Since the path is /add-message after the host, the else statement gets called which then changes the class variable messageBoard to now include a new string with the format: user: message or in this case jpolitz: Hello. Then the entire Arraylist<String> is printed to the website. The only class variable that changes is the messageBoard variable. 

Second message being sent:
![alt text](https://github.com/PierceNguyen/cse15l-lab-reports/blob/main/Images/Lab2/addMessage-ex2.png?raw=true)
When going to this url the method handleRequest is called with the url: localhost:4000/add-message?s=How%20are%20you&user=yash as its parameter. Since the path is /add-message after the host, the else statement gets called which then changes the class variable messageBoard to now include a new string with the format: user: message or in this case yash: How are you. Then the entire Arraylist<String> is printed to the website. The only class variable that changes is the messageBoard variable. 

## Part 2
```
pninv@PHN-Evo MINGW64 ~/.ssh
$ ls
id_ed25519  id_ed25519.pub  known_hosts

pninv@PHN-Evo MINGW64 ~/.ssh
$ pwd
/c/Users/pninv/.ssh
```

```
pninv@PHN-Evo MINGW64 ~/.ssh
$ ssh phn014@ieng6.ucsd.edu
Last login: Thu Jan 25 14:09:26 2024 from 100.81.32.60
quota: Cannot resolve mountpoint path /home/linux/ieng6/cs120wi24/public/.snapshot/daily.2023-12-28_0010: Stale file handle
Hello phn014, you are currently logged into ieng6-203.ucsd.edu
```

```
[phn014@ieng6-203]:~:40$ cd .ssh
[phn014@ieng6-203]:.ssh:41$ ls
authorized_keys
[phn014@ieng6-203]:.ssh:42$ pwd
/home/linux/ieng6/oce/4m/phn014/.ssh
```

# Lab 1 Remote Access and FileSystem

This week in CSE15L we learned various terminal commands such as ``cd``, ``ls`` and ``cat``. This lab report outlines the three commands and various arguments that can be passed for them below. 

## Command: cd

**Using cd with no commands**

With the root directory as the working directory:
```
[user@sahara ~]$ cd
[user@sahara ~]$
```
Outside the root directory:
```
[user@sahara ~/lecture1]$ cd
[user@sahara ~]$
```
As shown above, using cd with no commands does not result in an error but also does not output anything. If you use cd in the root directory nothing occurs; however, if you use it outside the root directory it changes the working directory to be one layer up.


**Using cd with a directory as an argument**
```
[user@sahara ~]$ cd lecture1
[user@sahara ~/lecture1]$
```
Here we can see that despite cd not outputting anything there was no error either. Originally, we were in the root directory however upon passing a valid directory we were able to change our working directory to be the lecture1 folder. 


**Using cd with a file as an argument**
```
[user@sahara ~/lecture1]$ cd Hello.java
bash: cd: Hello.java: Not a directory
[user@sahara ~/lecture1]$
```
Here we can see that cd did output something this time and it is an error. This is an error since cd can only be used to open directories and cannot be used to open files. 


## Command: ls

**Using ls with no commands**
```
[user@sahara ~/lecture1]$ ls
Hello.class  Hello.java  messages  README
[user@sahara ~/lecture1]$
```
As shown above, using ls with no commands does not result in an error and prints a list of all the files and folders in the working directory to terminal. Additionally folders are colored blue and bolded. 


**Using ls with a directory as an argument**
```
[user@sahara ~/lecture1]$ ls messages
en-us.txt  es-mx.txt  vi.txt  zh-cn.txt
[user@sahara ~/lecture1]$
```
Using ls with a directory as an argument prints a list of all the files and folders in the specified directory to terminal and is not an error.


**Using ls with a file as an argument**
```
[user@sahara ~/lecture1]$ ls Hello.java
Hello.java
[user@sahara ~/lecture1]$
```
Using ls with a file as an argument does not throw an error and prints out the name of the given file. 

## Command: cat

**Using cat with no commands**
```
[user@sahara ~/lecture1]$ cat
prints out what i type
prints out what i type
^C
[user@sahara ~/lecture1]$
```
If no commands are given cat does not throw an error but simply copies what you write in terminal and prints it out in terminal. To get back to the command line you need to hit control + c.


**Using cat with a directory as an argument**
```
[user@sahara ~/lecture1]$ cat messages
cat: messages: Is a directory
[user@sahara ~/lecture1]$
```
Using cat with a directory as an argument throws an error which states that the argument is a directory which implies that directories cannot be used as an argument for cat.


**Using cat with a file as an argument**

With one valid file given:
```
[user@sahara ~/lecture1]$ cat README
To use this program:

javac Hello.java
java Hello messages/en-us.txt
[user@sahara ~/lecture1]$
```

With two valid files given:
```
[user@sahara ~/lecture1]$ cat README Hello.java
To use this program:

javac Hello.java
java Hello messages/en-us.txt
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class Hello {
  public static void main(String[] args) throws IOException {
    String content = Files.readString(Path.of(args[0]), StandardCharsets.UTF_8);    
    System.out.println(content);
  }
}[user@sahara ~/lecture1]$ 
```
Here we can see that using files as arguments for the cat command does not result in an error. Instead the cat command prints the contents of all the given files to terminal. 

# Lab 3 Bugs and Commands

## Background and Code

For this lab we looked at a buggy program and created tests for various functions in the program and fixed some of the functions. 

## Part 1: Bugs

Here I wrote a JUnit test for the reverseInPlace function where the test is meant to fail due to the reverseInPlace function being buggy/incorrect. 
```
//Code that is tested
  static void reverseInPlace(int[] arr) {
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = arr[arr.length - i - 1];
    }
  }

//Code from Testing java file
@Test 
	public void CustomtestReverseInPlaceWrong() {
    int[] input1 = {1,2,3};
    ArrayExamples.reverseInPlace(input1);
    assertArrayEquals(new int[]{3,2,1}, input1);
	}
```

Here I wrote a JUnit test for the reverseInPlace function where the test is meant to pass despite the reverseInPlace function being buggy/incorrect. 
```
//Code that is tested
  static void reverseInPlace(int[] arr) {
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = arr[arr.length - i - 1];
    }
  }

//Code from Testing java file
@Test 
	public void CustomtestReverseInPlaceRight() {
    int[] input1 = {1,2,3,2,1};
    ArrayExamples.reverseInPlace(input1);
    assertArrayEquals(new int[]{1,2,3,2,1}, input1);
	}
```

Here is the output from running both JUnit tests with the 2nd test passing and the first test failing. 
```
$ bash test.sh
JUnit version 4.13.2
..E
Time: 0.015
There was 1 failure:
1) CustomtestReverseInPlaceWrong(ArrayTests)
arrays first differed at element [2]; expected:<1> but was:<3>
        at org.junit.internal.ComparisonCriteria.arrayEquals(ComparisonCriteria.java:78)
        at org.junit.internal.ComparisonCriteria.arrayEquals(ComparisonCriteria.java:28)
        at org.junit.Assert.internalArrayEquals(Assert.java:534)
        at org.junit.Assert.assertArrayEquals(Assert.java:418)
        at org.junit.Assert.assertArrayEquals(Assert.java:429)
        at ArrayTests.CustomtestReverseInPlaceWrong(ArrayTests.java:24)
        ... 30 trimmed
Caused by: java.lang.AssertionError: expected:<1> but was:<3>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at org.junit.internal.ExactComparisonCriteria.assertElementsEqual(ExactComparisonCriteria.java:8)
        at org.junit.internal.ComparisonCriteria.arrayEquals(ComparisonCriteria.java:76)
        ... 36 more

FAILURES!!!
Tests run: 2,  Failures: 1
```

Now that we see our function failing some tests, lets fix the function so it works as intended. 
```
  //buggy function
  static void reverseInPlace(int[] arr) {
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = arr[arr.length - i - 1];
    }
  }
```
```
  //fixed function
  static void reverseInPlace(int[] arr) {
    for(int i = 0; i < arr.length / 2; i += 1) {
      int temp = arr[i];
      arr[i] = arr[arr.length - i - 1];
      arr[arr.length - i - 1] = temp;
    }
  }
```

The original function was wrong since it would overwrite the first half the the array with the 2nd half effectively mirroring the array
around the midpoint. To fix this I just swapped the outer array elements and worked my way to the middle to ensure that none of the array
elements were overwritten before they were swapped. With this fix the function should be able to reverse arrays correctly now. 

## Part 2 - Researching Commands

Now lets explore the grep command in more detail and explore some of the command-line options we can pass to these commands. 



<div style="page-break-after: always"></div>

# Lab 2 Servers and SSH Keys

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

When going to this url the method handleRequest is called with the url: http://localhost:4000/add-message?s=How%20are%20you&user=yash as its parameter. Since the path is /add-message after the host, the else statement gets called which then changes the class variable messageBoard to now include a new string with the format: user: message or in this case yash: How are you. Then the entire Arraylist<String> is printed to the website. The only class variable that changes is the messageBoard variable. 

## Part 2
```
pninv@PHN-Evo MINGW64 /c/users/pninv/.ssh
$ ls /c/users/pninv/.ssh/id_ed25519.pub
/c/users/pninv/.ssh/id_ed25519.pub
```
Here we use ls to see the absolute path of the public key and can see that our public key ends with a .pub. Additionally we can see that our keys absolute path is: /c/Users/pninv/.ssh/id_ed25519.pub. 
```
pninv@PHN-Evo MINGW64 /c/users/pninv/.ssh
$ ssh phn014@ieng6.ucsd.edu
Last login: Tue Apr 16 11:10:45 2024 from 100.81.38.16
Hello phn014, you are currently logged into ieng6-203.ucsd.edu
```
After transferring the key to our remote server we are able to login without entering our password.
```
[phn014@ieng6-203]:.ssh:78$ ls /home/linux/ieng6/oce/4m/phn014/.ssh/authorized_keys
/home/linux/ieng6/oce/4m/phn014/.ssh/authorized_keys
```
Here we use ls to see the absolute path of the authorized keys. Additionally we can see that our keys absolute path is: /home/linux/ieng6/oce/4m/phn014/.ssh/authorized_keys. 

In our week 2 and 3 labs I learned how to connect into remote servers using ssh. I also learned how to generate public and private keys to authenticate my computer so I didn't need to type in my password every time I logged on to the server. 

<div style="page-break-after: always"></div>

# Lab 1 Remote Access and FileSystem

This week in CSE15L we learned various terminal commands such as ``cd``, ``ls`` and ``cat``. This lab report outlines the three commands and various arguments that can be passed for them below. 

## Command: cd

**Using cd with no arguments**

With the root directory as the working directory:
```
[user@sahara /]$ cd 
[user@sahara /]$ 
```
Outside the root directory:
```
[user@sahara ~/lecture1]$ cd 
[user@sahara ~]$ 
```
As shown above, using cd with no arguments does not result in an error but also does not output anything. If you use cd in the root directory the current directory does not change; however, if you use it outside the root directory it changes the working directory to be one layer up.


**Using cd with a directory as an argument**
With the root directory as the working directory:
```
[user@sahara /]$ cd /home/lecture1
[user@sahara ~/lecture1]$ 
```
Here we can see that despite cd not outputting anything there was no error either. Originally, we were in the root directory however upon passing a valid directory we were able to change our working directory to be the lecture1 folder. 


**Using cd with a file as an argument**
With /home/lecture1 as the working directory:
```
[user@sahara ~/lecture1]$ cd Hello.java
bash: cd: Hello.java: Not a directory
[user@sahara ~/lecture1]$
```
Here we can see that cd did output something this time and it is an error. This is an error since cd can only be used to change your current working directory and cannot be used to open files. 


## Command: ls

**Using ls with no arguments**
With /home/lecture1 as the working directory:
```
[user@sahara ~/lecture1]$ ls
Hello.class  Hello.java  messages  README
[user@sahara ~/lecture1]$
```
As shown above, using ls with no arguments does not result in an error and prints a list of all the files and folders in the working directory to terminal.


**Using ls with a directory as an argument**
With /home/lecture1 as the working directory
```
[user@sahara ~/lecture1]$ ls messages
en-us.txt  es-mx.txt  vi.txt  zh-cn.txt
[user@sahara ~/lecture1]$
```
Using ls with a directory as an argument prints a list of all the files and folders in the specified directory to terminal and is not an error.


**Using ls with a file as an argument**
With /home/lecture1 as the working directory
```
[user@sahara ~/lecture1]$ ls Hello.java
Hello.java
[user@sahara ~/lecture1]$
```
Using ls with a file as an argument does not throw an error and prints out the name of the given file. 

## Command: cat

**Using cat with no arguments**
With /home/lecture1 as the working directory
```
[user@sahara ~/lecture1]$ cat
prints out what i type
prints out what i type
^C
[user@sahara ~/lecture1]$
```
If no arguments are given cat does not throw an error but simply copies what you write in terminal and prints it out in terminal. To get back to the command line you need to hit control + c.


**Using cat with a directory as an argument**
With /home/lecture1 as the working directory
```
[user@sahara ~/lecture1]$ cat messages
cat: messages: Is a directory
[user@sahara ~/lecture1]$
```
Using cat with a directory as an argument throws an error which states that the argument is a directory which implies that directories cannot be used as an argument for cat.


**Using cat with a file as an argument**
With /home/lecture1 as the working directory

With one valid file given:
```
[user@sahara ~/lecture1]$ cat README
To use this program:

javac Hello.java
java Hello messages/en-us.txt
[user@sahara ~/lecture1]$
```

With more than one valid file given:
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

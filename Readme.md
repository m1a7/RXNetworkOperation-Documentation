![](Documentation/rxnetworkoperationLogo.png)

# Table of Contents
- [💎 Definition](#definition)
- [⁉️ The reason for the creation of RXNetworkOperation](#the-reason-for-the-creation-of-RXNetworkOperation)
- [👓 Description of Operations](#description-of-operations)
- [💡 Concept](#concept)
- [🍑 Benefits from NSOperation](#benefits-from-nsoperation)
- [🏗 Methods of management of the operation](#methods-of-management-of-operations)
- [🤖 Feedback from the user object](#feedback-from-the-user-object)
- [🚧 Important detail](#important-detail)
- [🍭 LifeHacks](#lifehacks)
- [📖 Deep study](#deep-study)
- [💼 Сode snippets](#code-snippets)
   - [RXDataTaskOperation]()   
	    - [Getting JSON](#getting-json)
	    - [Getting Image](#getting-image)
	    - [Posting JSON](#posting-json)
   - [RXUploadOperation]()
 	    - [Upload By Params (NSDictionary)](#upload-by-params-(nsdictionary))
	    - [Upload By Data (NSData)](#upload-by-data-(nsdata))
	    - [Upload By Local URL (NSURL)](#upload-by-local-url-(nsurl))
   - [RXDownloadOperation]()
  	    - [Downloading Image and writting on disk](#downloading-image-and-writting-on-disk)
- [🗺 Recommendations](#recommendations)

<br><br>


## Definition: 
RXNetworkOperation is an amazing tool that provides several subclasses of NSOperation to perform http requests.
With RXNetworkOperation, you do not need to have your own NetworkLayer to receive data from the network.

![](Documentation/Carbon-Screens/introductionToFramework.png)

---

## The reason for the creation of RXNetworkOperation:


RXNetworkOperation was created in order to be able to make requests to the Internet and not have at the same time a large number of dependencies.

You will no longer need different AFNetworking / Alamofire, you will no longer need to become a delegate of NSURLSession and implement the same methods in each controller.

Just import ```#import <RXNetworkOperation/RXNetworkOperation.h>``` and make requests to the Internet - instantly.
And also You will feel the full power over network operations. You can stop and start again. 
<br>

![](Documentation/Carbon-Screens/reasonOfCreatingFramework.png)

## Description of operations:
You are provided with a set of three classes RXDownloadOperation, RXUploadOperation and RXDataTaskOperation.

1. ```DO (RXDownloadOperation)``` - downloads data from the server and writes it to the device’s memory.
2. ```UO (RXUploadOperation) ```- uploads the data you provide to the server.
3. ```DTO (RXDataTaskOperation)``` - performs any HTTP requests (GET/POST/...) often used for routine operations (get json).

---


## Concept:

![](Documentation/EN/Concept/NetworkOperationConcept(EN).png)
---
<br>

 
## Benefits from NSOperation:
Since all of our DO/UO/DTO classes are modified inheritors of NSOperation in the class interface, you have the option to suspend/resume/cancel operations.

---


## Methods of management of operations:
1)  ```-(void)start ```     -  launch the task for the first time. (The next time (after suspending) we do not start the task, but resume it). <br>
2) ``` -(void) suspend ```  - pauses a previously running task. <br>
3) ``` -(void) resume ```   - resumes a previously suspended task. <br>
4)  ```-(void) cancel```    - cancels the operation. (After calling this method, you cannot resume execution.) <br>

All operations are created on the principle of  
 >"one object(DO / UO / DTO) - one network task”

 as a result, you can easily control 
the process of performing a task. You can also create as many operations as you need and pass them on to the NSOperationQueue object.

---


## Feedback from the user object:

Each of the operation objects gives you the ability to interact with them through blocks or delegates.
Each case has its own constructors.

Example:

![FeedBack](Documentation/Carbon-Screens/FeedbackFromTheUserObject.png) 



## Important detail:

There are several important properties and methods in the RXBaseOperation .h file.



1)```state ```- is an enum that displays the current state of the operation. 
   The operation can have the following States:

![](Documentation/Carbon-Screens/small-state.png)
![](Documentation/Carbon-Screens/small-enum.png)

2) ```isPrintLogInConsole ``` - if YES then prints all method calls to the console. (Default is NO).

3) ```-(OperationState)startSuspendResume``` -methods that works on the principle of the Play button in the player.
	That is, if the operation is not started - then run. if the work - suspend if you have suspended - resume.

4) ```-(NSString*)stateDescription```  - returns a string describing the status of the operation.

---


## LifeHacks:
To make your syntax more concise, you can use abbreviations.

![](Documentation/Carbon-Screens/typedef.png)



## Deep study: 
If you want to explore the work of the framework in more detail, go to our Wiki. <br><br>
🚧 Attention! The wiki is only available in a private repository.

[🇺🇸 English Wiki](). <br>
[🇷🇺 Russian Wiki](). <br>

<img src="Documentation/collageOfWiki.png" width="600" >

--
<br>

## Сode snippets:

## 1) The RXDataTaskOperation class is used to perform everyday network requests.
###  Getting JSON
 [Copy code](Documentation/TextSnippet/dto-get-json.txt)
![DTO-GET-JSON](Documentation/Carbon-Screens/DTO-GET-JSON.png)

### Getting Image
 [Copy code](Documentation/TextSnippet/dto-get-image.txt)
![DTO-GET-Image](Documentation/Carbon-Screens/DTO-GET-IMAGE.png)

### Posting JSON 
 [Copy code](Documentation/TextSnippet/dto-post-json.txt)
![DTO-POST-JSON](Documentation/Carbon-Screens/DTO-POST-JSON.png)


## 2) The RXUploadOperation Class has three constructors for uploading data to the server

### Upload By Params (NSDictionary)
 [Copy code](Documentation/TextSnippet/upload-nsdictionary.txt)
![](Documentation/Carbon-Screens/Upload-NSDict.png)


### Upload By Data (NSData)
 [Copy code](Documentation/TextSnippet/upload-nsdata.txt)
![](Documentation/Carbon-Screens/Upload-Data.png)

### Upload By Local URL (NSURL)
 [Copy code](Documentation/TextSnippet/upload-nsurl.txt)
![](Documentation/Carbon-Screens/Upload-NSURL.png)

## 3) Example of using RXDownloadOperation 
###  Downloading Image and writting on disk
 [Copy code](Documentation/TextSnippet/download-png.txt)
  
![](Documentation/Carbon-Screens/Download-PNG.png)


## Recommendations
To write files from temporary directories, we recommend using the [FCFileManager](https://github.com/fabiocaccamo/FCFileManager) library.

## Author
👨🏼‍💻 [@m1a7](github.com/m1a7) <br>
👌🏻 thisismymail03@gmail.com

 

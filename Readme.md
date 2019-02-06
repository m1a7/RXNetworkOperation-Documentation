
### Description:
RXNetworkOperation is an amazing tool that provides several subclasses of NSOperation to perform http requests.
With RXNetworkOperation, you do not need to have your own NetworkLayer to receive data from the network.

---

### Description of operations:
You are provided with a set of three classes DownloadOperation, UploadOperation and DataTaskOperation.

1. ```DO (DownloadOperation)``` - downloads data from the server and writes it to the device’s memory.
2. ```UO (UploadOperation) ```- uploads the data you provide to the server.
3. ```DTO (DataTaskOperation)``` - performs any HTTP requests (GET/POST/...) often used for routine operations (get json).

---


### Benefits from NSOperation:
Since all of our DO/UO/DTO classes are modified inheritors of NSOperation in the class interface, you have the option to suspend/resume/cancel operations.

---

### Methods of management of the operation:
1)  ```-(void)start ```         -  launch the task for the first time. (The next time (after suspending) we do not start the task, but resume it).
2) ``` -(void) suspend ```  - pauses a previously running task.
3) ``` -(void) resume ```   - resumes a previously suspended task.
4)  ```-(void) cancel```	 - cancels the operation. (After calling this method, you cannot resume execution.)

All operations are created on the principle of  
 >"one object(DO / UO / DTO) - one network task”

 as a result, you can easily control 
the process of performing a task. You can also create as many operations as you need and pass them on to the NSOperationQueue object.

---

### Feedback from the user object:

Each of the operation objects gives you the ability to interact with them through blocks or delegates.
Each case has its own constructors.

Example:

```objectivec
 [[DownloadOperation GET:url withParams:nil withProgress:^(DownloadOperation* op, DODownProgress p){

       // Here update progress by download
	NSLog(@“Progress: %f”, p.totalBytesWritten/p.totalBytesExpectedToWrite);
  
   } withCompletion:^(DownloadOperation* op, NSError* error){
	
	// Here you should rewrite downloaded file in permanent  directory
	NSLog(@“Downloaded file is located here %@”,  op.locationFile)
   }] start];
``` 


### Important detail:

There are several important properties and methods in the BaseOperation .h file.

1) state - is an enum that displays the current state of the operation. 
   The operation can have the following States:

```objectivec
    BOReadyToStart,     // When the operation is initialized and ready to run
    BOWorking,          // When an operation performs an operation
    
    BOSuccessFinished,  // When the operation is successfully completed
    BOFailiedFinished,  // When the operation is completed with an error
    
    BOCancelled,        // When the operation is canceled
    BOSusspended        // When an operation is suspended
```

2) ```isPrintLogInConsole ``` - if YES then prints all method calls to the console. (Default is NO).

3) ```-(OperationState)startSuspendResume``` -methods that works on the principle of the Play button in the player.
	That is, if the operation is not started - then run. if the work - suspend if you have suspended - resume.

4) ```-(NSString*)stateDescription```  - returns a string describing the status of the operation.

---

### LifeHacks:
To make your syntax more concise, you can use abbreviations.

```objectivec
typedef DownloadOperation NetDownOp;
typedef DownloadOperation DownOp;
typedef DownloadOperation DO;
```

### Deep study: 
If you want to explore the work of the framework in more detail, go to our wiki.

---

### Detailed example:

1) the UploadOperation Class has three constructors for uploading data to the server

#### Upload By Params (NSDictionary)
```objectivec
   // Params-only-flow
    
    NSString* pathToPngToUpload = [[NSBundle mainBundle] pathForResource:@"pngPicture" ofType:@"png"];
    NSData*   pngDataToUpload   = [NSData dataWithContentsOfFile:pathToPngToUpload];
    NSString* pngStrBase64      = [pngDataToUpload base64EncodedStringWithOptions:0];
    
    
     [[UO uploadTo:@"https://httpbin.org/post"
                         withParams:@{@"data" : pngStrBase64,
                                      @"operation" : @"uploadPngOP(params-flow)" }
                       withProgress:^(UO *op, UOUpProgress p){

     NSLog(@“Progress: %@”,(float)p.totalBytesSent/p.totalBytesExpectedToSend);                   
     
  }withCompletion:^(UO *op, NSError *error) {
    	                     
    	NSLog(@“operation is completion”);
    }] start];

```

#### Upload By Data (NSData)
```objectivec
    NSString* pathToGifToUpload     = [[NSBundle mainBundle] pathForResource:@"gifPicture" ofType:@"gif"];
    NSData*   gifDataToUpload       = [NSData dataWithContentsOfFile:pathToGifToUpload];
    NSString* gifStrBase64ToUpload  = [gifDataToUpload base64EncodedStringWithOptions:0];
    
    NSDictionary* postGifJsonToUpload =@{ @"data" : gifStrBase64ToUpload, @"operation" : @"uploadPngOP(params-flow)" };
    NSError* errorCreateDataFromJSONToUpload;
    NSData* postGifDataToUpload = [NSJSONSerialization dataWithJSONObject:postGifJsonToUpload options:0 error:&errorCreateDataFromJSONToUpload];
    
    
 [[UploadOP uploadTo:@"https://httpbin.org/post"
                                     data:postGifDataToUpload
                             withProgress:^(UploadOperation* op, UOUpProgress p){
       
  NSLog(@“Progress: %@”,(float)p.totalBytesSent/p.totalBytesExpectedToSend);                                                
 }   withCompletion:^(UploadOperation* op, NSError* error){
                              
 NSLog(@“operation is completion”);                               
 }] start];
```
#### Upload By Local URL (NSURL)
```objectivec
NSURL *urlToFile = [[NSBundle mainBundle] URLForResource:@"Auslogics_BoostSpeed_10.0.2.0" withExtension:@"zip"];
    
[[UploadOP uploadTo:@"https://httpbin.org/post"
                                 localURL:urlToFile
                             withProgress:^(UploadOperation* op, UOUpProgress p){
                            
  NSLog(@“Progress: %@”,(float)p.totalBytesSent/p.totalBytesExpectedToSend);      

 }  withCompletion:^(UploadOperation* op, NSError* error){
                               
 NSLog(@“operation is completion”);         
}] start];

```

2) The DataTaskOperation class is used to perform everyday network requests.

####  Get JSON
```objectivec
 [DTO GET: urlToJSON withDownloadProgress:^(DTO* op,DTODownProgress p) {

  NSLog(@“Progress = %f”, (float)p.countOfBytesReceived/p.countOfBytesExpectedToReceive);        
} withCompletion:^(DataTaskOperation *op, NSError *error) {
        
        NSError* errorConvertJSON;
        NSDictionary* json = [NSJSONSerialization JSONObjectWithData:op.receivedData options:kNilOptions error:&errorConvertJSON];
    	NSLog(@“Response: %@”, json);    
}];
```

#### Get Image 
```objectivec

[DTO GET: urlToPicture withDownloadProgress:^(DTO* op,DTODownProgress p) {
        
  NSLog(@“Progress = %f”, (float)p.countOfBytesReceived/p.countOfBytesExpectedToReceive);     
} withCompletion:^(DataTaskOperation *op, NSError *error) {

	if (op.state == BOSussessFinished){
	     UIImage*  image = [[UIImage alloc]initWithData: op.receivedData];
	}        
}];
```
#### POST Image by JSON

```objectivec
    NSString* pathToJSON     = [[NSBundle mainBundle] pathForResource:@"cache" ofType:@"json"];
    NSData*   jsonData         = [NSData dataWithContentsOfFile:pathToJSON];
    NSString* jsonDataBase64 = [jsonData base64EncodedStringWithOptions:0];
    NSDictionary* paramsForDataTaskPost_Json_OP = @{@"data": jsonDataBase64};
    
   [[DTO POST:@"https://httpbin.org/post" withParams:paramsForDataTaskPost_Json_OP
                       withUploadProgress:^(DTO *op, DTOUpProgress p) {

			NSLog(@“Progress Upload: %f”, (float)p.totalBytesSent/p.totalBytesExpectedToSend);
                           
} withDownloadProgress:^(DTO *op, DTODownProgress p) {
        			NSLog(@“Progress Download: %f”, (float)p.countOfBytesReceived/p.countOfBytesExpectedToReceive);            
	                           
 } withCompletion:^(DTO *op, NSError *error) {

        NSError* errorConvertServerDataToJson;
        NSDictionary* serverJson = [NSJSONSerialization JSONObjectWithData:op.receivedData options:kNilOptions error:&errorConvertServerDataToJson];                           
        NSLog(@“Response: %@”, json);    
}] start];
```


### Exceptions:
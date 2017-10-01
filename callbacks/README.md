# Callbacks

## What is it?
Callback refers to a technique that guarantees two functions, say *A* and *B*, will be called **synchronously**, that is, **B will wait for A to finish**.  
In some cases (usually when calling a function to run in a remote machine), simply calling *A* and *B* in sequence may lead to *B* starting its processing before the result of first call (*A*), which may be undesirable if *B* depends on *A*.

## How is it done?
The technique consists of passing the later function *B* as an argument to the first function *A*, which **calls back** the function *B* at proper time. It is important to notice that *A* must contain within its body a *B* call, and must also respect *B*'s interface.

## Pros
 + No busy wait (a loop that wastes processing power by constantly checking for first function to finish)

## Cons
 - Increased complexity: may lead to several nested calls, nested anonymous functions, and possibly some recursion
 - Black box: second call is hidden within the first call

## Examples
Consider this:
 - *A* is a call to a database query that takes 1 second.
 - *B* uses the info fetched from A to update GUI.

The problem:
```
A(...)  // Sends request to remote database and immediatelly goes to next line
B(...)  // Does not wait for remote database's response triggered by A's call
```

The callback solution:
```
// Sends request to remote database and calls B when it finishes
A(..., B)
```

B may be defined anonymously:
```
// Sends request to remote database and calls B when it finishes
A(..., function(...){
    ...
})
```

## How *A* is made?
The implementation may vary and it is usually hidden by API's interfaces. A Javascript example:
```
// API side
function doQuery (query, callback) {
    var data = {}
    
    // Does lots of processing to solve the query and stores it in that variable above
    // ...
    // Finished processing

    callback(data);    // Passes data during callback
}

// User side
var query = "SELECT * FROM tweets WHERE retweets > 100"
doQuery(query, function (data) {
    updateGUI(data.tweets)
});
```
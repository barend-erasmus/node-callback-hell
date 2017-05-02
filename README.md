# Node: Callback Hell

In this document we'll cover:

1. The Problem
2. The Solution
3. Frameworks

## The Problem

```javascript
    // Find user by username
    findUserByUsername(username, function (err, user) {
        
        // Check if user is active
        if (user.isActive) {

            // Find user permissions by user id
            findUserPermissions(user.id, function (err, permissions) {

                // Check for error
                if (err) {
                    console.error(err);
                    return;
                }

                if (permission.contains('admin')) {
                    
                    // Insert data into database
                    sql.query('INSERT INTO ....', function (err, result) {

                        // Check for error
                        if (err) {
                            console.error(err);
                            return;
                        }
                        
                        // Update data in database
                        sql.query('UPDATE ....', function (err, result) {

                            // Check for error
                            if (err) {
                                console.error(err);
                                return;
                            }

                            // Call third party api
                            http.get(url, function (err, response) {

                                // Check for error
                                if (err) {
                                    console.error(err);
                                    return;
                                }
                                
                                // Send JSON object to client
                                sendJSONToClient();
                            });
                        });
                    });
                }else {

                    // Insert data into database
                    sql.query('INSERT INTO ....', function (err, result) {

                        // Check for error
                        if (err) {
                            console.error(err);
                            return;
                        }

                         // Send JSON object to client
                         sendJSONToClient();
                    });
                }
            });
        }
    });
```


## The Solution

```javascript
    // Find user by username
    findUserByUsername(username).then(function(user) {

        // Check if user is active
        if (user.isActive) {

            // Find user permissions by user id
            return findUserPermissions(user.id);
        }

    }).then(function (permission) {
         if (permission.contains('admin')) {

             // Insert data into database
             return sql.query('INSERT INTO ....');

         } else {

             // Insert data into database
             return sql.query('INSERT INTO ....');
        
         }
    }).then(function (result) {

        // Call third party api
        return http.get(url);

    }).then(function (response) {

        // Send JSON object to client
        sendJSONToClient();

    }).catch(function (err) {
        console.error(err);
    });
```

## Frameworks

Frameworks to make async calls read better:

* CO
* BlueBird
* Async.js


### CO Example

```javascript
co(function* () {
    // Find user by username
    var user = yield findUserByUsername(username);

    // Check if user is active
    if (user.isActive) {

        // Find user permissions by user id
        var permissions = yield findUserPermissions(user.id);

        if (permission.contains('admin')) {

            // Insert data into database
            var result = yield sql.query('INSERT INTO ....');

            var response = yield http.get(url);

            // Send JSON object to client
            sendJSONToClient();

         } else {

            // Insert data into database
            var result = yield sql.query('INSERT INTO ....');
        
         }
    }
});
```
# SwiftKueryMySQL
MySQL plugin for Swift-Kuery framework

<!-- 
[![Build Status - Master](https://travis-ci.org/IBM-Swift/SwiftKueryMySQL.svg?branch=master)](https://travis-ci.org/IBM-Swift/SwiftKueryMySQL)
-->
![macOS](https://img.shields.io/badge/os-Mac%20OS%20X-green.svg?style=flat)
![Linux](https://img.shields.io/badge/os-linux-green.svg?style=flat)
![Apache 2](https://img.shields.io/badge/license-Apache2-blue.svg?style=flat)

## Summary
[MySQL](https://dev.mysql.com/) plugin for the [Swift-Kuery](https://github.com/IBM-Swift/Swift-Kuery) framework. It enables you to use Swift-Kuery to manipulate data in a MySQL database.

### Install MySQL

#### macOS
```
brew install mysql
mysql.server start
```
Other install options: https://dev.mysql.com/doc/refman/5.7/en/osx-installation.html

On macOS, add `-Xlinker -L/usr/local/lib` to swift commands to point the linker to the MySQL library location.
For example,
```
swift build -Xlinker -L/usr/local/lib
swift test -Xlinker -L/usr/local/lib
swift package generate-xcodeproj -Xlinker -L/usr/local/lib
```

#### Linux
Download the release package for your Linux distribution from http://dev.mysql.com/downloads/repo/apt/
For example: `wget https://repo.mysql.com//mysql-apt-config_0.8.2-1_all.deb`
```
sudo dpkg -i mysql-apt-config_0.8.2-1_all.deb
sudo apt-get update
sudo apt-get install mysql-server
sudo service mysql start
```
More details: https://dev.mysql.com/doc/refman/5.7/en/linux-installation.html

On linux, regular swift commands should work fine:
```
swift build
swift test
swift package generate-xcodeproj
```

## Using Swift-Kuery-MySQL

First create an instance of `MySQLConnection` by calling:

```swift
let connection = MySQLConnection(host: host, user: user, password: password, database: database, 
                                 port: port, characterSet: characterSet, copyBlobData: copyBlobData)
```
**Where:**
- *host* - hostname or IP of the MySQL server, defaults to localhost 
- *user* - the user name, defaults to current user
- *password* - the user password, defaults to no password
- *database* - default database to use if specified
- *port* - port number for the TCP/IP connection if connecting to server on a non-standard port (not 3306)
- *characterSet* - MySQL character set to use for the connection
- *copyBlobData* - Bool indicating whether or not to copy bytes to Data objects in QueryResult (defaults to true).  
                   When false, the underlying buffer is reused for blobs in each row which can be faster for large blobs.  
                   Do NOT set to false if you use queryResult.asRows or if you keep a reference to returned blob data objects.  
                   Set to false only if you use queryResult.asResultSet and finish processing row blob data before moving to the next row.  

All the connection parameters are optional, so if you were using a standard local MySQL server as the current user, you could simply use:
```swift
let connection = MySQLConnection(password: password)
```
*password* is also optional, but recommended.

Alternatively, call:
```swift
let connection = MySQLConnection(url: URL(string: "mysql://\(user):\(password)@\(host):\(port)/\(database)")!))
```

To establish a connection and execute queries:

```swift
connection.connect() { error in
   // if error is nil, connect() was successful
   let query = Select(from: table)
   connection.execute(query: query) { queryResult in
      if let resultSet = queryResult.asResultSet {
         for row in resultSet.rows {
            // process each row
         }
      }
   }
}
```

You now have a connection that can be used to execute SQL queries created using Swift-Kuery. View the [Kuery](https://github.com/IBM-Swift/Swift-Kuery) documentation for more information.

If you want to share a connection instance between multiple threads use `MySQLThreadSafeConnection` instead of `MySQLConnection`
```swift
let threadSafeConnection = MySQLThreadSafeConnection(password: password)
```
A MySQLThreadSafeConnection instance can be used to safely execute queries on multiple threads with the same connection.

## License
This library is licensed under Apache 2.0. Full license text is available in [LICENSE](LICENSE.txt).

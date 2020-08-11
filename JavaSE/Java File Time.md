转载自：

[Home](http://zetcode.com/)[Subscribe](http://zetcode.us13.list-manage.com/subscribe?u=9def9ccd4c70dbbaf691f90fc&id=6556210f80)

# Java file time tutorial

In Java file time tutorial, we show how to determine file creation, last modification, and last access time in Java with `Files` and `BasicFileAttributes`.

[Tweet](https://twitter.com/share)

## Files

`Files` is a Java class that contains static methods that operate on files, directories, or other types of files. Mostly, these methods will delegate to the associated file system provider to perform the file operations.



## BasicFileAttributes

`BasicFileAttributes` holds basic file attributes. These are attributes that are common to many file systems and consist of mandatory and optional file attributes, such as size of file creation time. `BasicFileAttributes` are retrieved with `Files.readAttributes()` method.

## Java file creation time

The file creation time in Java is retrieved with `BasicFileAttributes.creationTime()` method.

com/zetcode/JavaFileLastCreationTime.java

```
package com.zetcode;

import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.attribute.BasicFileAttributes;

public class JavaFileLastCreationTime {

    public static void main(String[] args) throws IOException {
        
        String fileName = "/home/janbodnar/world.sql";
        
        File myfile = new File(fileName);
        Path path = myfile.toPath();
        
        BasicFileAttributes fatr = Files.readAttributes(path, 
                BasicFileAttributes.class);
        
        System.out.printf("File creation time: %s%n", fatr.creationTime());
    }
}
```

This example prints the creation time of the specified file.

```
File creation time: 2017-06-01T12:48:40Z
```

This is a sample output.



## Java file last modification time

The `BasicFileAttributes.lastModifiedTime()` method gets the last modification time of a file in Java.

com/zetcode/JavaFileLastModifiedTime.java

```
package com.zetcode;

import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.attribute.BasicFileAttributes;

public class JavaFileLastModifiedTime {

    public static void main(String[] args) throws IOException {

        String fileName = "/home/janbodnar/world.sql";

        File myfile = new File(fileName);
        Path path = myfile.toPath();

        BasicFileAttributes fatr = Files.readAttributes(path,
                BasicFileAttributes.class);

        System.out.printf("Last modification time: %s%n", fatr.lastModifiedTime());
    }
}
```

This example prints the last modification time of the specified file.

```
Last modification time: 2017-06-01T12:48:40Z
```

This is a sample output.

## Java file last access time

The last access time of a file in Java is retrieved with `BasicFileAttributes.lastAccessTime()` method.

com/zetcode/JavaFileLastAccessTime.java

```
package com.zetcode;

import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.attribute.BasicFileAttributes;

public class JavaFileLastAccessTime {

    public static void main(String[] args) throws IOException {

        String fileName = "/home/janbodnar/world.sql";

        File myfile = new File(fileName);
        Path path = myfile.toPath();

        BasicFileAttributes fatr = Files.readAttributes(path,
                BasicFileAttributes.class);

        System.out.printf("Last access time: %s%n", fatr.lastAccessTime());        
    }
}
```

This example prints the last access time of the specified file.

```
Last access time: 2017-06-01T12:48:40Z
```

This is a sample output.

In this tutorial, we have determined the file creation, last modification, and last access time with `Files` and `BasicFileAttributes`.



List [all Java tutorials](http://zetcode.com/all#java).
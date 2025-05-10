---
title: Monitoring Directory Changes with Apache Commons VFS
date: 2025-05-10T10:38:00+05:30
tags: ["java", "apache commons", "file system", "monitoring", "javaops"]
---

## Keeping an Eye on Your Files: Directory Change Monitoring with Apache Commons VFS

I always wonder, how I can track the files changes, within a directory when I was learning java in my college. When I came to work, this horror story comes true, when I was officially a part of team, which analyses, lots of file processing, contents changed within the directory, and it is closely integrated with the Spring boot microservices. Initially I tried this solution to be fixed via Observer Pattern, but recently I tried Apache Commons VFS. Since, I am writing these days on Apache Commons, and the essential java utils, I found it worth sharing my internal notes to my readers.

Thanks to the great open source developers who contribute to  Apache commons 🤠🥳.

Lets Start 🚀🚀🧑‍🚀🧑‍🚀🏃‍➡️

![Darth Vader](../../images/Darth_Vader_-_2007_Disney_Weekends.jpg)

In the world of Java operations, keeping track of changes within directories is a common requirement. Whether you're building a system that reacts to new file uploads, processes modified configuration files, or needs to audit file system activity, having a robust and efficient way to monitor directories is crucial.

Enter **Apache Commons VFS (Virtual File System)**, a powerful library that provides a unified interface for accessing various file systems (local, FTP, SFTP, etc.). Within its arsenal of tools, `FileAlterationObserver` stands out as a dedicated class for observing file and directory changes.

This post will guide you through the process of using `FileAlterationObserver` to monitor a local directory for events like file creation, deletion, and modification.

### Setting Up Your Project

First things first, you'll need to include the Apache Commons VFS dependency in your Maven or Gradle project.

**Maven:**

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-vfs2</artifactId>
    <version>2.9.0</version> 
</dependency>
```
Remember to sync your project after adding the dependency.

**The Core Components**
Before diving into the code, let's understand the key players involved:

- `FileAlterationObserver`: This is the main class responsible for monitoring a specific directory. You register `FileAlterationListener` instances with it to receive notifications about file system events.
- `FileAlterationListener`: An interface that defines methods to be called when file or directory events occur (e.g., `fileCreated`, `fileDeleted`, `fileModified`, `directoryCreated`, `directoryDeleted`). You'll need to implement this interface to handle the events.
- `FileObject`: Represents a file or directory within the VFS framework. You'll use this to specify the directory to be monitored.

**Implementing a Simple File System Monitor**

Let's create a basic example that monitors a directory named `watched_directory` in your project's root.

```java

import org.apache.commons.vfs2.FileChangeEvent;
import org.apache.commons.vfs2.FileListener;
import org.apache.commons.vfs2.FileObject;
import org.apache.commons.vfs2.FileSystemException;
import org.apache.commons.vfs2.VFS;
import org.apache.commons.vfs2.impl.DefaultFileMonitor;

public class DirectoryMonitor {

    public static void main(String[] args) throws FileSystemException, InterruptedException {
        // The directory to monitor
        String directoryToWatch = "file:///path/to/your/watched_directory"; // Replace with your actual path

        // Create a FileObject for the directory
        FileObject watchedDir = VFS.getManager().resolveFile(directoryToWatch);

        // Create a FileListener to handle events
        FileListener listener = new FileListener() {
            @Override
            public void fileCreated(FileChangeEvent event) throws FileSystemException {
                System.out.println("File created: " + event.getFile().getName().getBaseName());
            }

            @Override
            public void fileDeleted(FileChangeEvent event) throws FileSystemException {
                System.out.println("File deleted: " + event.getFile().getName().getBaseName());
            }

            @Override
            public void fileChanged(FileChangeEvent event) throws FileSystemException {
                System.out.println("File modified: " + event.getFile().getName().getBaseName());
            }

            @Override
            public void directoryCreated(FileChangeEvent event) throws FileSystemException {
                System.out.println("Directory created: " + event.getFile().getName().getBaseName());
            }

            @Override
            public void directoryDeleted(FileChangeEvent event) throws FileSystemException {
                System.out.println("Directory deleted: " + event.getFile().getName().getBaseName());
            }

            @Override
            public void directoryChanged(FileChangeEvent event) throws FileSystemException {
                System.out.println("Directory modified: " + event.getFile().getName().getBaseName());
            }
        };

        // Create a FileAlterationObserver and add the listener
        DefaultFileMonitor fileMonitor = new DefaultFileMonitor(listener);
        fileMonitor.addFile(watchedDir);

        // Start the file monitor
        fileMonitor.start();

        System.out.println("Monitoring directory: " + watchedDir.getName().getPath());

        // Keep the main thread alive to continue monitoring
        Thread.sleep(Long.MAX_VALUE);

        // Stop the monitor (this part will likely not be reached in this example)
        // fileMonitor.stop();
    }
}
```
**Explanation:**

1. `directoryToWatch`: Replace `"file:///path/to/your/watched_directory"` with the actual absolute path to the directory you want to monitor. The `"file:///"` prefix is used for local file systems in VFS.
2. `VFS.getManager().resolveFile(directoryToWatch)`: This line uses the VFS manager to create a FileObject representing the directory to be watched.
3. `FileListener Implementation`: We create an anonymous inner class that implements the `FileListener` interface. Each method (`fileCreated`, `fileDeleted`, `fileChanged`, etc.) is overridden to print a message to the console when the corresponding event occurs. You would replace these System.out.println statements with your actual event handling logic.
4. `DefaultFileMonitor`: We instantiate a `DefaultFileMonitor` (which extends `FileAlterationObserver`) and register our `FileListener` with it using `addFile(watchedDir)`.
5. `fileMonitor.start()`: This starts the monitoring process in a separate thread.
6. `Thread.sleep(Long.MAX_VALUE)`: This line keeps the main thread alive indefinitely so that the `FileAlterationObserver` thread can continue monitoring. In a real-world application, you would have a more graceful way to manage the lifecycle of the monitor.
7. `fileMonitor.stop()`: This line is commented out as the `Thread.sleep(Long.MAX_VALUE)` will prevent it from being reached in this simple example. In a real application, you'd call this when you want to stop monitoring.

**Running the Example**
Create a directory named `watched_directory` in your project's root (or adjust the `directoryToWatch` path accordingly).

**Run the DirectoryMonitor class.**
Now, try creating, deleting, and modifying files and subdirectories within the `watched_directory`. You should see the corresponding messages printed to your console.

**Beyond the Basics**
This example provides a fundamental understanding of how to use `FileAlterationObserver`. Here are some ideas for extending it:

- `Filtering Events`: You can implement custom logic within your `FileListener` to filter events based on file names, extensions, or other criteria.
- `Asynchronous Processing`: For more complex tasks, you might want to use a thread pool or a message queue to handle the events asynchronously.
- `Error Handling`: Implement robust error handling to gracefully manage potential `FileSystemExceptions`.
- `Monitoring Multiple Directories`: You can create multiple FileAlterationObserver instances to monitor different directories simultaneously.

### Conclusion
Apache Commons VFS's FileAlterationObserver offers a clean and efficient way to monitor directory changes in Java. By implementing a FileAlterationListener, you can react to file system events and build powerful applications that are aware of their environment. This library abstracts away the complexities of platform-specific file system monitoring mechanisms, providing a consistent and reliable solution for your JavaOps needs.
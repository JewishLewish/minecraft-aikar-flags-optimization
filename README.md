This guide focuses on using Aikar's Flags: https://docs.papermc.io/paper/aikars-flags

# What are Aikar's Flags
Aikar's Flags are a set of command line flags that can be used to improve the performance of a Minecraft server running on the PaperMC server software. These flags are used to configure the Java Virtual Machine (JVM) that runs the server, and they can help to reduce lag and improve overall performance.

The main purpose of using Aikar's Flags is that it can allow for Minecraft Servers to run more effectivily and efficiently. 

# Tests

Server Being Used:
* Quad Core, Armpeture (Arm64)
* Unmetered Storage
* 6GB of Memory
* MC 1.19.2 Paper
* Java 17
* All .yml files are NOT optimized. 


**Test #1 Flags:** 

```
java -Xms128M -XX:MaxRAMPercentage=95.0 -jar server.jar
```
|        | Time (Start Up)          | Memory Usage  |
| ------------- |:-------------:| :-----:|
| Average     | 9.02s | 1.45GB |
| S.Deviation      | ±.241s     |   ±.11GB |

This uses the default startup flags (usually used in Pterodactyl).



**Test #2 Flags:**

```
java -Xms1G -Xmx1G -XX:+UseG1GC -XX:MaxRAMPercentage=95.0 -XX:+UnlockExperimentalVMOptions -XX:MaxGCPauseMillis=100 -XX:+DisableExplicitGC -XX:TargetSurvivorRatio=90 -XX:G1NewSizePercent=50 -XX:G1MaxNewSizePercent=80 -XX:G1MixedGCLiveThresholdPercent=50 -XX:+AlwaysPreTouch -XX:ReservedCodeCacheSize=2048m -XX:+UseCodeCacheFlushing -XX:SoftRefLRUPolicyMSPerMB=20000 -jar server.jar
```
|        | Time (Start Up)          | Memory Usage  |
| ------------- |:-------------:| :-----:|
| Average     | 9.75s | 1.25GB |
| S.Deviation      | ±.376s     |   ±.011GB |

We are now telling the code to be more specific and do certain adjustments. More detail about the flags will be explained below. 



**Test #3 Flags:** 

```
java -Xms5G -Xmx5G -XX:+UseG1GC -XX:MaxRAMPercentage=95.0 -XX:+UnlockExperimentalVMOptions -XX:MaxGCPauseMillis=100 -XX:+DisableExplicitGC -XX:TargetSurvivorRatio=90 -XX:G1NewSizePercent=50 -XX:G1MaxNewSizePercent=80 -XX:G1MixedGCLiveThresholdPercent=50 -XX:+AlwaysPreTouch -XX:ReservedCodeCacheSize=2048m -XX:+UseCodeCacheFlushing -XX:SoftRefLRUPolicyMSPerMB=20000 -jar server.jar
```
|        | Time (Start Up)          | Memory Usage  |
| ------------- |:-------------:| :-----:|
| Average     | 8.23s | 5.39GB |
| S.Deviation      | ±.196s     |   ±.004GB |

This is similiar to Test 2 but now we are allocating 5GB of the server instead of 1GB to the flags. Since memory is allocated ahead of time and more memory is allocated than needed for the MC server. It allows the MC server to start up much faster since all the memory has been allocated ahead of time. 

## Explaining the Difference in Tests
You may have noticed the big difference between all three tests and, the main reason, has to do with the first two flags:

``-Xms5G``: sets the initial heap size to 5 gigabytes. This means that when the JVM starts, it will allocate 5 gigabytes of memory for the heap.

``-Xmx5G``: sets the maximum heap size to 5 gigabytes. This means that the JVM will not allocate more than 5 gigabytes of memory for the heap, even if the program requires more memory.

The heap is the area of memory where Java objects are stored. The heap size is important because if the heap is too small, the JVM will not be able to allocate enough memory for the program, resulting in OutOfMemoryError. On the other hand, if the heap is too large, the JVM will waste memory resources, and could potentially cause the server to crash.

By setting both the initial and maximum heap size to the same value, in this case 5 gigabytes, you ensure that the JVM always has a consistent amount of memory available. This can help to prevent OutOfMemoryError and improve overall performance.

### **Sidenote:**
You can remove the ``-Xmx5G`` from the startup and will operate normally but it comes at a cost that the server will never know what's the "maximum" size of the memory heap. This *can* cost you performance.

### **Takeaway:**
When using the first two flags. Try to set it atleast 512MB to 1GB below the server's amount of memory.
    
    ex. The Test Server I used was 6GB. So I allocated max 5GB to Paper MC Server.

# Explaining More Flags 
``-XX:+UseG1GC``: Enables the G1 garbage collector, which is designed to minimize pauses in the server by more aggressively collecting and cleaning up memory that is no longer being used by the program.

``-XX:+ParallelRefProcEnabled``: Enables parallel reference processing, which allows the JVM to process multiple references at the same time, improving performance on multi-core systems.

``-XX:+UseStringDeduplication``: Enables string deduplication, which reduces memory usage by sharing identical strings.

``-XX:+UseFastAccessorMethods``: Enables fast accessor methods, which improves performance when accessing fields in classes.

``-Dpaper.debug``: Enables debug mode for PaperMC, which provides additional logging and performance information.

``-XX:+UseCompressedOops``: Enables the use of Compressed Object Pointers (CompressedOops) which is a feature that allows the JVM to use 32-bit memory addresses on 64-bit systems, reducing memory usage and improving performance.

``-XX:+UnlockExperimentalVMOptions``: Enable the use of experimental VM options.

``-XX:+AlwaysPreTouch``: Enables AlwaysPreTouch flag, which causes the JVM to fully initialize all memory pages used by the heap at startup, which can help to prevent memory-related errors.

``-XX:+UseLargePages``: Enables the use of large pages, which can improve performance by reducing the number of memory page translations the JVM needs to make.

``-Djdk.tls.ephemeralDHKeySize=2048`` : this is a security flag that increases the key size used for ephemeral Diffie-Hellman (DH) key agreement. This improves the security of the SSL/TLS connection.

``-XX:+UseAdaptiveSizePolicy``: Enables the use of adaptive size policy, which dynamically adjusts the size of the heap and other memory-related settings based on the current usage of the JVM.

``-XX:+UseTransparentHugePages``: Enables the use of transparent huge pages, which can improve performance by reducing the number of memory page translations the JVM needs to make.

``-XX:+UseParNewGC``: Enables the use of parallel new generation garbage collector.

``-XX:+UseConcMarkSweepGC``: Enables the use of Concurrent Mark Sweep (CMS) garbage collector.

``-XX:+UseNUMA``: Enables the use of Non-Uniform Memory Access (NUMA) which can improve performance on systems with non-uniform memory architecture.

``-XX:+UseNUMAInterleaving``: Enables the interleaving of memory across NUMA nodes.

``-XX:+UseTLAB``: Enables the use of thread-local allocation buffer (TLAB) which can improve performance by reducing contention for memory allocation.

### **Takeaway:**
Any of these flags can be used for startup and will help with making your MC server run more efficiently.


# Other Optimization Advice 

## .yml files + Avoiding certain plugins 
YouHaveTrouble made a really great guide on how to properly optimize your mc file servers: https://github.com/YouHaveTrouble/minecraft-optimization

HIGHLY Recommended. 

## Making your own plugins 

If you are trying to make your plugin run more efficiently / less memory usage then I would advise the following:

* Use less global variables

In this example, you can see the difference between a "global" and "local" variable. As soon as the public void for "exampleMethod()" is done, that local variable is thrown to the garbage. The global variable can stick around since the Garbage Collector might not know if the variable is still being used or not.
```java
public class Example{
    int globalVariable = 0; // this is a global variable
    public void exampleMethod() {
        int localVariable = 0; // this is a local variable
        //As soon as the code for "exampleMethod()" ends, the local variable is freed. Which preserves memory.
    }


    //more code here
    //The GC doesn't know if the Global Variable will continue to be used or not. 
    //So it sticks around, causing memory to be used.
}
```



* Avoid for unncessarily loops / while loops or Use Java's built-in tools

Let's say you want to write a code that loops every pig in Minecraft. Java has a lot of tools to help us.

Inefficient manner:
```java
World world = ...; // Get the world you want to loop entities
List<Entity> entities = world.getEntities();
for (Entity entity : entities) {
    //In this example, it loops every single entity in world. 
    //However, if we are only trying to get pig entities, then this code is inefficient.
}
```

Efficient manner: 
```java
World world = ...; // Get the world you want to loop entities
List<Entity> entities = world.getEntities();
List<Pig> pigEntities = entities.stream()
                                .filter(e -> e instanceof Pig)
                                .map(e -> (Pig) e)
                                .collect(Collectors.toList());
for (Pig pig : pigEntities) {
    //This code does the same above but only loops through Pigs.
    //So we don't have to loop through every other animal. 
}
```

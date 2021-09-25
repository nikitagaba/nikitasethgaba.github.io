## JAVA 11 : Source-File Launch Capability

We all are aware of the two step procedure of comiling and running Java Programs i.e.
1. Compile Java Program into Bytecode using Javac Compiler
```
javac filename.java
```
2. Run the generated class file from step 1 using Java Launcher - java
```
java filename.class
```

However, with the launch of Java 11 - the above two steps can be clubbed into 1 without explicitly invoking Javac Compiler i.e. 
```
java filename.java
```
The above command automatically compiles and runs the file, without actually generating a filename.class file. 

This capability is useful for:
1. Short one-time use programs for usage in scripts
2. Experimenting some features.

But there's a catch:
1. Since the class file is not generated, you need to rerun the program, you need to execute the source file each time.
2. If a class file already exists i.e. filename.class, you must delete it before trying source-file launch feature otherwise the launch will fail.
3. Not all programs are eligible for this launch capability
    - Entire program should be a part of single source file
    - It will always execute the first class in the file , so that class must contain the main() method otherwise the launch will fail



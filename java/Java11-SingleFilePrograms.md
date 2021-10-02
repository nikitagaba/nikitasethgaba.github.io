## JAVA 11 : Source-File Launch Capability

We all are aware of the two step procedure of comiling and running Java Programs i.e.
1. Compile Java Program into Bytecode using Javac Compiler
```
javac filename.java
```
2. Run the generated class file from step 1 using Java Launcher - java
```
java filename
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
2. Not all programs are eligible for this launch capability
    - Entire program should be a part of single source file.
    - It will always execute the first class in the file, so that class must contain the main() method otherwise the launch will fail.
    
For Instance, for the below code snippet:

```
class J11AnotherClass{
	static void readOnlyMethod(){
		System.out.println("Calling method from another class");
	}

}

class J11SingleFileProgram{
	public static void main(String args[]){
		System.out.println("Experimenting with Source-File Launch Capability");
		J11AnotherClass.readOnlyMethod();
	}
}

```
This source-file launch capability will give error: 

```
>java J11SingleFileProgram.java
error: can't find main(String[]) method in class: J11AnotherClass
```

The same is not true with standard two step complilation and execution:

```
>javac J11SingleFileProgram.java

>java J11SingleFileProgram
Experimenting with Source-File Launch Capability
Calling method from another class
```

But if the sequence of classes is reversed i.e. J11SingleFileProgram class is the first class compiler reads, program runs successfully

```

class J11SingleFileProgram{
	public static void main(String args[]){
		System.out.println("Experimenting with Source-File Launch Capability");
		J11AnotherClass.readOnlyMethod();
	}
}

class J11AnotherClass{
	static void readOnlyMethod(){
		System.out.println("Calling method from another class");
	}

}
```

```
>java J11SingleFileProgram.java
Experimenting with Source-File Launch Capability
Calling method from another class
```




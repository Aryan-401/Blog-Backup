## So I tried Carbon...

First of all, Happy 75th Independence Day, India!!! 🇮🇳🇮🇳🇮🇳

>Carbon *(/ˈkɑːb(ə)n/)* *noun.*

>The chemical element of atomic number 6, a non-metal with two main forms (diamond and graphite), occurs in impure form in charcoal, soot, and coal.

Carbon isn't just atomic number 6, haunting us in General Organic Chemistry, but now is a programming language developed by google to eventually be used over the Legendary (and age-old) language C++ \*Dramatic music intensifies*. 
With the help of a few straightforward programs written in this article, we will try to understand and learn the language. We would also try to understand why Google (did I forget to mention Google made the language) even needed to create Carbon and why it might be time for our old friend C++ to bid farewell.

## Why Carbon?

C++ is a monster of a programming language. First built in 1985, the creator of C++ (Bjarne Stroustrup) has stated, "Within C++, there is a much smaller and cleaner language struggling to get out." The problem with changing or evolving C++ today is that it is an essential part of most code bases that it doesn't make sense to dramatically change how the language functions for the sake of changing technology. C++ focuses more on standardization rather than design functionality because of this reason. Carbon was designed not as a substitute for C++. Rather than an attempt to incrementally evolve C++, it is designed around interoperability with C++ and large-scale adoption and migration for existing C++ codebases and developers. 
 
As part of the documentation of Carbon, it states that this new, more dynamic language would need to have certain features, which include:

- **Performance matching C++**an essential property for our developers.
- **Seamless, bidirectional interoperability with C++**, such that a library anywhere in an existing C++ stack can adopt Carbon without porting the rest.
- **A gentle learning curve** with reasonable familiarity for C++ developers.
- **Comparable expressivity** and support for existing software's design and architecture.
- **Scalable migration**, with some level of source-to-source translation for idiomatic C++ code.

We can also observe how specific newer languages like Kotlin and Typescript have been a medium for change for their older counterparts (Java and JavaScript, respectively). Google wants to do that with C++ by Introducing Carbon.

Carbon is still an experimental language and by no means ready to be used in real-world applications, and it won't be for a reasonably long time, but it doesn't hurt to try to use it and add another language you know how to "Hello World" in.

## Installing Carbon and its Compiler

Since Carbon is in such early stages of development, it isn't easy to use it on your local machine. For Windows, it's an even longer procedure since we have to install brew through WSL (Windows Subsystem for Linux). Assuming you have WSL installed with Ubuntu as your primary Linux Operating System, we can now move forward with installing brew and then ultimately installing Carbon. 

### Installing Homebrew

1. Click [here](https://brew.sh/) and copy the installation command on the webpage. Open your Ubuntu environment in WSL and paste the command there. [Takes 5-10 mins]

You might be required to enter your password during the previous step. I had to run this command twice as I got multiple fatal errors during this step.

2. After the command runs successfully, scroll until you see a section called "Next Steps." Copy and paste the commands in the order they are given into the terminal. They will look like the image below:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1660208306403/jWqO0Yoe2.png align="left")

3. Next in line is to install the build-essentials of homebrew, which can be done with `sudo apt-get install build-essentials` [Takes 3-5 mins]

4. Now, we will install gcc. Install it using the `brew install gcc` command [Takes 5-10 mins]

I ran into another error where my homebrew-core was "not tapped correctly," which I figured out by running `brew doctor`. It gave me the necessary commands to fix the error, after which `gcc` installation was easy as pie.

## Install the Carbon Compiler

1. Install the Bazelisk Launcher using `brew install bazelisk`

2. Install LLVM (Low Level Virtual Machine) using `brew install llvm` [Takes 5-15 mins]

3. Add this to your path variable using `export PATH="$(brew --prefix llvm)/bin:${PATH}"`

4. Since we are doing this installation on Ubuntu, we will also need `zlib1g dev`, which we can install using `sudo apt install zlib1g-dev` 

5. We now have all the dependencies installed for the compiler. Time to clone the Github Repository using `git clone https://github.com/carbon-language/carbon-lang`

## Coding Carbon in Windows using VSCode

And there we have it, we've successfully installed Carbon on your Linux Machine. Time to port it over to windows, so we don't have to code in the terminal again.

1. Open VS Code and install the Remote Development Extention

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1660209485466/avdIWRmWA.png align="left")

2. Open Remote Explorer on the left-hand pane and click on the drop-down at the top of the screen. Select "WSL Targets" from the list. Then click on the button beside Ubuntu, as shown in the image.

![Screenshot 2022-08-11 144918.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1660209646742/4wrazKWhC.png align="left")

3. Wait for the Home Screen to Install all required dependencies and then go to `File < Open Folder < /home/{user}/carbon-lang/`

4. Open `explorer/testdata/print/format_only.carbon` in the VS Code Window. A Hello World Program is displayed! 

5. To see its output run `bazel run //explorer --  ./explorer/testdata/print/format_only.carbon` and wait a couple minutes. Since Carbon isn't ready to be adopted by the masses, its compilation time is painfully slow, and it took me almost 2 minutes to compile.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1660210143338/Il8xuDedq.png align="left")


## Writing Fizz Buzz in Carbon

Everyone has heard of Fizz Buzz. If the number is divisible by 2, print "Fizz"; If it's divisible by 3, print "Buzz". If it's divisible by both, print "FizzBuzz"; else, print the number. Here is the implementation in Carbon: 

```c++
package ExplorerTest api;

fn Main() -> i32{
    var i: auto = 0;
    while(i <= 100){
        if (i % 6 == 0){
            Print("{0} FizzBuzz", i);
        }

        else if (i % 2 == 0){
            Print("{0} Fizz", i);
        }

        else if (i % 3 == 0){
            Print("{0} Buzz", i);
        }

        else{
            Print("{0}", i);
        }
        i = i + 1;
    }

    return 1;
}
```

Let us discuss what every line in this program does: 

- `package ExplorerTest api;`

Base package and acts like `#include <iostream>` in C++

- `fn Main() -> i32` 

Our main function which has a return type of Integer 32bit

- `var i: auto = 0`

Creates a variable `i' with value `0`

- `while(i <= 100)`

While Loop to be executed while `i < 100`. I had originally tried to create a for loop like in C++ [`for (int i =0; i <= 10; i++)`] but got a syntax error 

[COMPILATION ERROR: foo_bar_baz.carbon:4: syntax error, unexpected identifier, expecting COLON]

- `if` statements

They execute their block only if the condition in parenthesis is True. 

- `Print` statements

Print the value inside the parenthesis. If variables need to be printed, we can use `{num}` where `num` is a number starting from 0. 
Eg: 
```c++
var a: auto = 1;
var b: auto = 5;
var c: auto = 2;
var d: auto = 6;

Print("These are Numbers: {0}, {1}, {2}, {3}", a,b,c,d);
```
Will give the output: 
```
These are Numbers: 1, 5, 2, 6
```

## Conclusion
Carbon is a fantastic Language, it's still in its early stages of development, and it'll hopefully get better over time. One obvious flaw is the lack of documentation. I want to remind you that Carbon is still under development, and Google does not recommend using it for any scaled-up applications. I hope you have as much fun as I did trying to learn Carbon!
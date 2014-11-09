# Pry

Pry is awesome. Here are some of the commands available within it. PResuming I have an object called `s` of type string:

````
show-doc s.downcase # Show the docs for String#downcase
show-doc String#downcase # Same as ^
show-method s.downcase # Show the method definition.
show-method s.swapcase -l # Use line numbers
````

We canuse ls to show us methods etc
````sh
ls #List the methods and variables on the current object
ls s #List the methods and variables on s
ls MyClass -M #List the class methods available
ls MyClass -m #List the instance methods available
ls MyClass -mj # The -j flag only shows methods that aren't inherited.
````

We can also move up and down the object heirachy

    cd MyClass
    nesting
    cd ..

Using a semicolon on the end of a command surpresses the return value of the expression. This is great if we only want to see what was printed to STDOUT.

We can also look inside gems:
````shell
gem-cd cucumber # Switch into a specific gem
.pwd # Runs a shell command
.tree # Show internal structure of gem
.cd lib # Move about inside the gem
````

I can clear a partially written class or method out of the input buffer too

    %> def foo
    %*   pu

    show-input # Show the current input buffer
    amend-line 1 def foo(param) # Amend the first line to take a parameter
    ! # Clear the buffer entirely

We can edit methods from within pry too. Presuming we have a method in context:

    edit-method to_s # Jump to the right place in the code.



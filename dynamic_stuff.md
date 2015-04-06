# Dynamic Stuff

## Structs

It's a fairly common use case to have a class that just exposes it's instance variables as methods:

```ruby
class Tweet
  attr_accessor :user, :status

  def initialize(user, status)
    @user = user
    @status = status
  end
end
```

We can replicate this functionality using a struct:

```ruby
Tweet = Struct.new(:user, :status)
```

This is particularly useful in tests. If we want to add instance methods to the `Tweet` class, we can pass a block to the struct constructor:

```ruby
Tweet = Struct.new(:user, :status) do
  def to_s
    "#{user}: #{status}"
  end
end
```

We can now call `.to_s` on Tweet instances. Generally, if we're adding more than a few methods to a struct then we should probably be using a proper class. Structs are intended to deal with classes that mostly handle storing data.

## Aliases

The `alias_method` allows us to alias instance methods. If we need to reopen a class to add some functionality, we can create an alias to the original method and then call that in our new one:

```ruby
class Hash
  alias_method :old_to_s, :to_s

  def to_s
    do_some_new_stuff
    old_to_s
  end

  def do_some_new_stuff
  end
end
```

Having said that, it's probably better to create a new custom subclass of Hash and use that, in this instance.

## Dynamically defining a method

The `define_method` takes a symbol (which ill be used as the method name) and a block (which will be used as the methods contents). This is useful if we want to define a bunch of methods recursiveley based on some symbols:

```ruby
class Tweet
  states = [:draft, :posted, :deleted]
  states.each do |state|
    define_method state do
      @state = state
    end
  end
end
```

This will create `draft`, `posted` and `deleted` methods that test the @state to `:draft`, `:posted` and `:deleted` respectiveley.

## Calling a method with `send`.

Instead of calling a method using normal dot notation, we can call it with `send`:

````ruby
"hello".send(:to_s)
````

This behaves exactly the same, but is useful because:

- It can have a symbol OR string passed to it, meaning we can make dynamic method calls.
- It is able to call protected or private methods (calling `public_send` instead will prevent this).

## Capturing a method.

We can capture a method into a Method object by using the `method` method. We can then call that method later:

```ruby
method = "hellothere".method(:upcase) #=> #<Method: String#upcase>
method.call #=> "HELLOTHERE"
```

If the method takes a parameter, then we need to pass that in when we call it: `method.call(1)`.

This might seem useless, but any method object can be used with the `&`, just like a proc or lambda.

# The Meaning of Self

In global scope, `self` will always refer to `main`. Inside a class, `self` refers to the class itself. Thus, when we call:

```ruby
class Thing
  def self.find(word)
  end
end
```

Is the same as:

```ruby
class Thing
  def Thing.find(word)
  end
end
```

That's how class methods work.

Inside an instance method, `self` refers to the instance on which it is called.

This is all important because when you call a method without an explicit reciever, ruby will look for the method on self:

```ruby
object.my_method #=> Looks for the method in #<object>
my_method #=> Equivelant to self.my_method. Looks for the method in the current context, in this case it's main.
```

## Class and Instance Eval

We can call this on any class, passing in a block. It will set the value of self to the given class and executes the block.

```ruby
class Tweet
  attr_accessor :status, :created_at

  def initialize(status)
    @status = status
    @created_at = Time.now
  end
end
```

Instead of reopening the class method to add a new attr_accessor, we could use `class_eval`:

```ruby
Tweet.class_eval { attr_accessor :user }
```

This will set self to `Tweet` and execute the block. LEt's say we wanted to create a logger that logs all calls to the `sa_hi` method on `Tweet` objects. Here's how we'd like it to work:

```ruby
logger = MyMethodLogger.new
logger.log_method(Tweet, :say_hi)
```

Here's what we want to do inside our `log_method` method:

1. Open the class we've passed in (using `class_eval`).
2. Alias the method, so we can call it later.
3. Override the method with a new one (using `define_method`).
4. In our new method, make a log entry and then run the original method using `send`.

```ruby
class MyMethodLogger
  def log_method(klass, method_name)
    klass.class_eval do
      alias_method "old_#{method_name}", method_name
      define_method method_name do
        puts "#{Time.now}: Called #{method_name}"
        send("old_#{method_name}")
      end
    end
  end
end
```

This is pretty cool stuff. However, it doesn't work with methods that recieve arguments. To solve this, we should capture any arguments or block passed in when we define our new block, and pass them on when we call the original using send:

```ruby
class MyMethodLogger
  def log_method(klass, method_name)
    klass.class_eval do
      alias_method "old_#{method_name}", method_name
      define_method method_name do |*args, &block|
        puts "#{Time.now}: Called #{method_name}"
        send("old_#{method_name}", *args, &block)
      end
    end
  end
end
```

The `instance_eval` method works in a simmilar way to `class_eval`, but sets self to the instance it is called on:

```ruby
tweet = Tweet.new
tweet.instance_eval do
  self.status = "some new thing"
end
```

# Method Missing

This is run every time a method is called, that doesn't exist. It's useful for printing error messages. Note that we pass this on to ruby's default method_missing using `super`:

```ruby
class Tweet
  def method_missing(method_name, *args)
    logger.warn "You called #{method_name} with args: #{args}"
    super
  end
end
```

This is really useful if we want to delegate methods to another class. For example, we couls defer all unknown method calls on a tweet object to an associated user object:

```ruby
class Tweet
  def method_mising(method_name, *args)
    @user.send(method_name, *args)
  end
end
```

It's often a good idea to only defer the methods you need, so as to avoid method_missing errors being produced by the wrong class. We can achieve this by creating a constant array containing the methods to delegate, and then checking:

```ruby
class Tweet
  DELEGATED_METHODS = [:username, :photo]

  def method_mising(method_name, *args)
    if DELEGATED_METHODS.include? method_name
      @user.send(method_name, *args)
    else
      super
    end
  end
end
```

Rubt provieds a `SimpleDelegator` class that we can inherit from, to achieve this simple delegation.

Generally, when using method_missing, we should also implement `respond_to?`, so that it gives the correct response. As of ruby 1.9.3, we should use `responde_to_missing?` instead. This ensures that calls to `something.method(:some_missing_method)` return a valid Method object.

### Speeding up method_missing.

If we know that an unknown method is likeley to be called very regularly, it can be faster to define that method the first time it's called:

```ruby
def method_missing(method_name, args*)
  self.class.class_eval do # Evaluate in the contect of the class
    define_method(method_name) do # Define the method
      # Do some stuff
    end
    send(method_name) #Invoke the method the first time
  end
end
```

Once we've defined this, the method will exist on the class, and will be called instead of `method_missing` in the future.






## Basic Blocks

Given some similar code like this, we shuould notice that both methods are very similar:

```
def update_status(user, tweet)
  sign_in(user)
  post(tweet)
  rescue ConnectionError => e
    logger.error(e)
  ensure
    sign_out(user)
end

def get_list(user, list_name)
  sign_in(user)
  retrive_list(tweet)
  rescue ConnectionError => e
    logger.error(e)
  ensure
    sign_out(user)
end
```

 Only one line is different. This should signal that we could just pass that line as a block. Let's create a new method, replacing that line with `yield`:

```
def while_signed_in_as(user)
  sign_in(user)
  yield
  rescue ConnectionError => e
    logger.error(e)
  ensure
    sign_out(user)
end
```

We can then call the method as usual, but pass in a block which will be substituted for `yield`:

while_signed_in_as(user) { post(tweet) }
while_signed_in_as(user) { retrieve_list(list_name) }

Note that we only need to include `yeild` in a method to signal that it accepts a block.

Here's another example. Assume we have the following code:

```
class Timeline
  def list_tweets
    @user.friends.each do |friend|
      friend.tweets.each {|tweet| puts tweet}
    end
  end
  def store_tweets
    @user.friends.each do |friend|
      friend.tweets.each {|tweet| tweet.cache}
    end
  end
end
```

Again, only one line differs: the action inside the inner block. We can abstract this and create our own method. Let's follow convention, and call it `each`:

```
class Timeline
  def each
    @user.friends.each do |friend|
      friend.tweets.each {|tweet| yield tweet}
    end
  end
end
```

Note that we're yielding the tweet to the block. this makes it available to the block as a variable (without this, we wouldn't be able to use it as `my_tweet`):

```
timeline.each {|my_tweet| puts my_tweet}
timeline.each {|my_tweet| my_tweet.cache}
```

Handy Hint: Because we've named our method `each`, if we include Enumerable in the class, we'll get all of the methods it provides to other iterators.

### Optional Blocks

If we want a method to optionally recieve a block, we can use the `block_given?` method:

```ruby
def my_method
  if block_given?
    puts yield
  else
    puts "No block provided"
  end
end
```

### Using Blocks to Instansiate Objects

It's a common pattern to create new objects using blocks:

```ruby
Tweet.new do |tweet|
  tweet.status = "Hello there"
  tweet.created_at = Time.now
end
```

This is done by yielding `self` to the block (so that the block is essentially calling `self.status=` etc). Obviously, we only want to do that if a block has been provided:

```ruby
class Tweet
  def initialize
    yield self if block_given?
  end
end
```

## Procs and Lambdas

Procs allow us to store a block of code for later execution:

```ruby
a = Proc.new { puts "Hello"}
a.call # Calls the proc
```

We could also use a lambda. While it's not the same, it responds to the same interface:

```ruby
a = lambda { puts "Hello"}
a.call # Calls the proc
```

Since 1.9, we can create lambdas with a nicer syntax (called 'stabby lambdas'):

```ruby
a = -> { puts "Hello"}
a.call # Calls the proc
```


Assuming we have this code, which handles authentication, and yields the actual posting of the tweet to the block:

```ruby
class Tweet
  def post
    if authenticate?(@user, @password)
      # submit the tweet
      yield
    else
      raise 'Auth Error'
    end
  end
end

tweet = Tweet.new
tweet.post { puts 'Sent' }
```

We could change this to call the `call` method on a proc that we pass into the post method. It may seem that there's no advantage to this, but it alows us to pass more than one block into a method. We might include one to handle the error message:

```ruby
class Tweet
  def post(success, error)
    if authenticate?(@user, @password)
      # submit the tweet
      success.call
    else
      error.call
    end
  end
end

tweet = Tweet.new
success = -> { puts 'Sent' }
error = -> { raise 'Auth Error' }
tweet.post(success, error)
```

## Converting between Procs/lambdas and Blocks

This fails, because the each method expects a block, not a Proc object:

```ruby
tweets = ["First one", "second one"]
printer = -> {|tweet| puts tweet}

tweets.each(printer)
```

By prefixing the perameter with an `&` **when we're calling it**, any proc passed to it will be converted to a block:

```ruby
tweets = ["First one", "second one"]
printer = -> {|tweet| puts tweet}

tweets.each(&printer)
```

We can also use the `&` when deining methods. This turns any block that's passed into it when it's called into a Proc, so that we can execute it later.

Consider this:

```ruby
class Timeline
  attr_accessor :tweets

  def each
    tweets.each {|t| yield t}
  end
end

timeline.each do |tweet|
  puts tweet
end
```

We could refactor this so that the Timeline#each method takes a block, converts it to a proc (using the `&`, then within the method passes it on to the tweets#each method, converting it back to a proc as it calls `tweets.each`, because `tweets.each` expects to recieve a block:

```ruby
class Timeline
  attr_accessor :tweets

  def each(&block)
    tweets.each(&block)
  end
end

timeline.each do |tweet|
  puts tweet
end
```

## Converting a symbol to a Proc

Given some code like this:

```ruby
tweets.map {|tweet| tweet.user}
```

We can refactor it like this:

```ruby
tweets.map(&:user)
```

We are saying: "Call the method named by the symbol on whatever is yielded to the block".














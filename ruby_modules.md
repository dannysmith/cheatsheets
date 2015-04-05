
## Calling methods on a module directly

We can use modules as namespaces for methods.
We can use them after rquiring the module.

````
module ImageUtils
  def self.perview(image)
  end

  def self.transfer(image, destination)
  end
end

image = user.image
ImageUtils.preview(image)
````

## Extending a class' instance methods.

We can also include the module in a class.
This makes them available as INSTANCE methods on the class

````
module ImageUtils
  def self.perview
  end

  def self.transfer(destination)
  end
end

class Image
  include ImageUtils
end

image = user.image
image.preview
````

## Extending a class' class methods

If we use extend instead, the methods are available as CLASS methods:

````
module Listable
  def show_as_list
  end
end

class Tweet
  extend Listable
end

Tweet.show_as_list
````

## Extending the instance methods of an individual object

If we want to extend the instance methods of one object only, we can call the `extend` method:

````
class Image; end

module ImageUtils
  def preview; end
end

image = Image.new
image.extend(ImageUtils)
image.preview
````

This is particularly useful when overriding the default behaviour of built-in classes to add or override methods since it will not polute all instances of those objetcs.


## Including a module's class AND instance methods in a class

If we create a module with a submodule containing the class methods, we can include one and extend the other:

````
module ImageUtils
  def preview
  end

  module ClassMethods
    def create_image
    end
  end
end

class Image
  include ImageUtils
  extend ImageUtils::ClassMethods
end

Image.create_image
image.preview
````

There is, however, a better way to do this by using method hooks. The `self.included` hook is run whenever this module is included in a class. It recieves the name of that class as its argument. In this case, we are using that to call the extend method on.

````
module ImageUtils

  def self.included(base)
    base.extend(ClassMethods)
  end

  def preview
  end

  module ClassMethods
    def create_image
    end
  end
end

class Image
  include ImageUtils
end
````

## Using Activesupport concerns

If we add a new class method, and have it called whenever the module is included in a class:

````
module ImageUtils

  def self.included(base)
    base.extend(ClassMethods)
    base.clean_up
  end

  def preview
  end

  module ClassMethods
    def create_image
    end

    def clean_up
    end
  end
end

class Image
  include ImageUtils
end
````

We can clean this up by using an ActiveSupport concern:

````
require 'active_support/concern'
module ImageUtils

  # This looks for a module named ClassMethods and extends them for us
  extend ActiveSupport::Concern

  # It also provides a method where we can call class methods when this module is included:
  included do
    clean_up
  end

  def preview
  end

  module ClassMethods
    def create_image
    end

    def clean_up
    end
  end
end

class Image
  include ImageUtils
end
````



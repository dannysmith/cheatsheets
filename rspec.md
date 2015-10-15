# RSpec Notes

When working with RSpec, we can describe a class like this:

```ruby
describe MyClass do
  context 'Some contect' do
    
    subject(my_class) {described_class.new}

    it 'should do something' end

    end
  end
end
```

The `described_class` method will reference whatever class we are describing at the moment, in this case, `MyClass`. It's worth noting that it's usual to use the variable name subject in tests to describe the subject of a test.

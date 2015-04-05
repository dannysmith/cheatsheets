# Activesupport Notes

`require 'active_support/all'`

## Hash methods
`options.reverse_merge!(defaults)` merges the params hash with the defaults hash, with preference for the defaults one.

`options.diff(defaults)` returns a hash of the difference between the two hashes.

`myhash.symbolize_keys!` will turn all keys into symbols. `.stringify_keys` is also available. These both use the `transform_keys` method under the hood, which takes a block and applies it to ech key in turn. `.deep_symbolize_keys` and it's stringify equivelant are also available. All have a plan and bang option.

`.transform_values(&block)` works the same as transform_keys, but applies to the values.

`options.except(:some_key)` will return everything except the specified key.

`options.asset_valid_keys(:one, :two)` will throw an exception if any keys exist that are not in the list provided.

`.slice(:a, :b, :c)` will return a hash containing only they a, b and c key/value pairs. It can be called with a bang.

`.compact` removes pairs with a value of nil.



## Integer Methods

Activesupport provides `.odd?` and `.even?` methods.

## Array Methods

`[1,2,3].from(2)` returns the array from the specified location to the end. There is an equivelant `.to(i)` method.

`[1,2,3,4].in_groups_of(i)` returns an array containing the original array's items in groups of i. `.in_groups(i)` will return i groups.

`.prepend` and `.append` do the obvious thing.

## Inflector & Helper Methods


`1.ordinalize` retursn `"1st"`. This works on all numbers.

`"cat".pluralize` returns the plural of the string. This works in most cases. The equivilent `.singularize` is also available.

`.titleize` turns a string to titlecase, while `'my_account'.humanize` returns "My account".

`.camelize` and `.underscore` convert strings to and from CamelCase or snake_case. `.dasherize` replaces underscores with dashes. This is useful for appending class name to html.

`.truncate(i, seperator: s)` will truncate a string to i characters, to the nearest seperator s. If s is a space, then it will be to the nearest word.

`.starts_with('x')` and `.ends_with('x')` check if a string starts or ends with the supploed string.

The `.to_s` method takes an optional symbol, so that 1234567.to_s(:delimited) returns `"12,34,567"`. There are a sumber available, including `:rounded` and `:percentage`.

## Date Methods

`.to_time`, `.to_date` and `.to_datetime` convert between Time, Date and DateTime objects.

Methods like `2.years.from_now` and `3.weeks.ago` return the expected times.

# XML and JSON

`.to_xml` will convert a hash into valid XML.



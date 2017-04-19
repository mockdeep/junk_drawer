# JunkDrawer

`JunkDrawer` is a gem providing exactly one (and someday more) random utility
that is commonly useful across projects.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'junk_drawer'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install junk_drawer

## Usage

### JunkDrawer::Callable

`JunkDrawer::Callable` is a module that provides constraints and conveniences
for objects that implement a single method `#call`. It comes with the
philosophy that objects that *do* something, should do only one thing. When
including the `JunkDrawer::Callable` in one of your classes, you will get the
following:

1) It raises an error if you try to implement a public method other than
  `#call`.

  ```ruby
  class Foo
    include JunkDrawer::Callable
    def bar # Bad: can't define public method "#bar"
    end
  end
  ```

  produces:

  ```
  JunkDrawer::CallableError: invalid method name bar, only public method allowed is "call"
  ```

  Private methods are fine:

  ```ruby
  class Foo
    include JunkDrawer::Callable
  private
    def bar # private methods are okay!
    end
  end
  ```

2) It delegates `call` on the class to a new instance:

  ```ruby
  class Foo
    include JunkDrawer::Callable
    def call(stuff)
      puts "I am a Foo! I've got #{stuff}"
    end
  end
  ```

  ```
  > Foo.call('a brochure')
  I am a Foo! I've got a brochure
  > Foo.new.call('a brochure')
  I am a Foo! I've got a brochure
  ```

3) It implements `to_proc`, both on the class and instance, allowing operations
  such as:

  ```
  > ['puppies', 'a cold', 'cheeseburgers'].each(&Foo)
  I am a Foo! I've got puppies
  I am a Foo! I've got a cold
  I am a Foo! I've got cheeseburgers
  ```

  See here for a great explanation of `to_proc` and the `&` operator:

  http://www.brianstorti.com/understanding-ruby-idiom-map-with-symbol/

-------------------------------------------------------------------------------

### JunkDrawer::Notifier

`JunkDrawer::Notifier` is a class that provides simple notification strategies
for different environments. When you call it, it will send a notification via
your selected strategy. The strategies available are as follows:

1) `:raise` raises an error when you call the notifier:

  ```ruby
  JunkDrawer::Notifier.strategy = :raise
  JunkDrawer::Notifier.call('some message', some: 'context')
  ```

  produces:

  ```
  JunkDrawer::NotifierError: some message, context: {:some=>"context"}
  ```

2) `:honeybadger` will send a notification to Honeybadger. You'll need to make
  sure you have Honeybadger required in your application and configured for
  this to work.

3) `:null` is a noop. If you want to disable notifications temporarily, you can
  configure the strategy to `:null`.

If you're using Rails, you may want to configure `Notifier` based on the
environment, so in your `config/environments/development.rb` you might have:

```ruby
config.after_initialize do
  JunkDrawer::Notifier.strategy = :raise
end
```

While in `production.rb` you might want:

```ruby
config.after_initialize do
  JunkDrawer::Notifier.strategy = :honeybadger
end
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run
`rake spec` to run the tests. You can also run `bin/console` for an interactive
prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To
release a new version, update the version number in `version.rb`, and then run
`bundle exec rake release`, which will create a git tag for the version, push
git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at
https://github.com/mockdeep/junk_drawer. This project is intended to be a
safe, welcoming space for collaboration, and contributors are expected to
adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

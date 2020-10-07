# Dynamic ORM with Inheritance

## Objectives

- Explain why it is useful to have an ORM class from which other classes in your
  program can inherit
- Describe the code that is used to construct such a class

## Why Inheritance?

The great thing about a Dynamic ORM is that it provides us with a way to write
an ORM that is almost entirely abstract. In other words, the methods we write
that allow a given Ruby class and instances to communicate with a database are
*not specific to any one class*.

This means we can use such methods again and again. We can define them in only
one place and simply make them available, via inheritance, to any other class in
our program.

## The Super Class

In this repo, in the `lib` directory, you'll see the `interactive_record.rb`
file. Open it up and take a look.

You'll see that the `InteractiveRecord` class contains almost all of the code
responsible for communicating between our Ruby program and our database. All of
the methods defined there are abstract––they do not reference explicit class or
attribute names nor do they reference explicit table or column names.

These are methods that can be used by *any Ruby class or instance*, as long as
we make them available to that class or instance.

## The Child Class

Go ahead and open up `lib/song.rb`. Notice that the `Song` class *inherits* from
`InteractiveRecord`. This means that *all* of the methods defined in
`InteractiveRecord` are available to `Song`.

```rb
require_relative "./interactive_record.rb"

class Song < InteractiveRecord

  self.column_names.each do |col_name|
    attr_accessor col_name.to_sym
  end

end
```

The only code the `Song` class needs to contain is the code to create the
`attr_accessor`s specific to itself. But even that code uses a method,
`#column_names`, inherited from the super class.

## Our Code in Action

Open up the executable file in `bin/run`.

```ruby
song = Song.new(name: "Hello", album: "25")
puts "song name: " + song.name
puts "song album: " + song.album
song.save

puts Song.find_by_name("Hello")
```

Here we create a new `Song` instance. Remember, there was no `initialize` method
defined in the `Song` class itself -- it is inherited from the
`InteractiveRecord` class.

After the `Song` instance is created, information about the song's name and
album is printed out with `puts`. The song instance is then saved to the
database.

Just to make sure everything is working, `Song.find_by_name("Hello")` is used to
search the database for the newly created song. When `ruby bin/run` is run in
your terminal, it should produce the following, confirming that the song was
saved:

```text
song name: Hello
song album: 25
{"id"=>1, "name"=>"Hello", "album"=>"25"}
```

The `#initialize`, `#save` and `#find_by_name` methods used by `Song` here were
inherited from `InteractiveRecord`.

## Looking Ahead

As we begin to build complex web applications using Sinatra and Rails, this
pattern of inheritance will become familiar. In fact, we'll use an ORM tool
called Active Record that handles *all* of the ORM interactions for us, and
we'll simply inherit our Ruby classes from Active Record's classes.

<p class='util--hide'>View <a href='https://learn.co/lessons/dynamic-orm-inheritance'>Dynamic ORMs with Inheritance</a> on Learn.co and start learning to code for free.</p>

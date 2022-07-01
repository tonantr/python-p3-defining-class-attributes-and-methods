# Defining Class Attributes and Class Methods

## Learning Goals

- Use class attributes and methods to write durable and powerful code.
  - Define class attributes and class methods.
  - Understand when, why, and how to use class attributes and class methods.
- Accomplish complex programming tasks using knowledge from previous modules.

***

## Key Vocab

- **Attribute**: variables that belong to an object.
- **Constant**: variable whose value cannot be changed.
- **Instance**: one specific working copy of a class. It is created when a
  class's `__init__` method is called.
- **Class**: a bundle of data and functionality. Can be copied and modified to
  accomplish a wide variety of programming tasks.
- **Static**: an attribute or method that cannot manipulate the class or
  instance it belongs to.
- **Exception**: an error that occurs during the execution of a program.
  Exceptions can be anticipated and handled without disrupting the execution of
  the program.

***

## Introduction

All objects are bundles of data and behavior — or attributes and methods. We
understand this to be true of instances of a class. Each instance contains
attributes or properties to store data, as well as methods that can enact
behaviors.

For example, let's say we have a class, `Album`. Every individual album instance
should have a release date attribute. To accomplish this, we'll define an
instance variable, `self.release_date` that stores and makes this information
available.

```py
class Album:
    def release_date(self, date):
        self.release_date = date
```

Here we have an instance variable, `self.release_date`, which can be accessed
through dot notation on the instance itself:

```py
album = Album()
album.release_date = 1991
album.release_date
# 1991
```

What you might not know, however, is that the **`Album` class itself is also an
object**. If our definition of an object is a bundle of code that contains
attributes and behaviors, then the entire `Album` class itself absolutely
qualifies.

The `Album` class can have its own attributes and methods. We call these class
attributes and class methods.

***

## Why Use Class Variables and Methods

Let's say you wanted to keep a counter for how many albums you had in your music
collection. That way, you can brag to your friends about what a music aficionado
you are. The current code in our `Album` class has no way to keep such a count.
Looks like we will have to write some code to accommodate this new feature of
our program.

When it comes to adding new features or functionalities to our code, we start
out by asking a question: whose responsibility is it to enact this behavior or
functionality?

Right now, our program is pretty simple. We have an `Album` class and we have
album instances. So, is it the responsibility of an individual album to keep a
count of all of the other albums? Or is it the responsibility of the `Album`
class, which actually produces the individual albums, to keep a running count? I
think we can agree that it _isn't_ the job of the individual albums, but the job
of the `Album` class to keep a count of all of the instances it produces.

Now that we've decided whose job it is to enact the "keep a count of all albums"
behavior, we can talk about _how_ we enact that behavior.

We do so with the use of class variables and methods. Our goal is to be able to
ask the `Album` class: "how many albums have you produced?" When we ask an
object to tell us something about itself, we use methods. It would be great if
we could do something like:

```ruby
Album.count
```

...and return the number of existing albums. Let's build out this capability
now.

## Building Class Methods and Using Class Variables

An **instance variable** is responsible for holding information regarding an
_instance_. It is a variable that is available in **scope** for all instance
methods in the class.

A **class variable** is accessible to the entire class — it has **class scope**.
A class method is a method that is called on the class itself, not on the
instances of that class.

Class variables are typically used to store information regarding the class as a
whole and class methods enact behaviors that belong to the whole class, not just
to individual instances of that class.

### Defining a Class Variable

A class variable looks like this: `@@variable_name`. Just like an instance or a
local variable, you can set it equal to any type of data.

Let's create a class variable, `@@album_count` and set it equal to `0`:

```ruby
class Album

  @@album_count = 0

  def release_date=(date)
    @release_date = date
  end

  def release_date
    @release_date
  end
end
```

Great, now we have a class variable to store our count of albums in. We can't
yet access that variable from outside of our class though. How can we expose the
contents of that variable? With a class method.

### Defining a Class Method

A class method is defined like this:

```ruby
def self.class_method_name
  # some code
end
```

Here, the `self` keyword refers to the **entire class itself**, _not to an
instance of the class_. In this case, we are inside the class only, not inside
an instance method of that class. So, we are in the **class scope**, not the
instance scope.

Let's define a class method `.count` that returns the current count of albums.

```ruby
class Album
  @@album_count = 0

  def self.count
    @@album_count
  end
end
```

Great, now if we call:

```ruby
Album.count
```

It will return `0`.

### Operating On a Class Variable From an Instance Method

Currently, however, our `@@album_count` is stuck at `0`. When and how should we
increment it? The count of albums should go up as soon as a new album is
created, or initialized. We can hook into this moment in time in our
`#initialize` method:

```ruby
class Album
  @@album_count = 0

  def initialize
    @@album_count += 1
  end

  def self.count
    @@album_count
  end
end
```

Here we are using the `@@album_count` class variable, inside of our
`#initialize` method, which is an instance method. We are saying: when a new
album is created, access the `@@album_count` class variable and increment its
value by 1.

We can access our class variables anywhere in our class: in both class and
instance methods.

Now our code should behave in the following manner:

```ruby
Album.new
Album.new
Album.new

Album.count
  # => 3
```

If we were to write a similar class definition in JavaScript, here's how it
might look:

```js
class Album {
  static albumCount = 0;

  constructor() {
    Album.albumCount++;
  }

  static count() {
    return this.albumCount;
  }
}

new Album();
new Album();
new Album();

Album.count();
// => 3
```

## Class Constants

One other type of variable that can be useful when building out classes is a
**class constant**. Class constants have a lot in common with class variable.
Both types of variable:

- Are defined in the body of the class
- Can be accessed from within a class method
- Can be accessed from within an instance method

A class constant looks a bit different than a class variable. It's defined using
all capital letters, like so:

```rb
class User
  ROLES = ["Admin", "Moderator", "Contributor"]
end
```

When deciding when to use a class constant or a class variable, the key
distinction is that class constants are used to store data that _doesn't change_
(is constant), while class variables are used to store data that does change.

For example, we could define a list of valid genres for our album class using
a class constant:

```rb
class Album
  GENRES = ["Hip-Hop", "Pop", "Jazz"]

  @@album_count = 0

  def initialize(genre)
    if GENRES.include?(genre)
      @@album_count += 1
    end
  end

  def self.count
    @@album_count
  end
end
```

Scope-wise, class constants can _also_ be accessed from outside of the class
using this syntax:

```rb
Album::GENRES
# => ["Hip-Hop", "Pop", "Jazz"]
```

Unlike in JavaScript, declaring a constant variable in Ruby doesn't actually
prevent the variable from being reassigned (though it will give a warning
message):

```rb
Album::GENRES = "not an array anymore"
# warning: already initialized constant Album::GENRES
# => "not an array anymore"
```

However, declaring a variable with a constant is still a good indicator to other
developers that they _shouldn't_ reassign the variable's value.

## Conclusion

So far in our object-oriented Ruby code, we've focused on defining behavior that
is specific to an individual instance of a class using **instance methods** and
**instance variables**. By also using **class methods**, **class variables**,
and **class constants**, we can expand on our classes' functionality by defining
behavior that's not tied to one particular instance of a class, but is related
more generally to the class itself.

## Resources

- [Python Documentation](https://docs.python.org/3/)
- [Classes - Python](https://docs.python.org/3/)
- [Python Class Attributes: An Overly Thorough Guide - Toptal](https://www.toptal.com/python/python-class-attributes-an-overly-thorough-guide)
- [Python's Instance, Class, and Static Methods Demystified - Real Python](https://realpython.com/instance-class-and-static-methods-demystified/)

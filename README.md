# PatternMatching [![Build Status](https://secure.travis-ci.org/jdantonio/pattern_matching.png)](http://travis-ci.org/jdantonio/pattern_matching?branch=master) [![Dependency Status](https://gemnasium.com/jdantonio/pattern_matching.png)](https://gemnasium.com/jdantonio/pattern_matching)

A gem for adding Erlang-style pattern matching to Ruby classes.

*NOTE: This is a work in progress. I will push to RubyGems.org when it has enough features to be useful in real software.*

## WTF?

[Ruby](http://www.ruby-lang.org/en/) is my favorite programming by far. As much as I love Ruby I've always been a little disappointed that Ruby doesn't support function overloading. Function overloading tends to reduce branching and keep functions signatures simpler. No sweat, I learned to do without. Then I started programming in [Erlang](http://www.erlang.org/)…

I've really started to enjoy working in Erlang. Erlang is good at all the things Ruby is bad at and vice versa. Together, Ruby and Erlang make me happy. My favotite Erlang feature is, without question, [pattern matching](http://learnyousomeerlang.com/syntax-in-functions#pattern-matching). Pattern matching is like function overloading cranked to 11. So one day I was musing on Twitter and one of my friends responded with "Build it!" So I did. And here it is.

## Features

* Basic pattern matching for instance methods.
* Parameter count matching
* Mathing against primitive values
* Matching by class/datatype
* Matching against specific key/vaue pairs in hashes
* Matching against the presence of keys within hashes
* Reasonable error messages when no match is found
* Dispatching to superclass methods when no match is found
* Recursive calls to other pattern matches
* Recursive calls to superclass methods

### To-do

* Variable-length argument lists
* Matching against array elements
* Guard clauses

## Install

    gem install pattern-matching

or add the following line to Gemfile:

    gem 'pattern-matching

and run `bundle install` from your shell.

## Supported Ruby versions

MRI 1.9.x and above. Anything else and your mileage may vary.

## Examples

For more examples see the integration tests in *spec/integration_spec.rb*.

### Simple Function

This example is based on [Syntax in defnctions: Pattern Matching](http://learnyousomeerlang.com/syntax-in-defnctions) in [Learn You Some Erlang for Great Good!](http://learnyousomeerlang.com/).

Erlang:

    greet(male, Name) ->
      io:format("Hello, Mr. ~s!", [Name]);
    greet(female, Name) ->
      io:format("Hello, Mrs. ~s!", [Name]);
    greet(_, Name) ->
      io:format("Hello, ~s!", [Name]).

Ruby:

    require 'pattern_matching'

    class Foo
      include PatternMatching
  
      defn(:greet, _) do |name|
        "Hello, #{name}!"
      end
  
      defn(:greet, :male, _) { |name|
        "Hello, Mr. #{name}!"
      }
      defn(:greet, :female, _) { |name|
        "Hello, Ms. #{name}!"
      }
      defn(:greet, _, _) { |_, name|
        "Hello, #{name}!"
      }
    end

### Simple Function with Overloading

This example is based on [Syntax in defnctions: Pattern Matching](http://learnyousomeerlang.com/syntax-in-defnctions) in [Learn You Some Erlang for Great Good!](http://learnyousomeerlang.com/).

Erlang:

    greet(Name) ->
      io:format("Hello, ~s!", [Name]).

    greet(male, Name) ->
      io:format("Hello, Mr. ~s!", [Name]);
    greet(female, Name) ->
      io:format("Hello, Mrs. ~s!", [Name]);
    greet(_, Name) ->
      io:format("Hello, ~s!", [Name]).

Ruby:

    require 'pattern_matching'

    class Foo
      include PatternMatching
  
      defn(:greet, _) do |name|
        "Hello, #{name}!"
      end
  
      defn(:greet, :male, _) { |name|
        "Hello, Mr. #{name}!"
      }
      defn(:greet, :female, _) { |name|
        "Hello, Ms. #{name}!"
      }
      defn(:greet, nil, _) { |name|
        "Goodbye, #{name}!"
      }
      defn(:greet, _, _) { |_, name|
        "Hello, #{name}!"
      }
    end

### Matching by Class/Datatype

Ruby:

    require 'pattern_matching'

    class Foo
      include PatternMatching
  
      defn(:concat, Integer, Integer) { |first, second|
        first + second
      }
      defn(:concat, Integer, String) { |first, second|
        "#{first} #{second}"
      }
      defn(:concat, String, String) { |first, second|
        first + second
      }
      defn(:concat, Integer, _) { |first, second|
        first + second.to_i
      }
    end

### Matching a Hash Parameter

Ruby:

    require 'pattern_matching'

    class Foo
      include PatternMatching

      defn(:hashable, {foo: :bar}) { |opts|
        # matches any hash with key :foo and value :bar
        :foo_bar
      }
      defn(:hashable, {foo: _}) { |opts|
        # matches any hash with :key foo regardless of value
        :foo_unbound
      }
      defn(:hashable, {}) { |opts|
        # matches any hash
        :unbound_unbound
      }
    end

# ruby-pitfalls

An evolving list of language pitfalls when writing ruby code.

## Rescue without explicit `Exception` doesn't catch all exceptions, but `ensure` runs

The syntax:

    begin
      ...
    rescue
      ...
    ensure 
      ...
    end
    
    
Won't catch all exceptions. Most notably, it will fail to catch `LoadError` and `SyntaxError` exceptions. However, `ensure` will run regardless. For example, this means that finalization code will run even if a `require` statement failed due to a missing file. 

To catch all exceptions use `rescue Exception` (or with a binding name, `rescue Exception => e`). 

## Calling a super method may lose information

If a subclass defines a method with the same name but different signature - less arguments - than the base class, calling "super" to invoke the base class' method will not pass the missing argument's information.


## {} and do..end are not equivalent

Curly braces {} bind more tightly than do..end, so they are not syntactically equivalent. 
Quote from http://stackoverflow.com/a/5587399/562906 (by [David Brown](http://stackoverflow.com/users/185171/david-brown))


> From Programming Ruby:

> > Braces have a high precedence; do has a low precedence. If the method invocation has parameters that are not enclosed in parentheses, the brace form of a block will bind to the last parameter, not to the overall invocation. The do form will bind to the invocation.

> So the code

> `f param {do_something()}`

> Binds the block to the param variable while the code

> `f param do do_something() end`

> Binds the block to the function f.

> However this is a non-issue if you enclose function arguments in parenthesis.

## Return statement is ignored in setter methods (with '=' suffix) 

*Credit: shevy from #ruby on freenode*

    > class Foo; def bla=(i) ; return 33; i; end; end
     => :bla= 
    > foo = Foo.new
     => #<Foo:0x000000017458c8> 
    > foo.bla = 9
     => 9 

See https://jeremy.wordpress.com/2010/09/03/assignment-like-methods-and-the-returned-value-in-ruby/ 


## Space between method name and parenthesis

method (arg) is not the same as method(arg)

    > def f(a) a * a end
    => nil
    > f 2
    => 4
    > f(2)
    => 4
    > f (2)+1
    => 9
    > f(2)+1
    => 5

## Many built-in objects are mutable

For example, a contribed example involving `nil`:
  
    def nil.+­(x)
      return 59+x
    end
  
Later:
  
    nil + 3
    # evaluates to 62...
  
Or worse:
  
    class Shoko­
      def howMany
        return @x
      end
    end
    
    Shoko.new.howMany­
    # returns nil
    
    Shoko.new.­howMany + 3
    # evaluates to 62
    
    
## Extending builtin type instances which are immediate values

*Credit: shevy from #ruby on freenode*

    > x = 'b'
    => "b" 
    > def x.moo; return 3; end
    => :moo 
    > x.moo
    => 3 

This works when `x` is a string, but not when `x` is a number of other builtin types:
    
    > x = 2
     => 2 
    > def x.moo; return 9; end
    TypeError: can't define singleton


Explanation (quoting J. Ryan Sobol from https://www.ruby-forum.com/topic/50170#16763):

> Quoting http://www.rubygarden.org/faq/entry/show/83 :
> > "Fixnums, Symbols, true, nil, and false are implemented as immediate values. With immediate values, variables hold the objects themselves, rather than references to them.
> > Singleton methods cannot be defined for such objects. Two Fixnums of the same value always represent the same object instance, so (for example) instance variables for the Fixnum with the value "one" are shared between all the "ones" is the system. This makes it impossible to define a singleton method for just one of these."

# Rails Pitfalls

`ActiveRecord::Relation`'s [`update` method](http://apidock.com/rails/ActiveRecord/Relation/update) doesn't report errors or throw exceptions:

> The resulting object is returned whether the object was saved successfully to the database or not.




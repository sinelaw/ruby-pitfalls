# ruby-pitfalls

An evolving list of language pitfalls when writing ruby code.


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
    
    

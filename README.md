# ruby-pitfalls

An evolving list of language pitfalls when writing ruby code.


## Calling a super method may lose information

If a subclass defines a method with the same name but different signature - less arguments - than the base class, calling "super" to invoke the base class' method will not pass the missing argument's information.


## {} and do..end are not equivalent

Curly braces {} bind more tightly than do..end, so they are not syntactically equivalent. See http://stackoverflow.com/a/5587399/562906.

## Space between method name and parenthesis

method (arg) is not the same as method(arg)

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
  
  

## Code Smell 147 - Too Many Methods 

 *Util classes are great to gather protocol*

> TL;DR: Don't add accidental protocol to your classes

# Problems

- Readability

- Single Responsibility Violation

- Bad Cohesion

- High Coupling

- Low Reusability

# Solutions

1. Break your class

2. [Extract Class](https://maximilianocontieri.com/refactoring-007-extract-class)

# Related Refactorings

%[https://maximilianocontieri.com/refactoring-007-extract-class]

# Context

We tend to put a protocol in the first class we find.

That's not a problem.

We just need to refactor.

# Sample Code

## Wrong

[Gist Url]: # (https://gist.github.com/mcsee/d1c326e90aa2feba4746c6e019999312)
```java
public class MyHelperClass {
  public void print() { }
  public void format() { }
  // ... many methods more

  // ... even more methods 
  public void persist() { }
  public void solveFermiParadox() { }      
}
```

## Right

[Gist Url]: # (https://gist.github.com/mcsee/c64e13c3ea97620ce02dab73ffc517b2)
```java
public class Printer {
  public void print() { }
}

public class DateToStringFormater {
  public void format() { }
}

public class Database {
  public void persist() { }
}

public class RadioTelescope {
  public void solveFermiParadox() { }
}   
```

# Detection

[X] Automatic 

Most linters count methods and warn us.

# Relations

%[https://maximilianocontieri.com/code-smell-124-divergent-change]

%[https://maximilianocontieri.com/code-smell-143-data-clumps]

%[https://maximilianocontieri.com/code-smell-94-too-many-imports]

%[https://maximilianocontieri.com/code-smell-22-helpers]

%[https://maximilianocontieri.com/code-smell-34-too-many-attributes]

# More info

[Refactoring Guru](https://refactoring.guru/smells/large-class)

# Tags

- Cohesion

- Bloaters

# Conclusion

Splitting classes and protocol is a good practice to favor small and reusable objects.

# Credits

Photo by [Marcin Simonides](https://unsplash.com/@cinusek) on [Unsplash](https://unsplash.com/s/photos/full)  

* * *

There is no code so big, twisted, or complex that maintenance can't make it worse.

Gerald M. Weinberg
 
%[https://maximilianocontieri.com/software-engineering-great-quotes]

* * *

This article is part of the CodeSmell Series.

%[https://maximilianocontieri.com/how-to-find-the-stinky-parts-of-your-code]
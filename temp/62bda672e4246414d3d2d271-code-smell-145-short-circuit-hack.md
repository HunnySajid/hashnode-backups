## Code Smell 145 - Short Circuit Hack 

 > TL;DR: Don't use Boolean comparison for side effect functions.

# Problems

- Readability

- Side Effects

# Solutions

1. Convert [short circuit](https://maximilianocontieri.com/code-smell-140-short-circuit-evaluation) into IFs

# Context

Smart programmers like to write hacky and obscure code even when there is no strong evidence for this improvement.

Premature optimization always hurts readability.

# Sample Code

## Wrong

[Gist Url]: # (https://gist.github.com/mcsee/be2e697d71cfb438110d911c9e4751dc)
```javascript
userIsValid() && logUserIn();

// this expression is short circuit
// Does not value second statament
// Unless the first one is true

functionDefinedOrNot && functionDefinedOrNot();

// in some languages undefined works as a false
// If functionDefinedOrNot is not defined does
// not raise an erron and neither runs
```

## Right

[Gist Url]: # (https://gist.github.com/mcsee/5c48bd13ce74f1605cf8d6a8ed2de4d9)
```javascript
if (userIsValid()) {
    logUserIn();
}

if(typeof functionDefinedOrNot == 'function') {  
    functionDefinedOrNot();
}
// Checking for a type is another code smell
```

# Detection

[X] Semi-Automatic 

We can check if the functions are impure and change the short circuit to an IF.

Some actual linters warn us of this problem

# Tags

- Premature Optimizacion

# Conclusion

Don't try to look smart. 

We are not in the 50s anymore.

Be a team developer.

# Relations

%[https://maximilianocontieri.com/code-smell-140-short-circuit-evaluation]

%[https://maximilianocontieri.com/code-smell-06-too-clever-programmer]
 
# Credits

Photo by Michael Dziedzic on Unsplash

* * *

> A computer is a stupid machine with the ability to do incredibly smart things, while computer programmers are smart people with the ability to do incredibly stupid things. They are, in short, a perfect match.

_Bill Bryson_
 
%[https://maximilianocontieri.com/software-engineering-great-quotes]

* * *

This article is part of the CodeSmell Series.

%[https://maximilianocontieri.com/how-to-find-the-stinky-parts-of-your-code]
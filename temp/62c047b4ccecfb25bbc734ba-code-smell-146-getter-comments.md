## Code Smell 146 - Getter Comments 

 > TL;DR: Don't use getters. Don't comment getters

# Problems

- Comment Abusers

- Readability

- Getters

# Solutions

1. Remove getter comments

2. Remove getters

# Context

A few decades ago, we used to comment on every method. Even trivial ones

Comment should describe only a critical design decision.

# Sample Code

## Wrong

[Gist Url]: # (https://gist.github.com/mcsee/29cd4411aa32467291998e467e6ef503)
```solidity
pragma solidity >=0.5.0 <0.9.0;

contract Property {
    int private price;   

    function getPrice() public view returns(int) {           
           /* returns the Price  */

        return price;
    }
}
```

## Right

[Gist Url]: # (https://gist.github.com/mcsee/bf1ab1d44b078d797796d19554032591)
```solidity
pragma solidity >=0.5.0 <0.9.0;

contract Property{
    int private _price;   

    function price() public view returns(int){        
        return _price;
    }
}
```

# Detection

[X] Semi-Automatic

We can detect if a method is a getter and has a comment. 

# Exceptions

The function needs a comment, that is accidentally a getter and the comment is related to a design decision

# Tags

- Comments

# Conclusion

Don't comment getters. 

They add no real value and bloat your code.

# Relations

%[https://maximilianocontieri.com/code-smell-05-comment-abusers]

%[https://maximilianocontieri.com/code-smell-68-getters]

%[https://maximilianocontieri.com/code-smell-01-anemic-models]

# Credits

Photo by Reimond de Zuñiga on Unsplash

* * *

> Code should be remarkably expressive to avoid most of the comments. There'll be a few exceptions, but we should see comments as a 'failure of expression' until proven wrong.

_Robert Martin_

%[https://maximilianocontieri.com/software-engineering-great-quotes]

* * *

This article is part of the CodeSmell Series.

%[https://maximilianocontieri.com/how-to-find-the-stinky-parts-of-your-code]
# -functional Object-Oriented System in Scheme
copied from http://okmij.org/ftp/Scheme/#pure-oo

site index page http://okmij.org/ftp/ indicates 'Unless specified otherwise, all the code and the documentation on this site is in public domain'



# Purely-functional Object-Oriented System

 	
The present code implements a classless, delegation-based OO system, similar to those of Self or Javascript. This is a full-fledged OO system with encapsulation, object identity, inheritance and polymorphism. It is also a purely functional system: there is not a single assignment or other mutation in the code below.

Objects' identity is decided by an eq? predicate applied to the result of an identity message. A set-x method returns an object with a new state, but with the same identity as the source object. An object in a changed state is in a sense a "child" of the original object. No wonder implementations of "mutation" and inheritance are so similar in this OO system. 
 

## Version
 	The current version is 1.2, February 29, 2000.

## References
* pure-oo-system.scm [6K]
  The source code, with comments and illustrative tests.
* A USENET article [plain text file](http://okmij.org/ftp/Scheme/oop-in-fp.txt)
  that discusses implementation of objects as functions (closures) in a non-pure and pure functional languages.

## From other archives
 	
comp.lang.smalltalk, comp.lang.functional, comp.lang.scheme newsgroups
an article "Re: FP, OO and relations. Does anyone trump the others? " posted on Wed, 29 Dec 1999 05:13:58 GMT

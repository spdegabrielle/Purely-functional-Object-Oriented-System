'functional Object-Oriented System in Scheme' copied from <http://okmij.org/ftp/Scheme/#pure-oo>

The site index page <http://okmij.org/ftp/> states 'Unless specified otherwise, all the code and the documentation on this site is in public domain'

---

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


---

USENET article [plain text file](http://okmij.org/ftp/Scheme/oop-in-fp.txt) from <http://okmij.org/ftp/Scheme/oop-in-fp.txt>:

    From www@deja.com Wed Dec 29 05:11:14 GMT 1999
    Message-ID: <84c4qe$48j$1@nnrp1.deja.com>
    From: oleg@pobox.com
    Subject: Re: FP, OO and relations. Does anyone trump the others?
    Date: Wed, 29 Dec 1999 05:13:58 GMT
    Reply-To: oleg@pobox.com
    Newsgroups: comp.lang.smalltalk,comp.lang.functional,comp.lang.scheme
    References: <841ffi$pt9$2@bgtnsc01.worldnet.att.net> <slrn86ff4e.k6.qrczak@qrnik.knm.org.pl>
    X-Article-Creation-Date: Wed Dec 29 05:13:58 1999 GMT
    Status: OR

It is actually rather easy to implement an OO system in a functional
language.

According to the classical definition,
"An object has state, behavior, and identity; the structure and
behavior of similar objects are defined in their common class; the
terms instance and object are interchangeable".

[See the OO FAQ, <http://www.cyberdyne-object-sys.com/oofaq2/> ]

Let's consider behavior, which is often understood as an ability to
send an object a message to which it replies. To put in other terms,
we invoke a dispatcher function of the object and pass it an indicator
of a message along with other parameters. The result of that function
is the reply. As far as the external world is concerned, this
dispatcher function is the representation of the object -- it *is* the
object. Well, an object has to encapsulate and maintain a state --
thus the dispatcher function should be a closure. The identity aspect
depends: if the dispatcher function is a pure function, or if it can
mutate its own state ("closed" variables) in response to some
messages.

In 1992 Ken Dickey wrote a fascinating paper "[Scheming with Objects](ftp://ftp.cs.indiana.edu/pub/scheme-repository/doc/pubs/swob.txt)"
<ftp://ftp.cs.indiana.edu/pub/scheme-repository/doc/pubs/swob.txt>
It starts as follows
"There is a saying--attributed to Norman Adams--that 'Objects are a
poor man's closures.' In this article we discuss what closures are and
how objects and closures are related, show code samples to make these
abstract ideas concrete, and implement a Scheme Object System which
solves the problems we uncover along the way."

His paper really implements an OO system in Scheme. It relies heavily
on custom syntax forms though, to make definitions of objects and
classes as familiar as possible. Listing 1 (below) shows a far lighter
version. It deliberately uses only most basic Scheme constructions. It
implements a delegation-based OO system (as found in Self and
Javascript).
In Listing 1, the closures are not pure (they can mutate their own
state). However, one can just as easily implement the same OO system
in a pure functional language: Listing 2. Well, it's Scheme again, its
purely functional subset. Listing 1 is a part of a post (which I
didn't get around to finish) about contra-variance and why it is
important.

Note that Listing 2 demonstrates a fully-fledged OO system with
encapsulation, object identity, inheritance and polymorphism. And no
assignments!

It is possible to implement a two-level OO system, with classes and
instances, in FP. CLOS is a very advanced example of a OO system with
generic functions and multi-methods.

In article <slrn86ff4e.k6.qrczak@qrnik.knm.org.pl>,
qrczak@knm.org.pl (Marcin 'Qrczak' Kowalczyk) wrote:
    > How do we define that a paradigm can model another one? After all
    > one can write an interpreter of any real language using any other
    > Turing-complete language. We don't want to say that the C language
    > models functional and OO paradigms only because compilers of
    functional
    > and OO languages use C as their target languages.
    >
    > We want the translated programs to "integrate" well enough with the
    > implementing language. I'm afraid that it must be very informal...

Matthias Felleisen wrote a paper exactly on that subject:
"On the Expressive Power of Programming Languages"
[ <http://www.cs.rice.edu/CS/PLT/Publications/> ] He came up
with an interesting, formal definition that lets you tell
when one language is strictly more expressive than the other.
He _proved_ for example that a call-by-value lambda-calculus
is strictly less expressive than call-by-name lambda-calculus
(if I remember correctly).

Listing 1. A trivial delegation-based OO system (with encapsulation
and inheritance). Object's identity is decided by eq? predicate. A
set-x! message changes object's state but does not affect its
identity.

    (define (make-point-2D x y)
      (define (get-x) x)
      (define (get-y) y)
      (define (set-x! new-x) (set! x new-x))
      (define (set-y! new-y) (set! y new-y))
      (lambda (selector . args)     ; a dispatcher
          (case selector
            ((get-x) (apply get-x args))
            ((get-y) (apply get-y args))
            ((set-x!) (apply set-x! args))
            ((set-y!) (apply set-y! args))
            (else (error "don't understand " selector)))))
    
    (define (make-point-3D x y z)
                    ; that is, point-3D _inherits_ from point-2D
      (let ((parent (make-point-2D x y)))
        (define (get-z) z)
        (define (set-z! new-z) (set! z new-z))
        (lambda (selector . args)
          (case selector
            ((get-z) (apply get-z args))
            ((set-z!) (apply set-z! args))
                    ; delegate everything else to the parent
            (else (apply parent (cons selector args)))))))
    
    (define a-point-2D (make-point-2D 5 6))
    (define a-point-3D (make-point-3D 7 8 9))
    (a-point-2D 'get-x) => 5
    (a-point-3D 'get-x) => 7
    (a-point-2D 'set-y! 10)
    (a-point-3D 'set-z! 11)
    (a-point-2D 'get-y) => 10
    (a-point-3D 'get-z) => 11
    (define b-point-2D (make-point-2D 5 10))
    
            ; a-point-2D and b-point-2D currently have the same state
    (equal? (list (a-point-2D 'get-x) (a-point-2D 'get-y))
            (list (b-point-2D 'get-x) (b-point-2D 'get-y))) ==> #t
    
            ; but they are different objects
    (eq? a-point-2D b-point-2D) ==> #f
    
Listing 2. The same but in a pure functional style. In addition, the
following system implements polymorphism. Each message returns a list;
its head is an object with a new state, having processed the message;
the rest of the list are the results of the message if any. Objects
identity is decided by an eq? predicate applied to the result of an
'identify' message. As before, a "set-x" method "changes" object's
state but preserves its identity -- in a manner of speaking, of
course.
[see <http://pobox.com/~oleg/ftp/Scheme/pure-oo-system.scm> ]
    
    I can elaborate further, but it's late and I want to go home...

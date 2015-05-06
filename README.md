GRand
=====

Version 1.1.5

GRand (say Gee-Rand) is a C++ random-number-generation library with a
simple, easy-to-use interface.

C++11 or later required. Not for cryptographic use.

Features
--------

* Very easy to use.
* High-quality random numbers (uses 32-bit Mersenne Twister engine:
  MT19937).
* Generate uniformly distributed integers and floating-point values, and
  boolean values with specified probabilities.
* Interoperable with C++ Standard Library random-number facilities.

Copyright & License
-------------------

`grand.h` and associated files copyright &copy; 2014-2015 Glenn G.
Chappell.

License: MIT. See [`LICENSE`](LICENSE) or
<http://opensource.org/licenses/MIT>.

Files
-----

* `grand.h` -- C++ header. Code that uses the library should `#include`
  this.
* `README.md` -- This file.
* `LICENSE` -- Package license.

Usage
-----

GRand consists of a single C++ header file (`grand.h`), which can be
copied into the source tree of a project. Source files that use the
library should `#include` the header, as usual.

### Class `GRand` & Seeding ###

A random-number generator is encapsulated by an object of class `GRand`.
Distinct objects are separate random-number generators.

A `GRand` object is seeded in its constructor. The default constructor
gives a nondeterministic (unpredictable) seed. A fixed seed gives a
predictable sequence. Pass the seed (an integer) to the constructor.

>     GRand r;      // RNG object; seeded with unpredictable value
>     GRand r2(7);  // Separate RNG; fixed seed for predictable sequence
>     GRand r3(7);  // Same seed, same sequence of random numbers
>     GRand r4(2);  // A different seed gives a different sequence

A `GRand` object may also be seeded later, using member function `seed`.
Arguments are handled as with the constructors.

>     r3.seed();   // Unpredictable seed
>     r4.seed(1);  // Fixed seed

A `GRand` object is typically seeded just once. After that, any number
of random numbers may be generated.

### Random-Number Generation ###

Random numbers are generated primarily through member functions `i`,
`d`, and `b`. These return a random `int`, `double`, and `bool`,
respectively.

#### Random `int` ####

Member function `i` returns a uniformly distributed random `int` in the
range [0, _n_-1], where _n_ is the argument.

>     // Print 5 random values, each in { 0, 1, 2, ..., 99 }
>     for (int k = 0; k < 5; ++k)
>         cout << r.i(100) << endl;
>     
>     // Roll a six-sided die
>     int dieroll = 1 + r.i(6);

#### Random `double` ####

Member function `d` returns a uniformly distributed random `double` in
the range [0, _x_), where _x_ is the argument.

>     double d3 = r.d(3.0);  // double in [0.0, 3.0)

Argument _x_ defaults to `1.0`.

>     double d1 = r.d();     // double in [0.0, 1.0)

#### Random `bool` ####

Member function `b` returns a random `bool` whose probability of being
`true` is given.

>     bool b75 = r.b(0.75);  // bool with 75% chance of being true

The probability defaults to `0.5`.

>     // Flip a coin
>     if (r.b())
>         cout << "HEADS" << endl;
>     else
>         cout << "tails" << endl;

### Shuffling a Sequence ###

A `GRand` object may be passed as the third argument to `std::shuffle`
and `std::random_shuffle`.

>     vector<int> v(1000);
>     std::shuffle(v.begin(), v.end(), r);  // Shuffle v randomly
>
>     std::random_shuffle(v.begin(), v.end(), r);  // Another way

### Use with Standard Library Distributions ###

A `GRand` object satisfies the requirements for a C++11 Uniform Random
Number Generator. Thus it may be used with Standard Library Random
Number Distributions.

>     // Generate random value according to an exponential distribution
>     double ex = std::exponential_distribution<>()(r);

### Raw Values ###

The raw 32-bit values generated internally by the random-number engine
are available using member `operator()`.

>     uint32_t rawrand = r();

### Thread Safety ###

Distinct `GRand` objects share no state; this includes copy-constructed
and copy-assigned objects. Thus the GRand package is thread-safe as long
as no single `GRand` object is accessed simultaneously by distinct
threads.

Therefore, in a multithreaded context, either (1) distinct threads
should use distinct `GRand` objects, or (2) simultaneous access to a
single `GRand` object should be prohibited, e.g., using mutex locking.

### Exceptions ###

Public member functions that take arguments of arbitrary types
(constructor from seed, `seed` [one parameter], `operator()` [one
parameter]) will throw what & when an operation on the argument type
throws. No such exceptions will be thrown if the argument is of a
fundamental type.

If the `GRand` object has been seeded unpredictably (default
constructor, `seed` [no parameters]), then public member functions that
generate random values (`i`, `d`, `b`, `operator()`) may throw the
exception thrown by `std::random_device` on failure. No such exceptions
will be thrown if the `GRand` object has not been seeded unpredictably.

In no other cases will any member function throw.

Whenever an exception is thrown, the Strong Guarantee holds, as long as
it holds for the type that originated the exception.

Notes
-----

Beginning with version 1.1.1, the GRand package uses semantic versioning
([SemVer 2.0.0](http://semver.org/spec/v2.0.0.html)).


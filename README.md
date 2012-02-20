Fixes needed to get lambdabot working on ghc 7.4.1

As of Mon Feb 20 15:04:25 MST 2012

* Add TypeOperators to extensions in IOSpec.cabal
* Add appropriate Eq and Show constraints to the following "numbers" files:
    * Dif.hs
    * Symbolic.hs
* Add FlexibleInstances to extensions in lambdabot.cabal

#Install lambdabot anyways
**Until these libs get fixed, here's how you can install lambdabot**

First, start off the normal way

    $ cabal update && cabal instal lambdabot

You'll likely run into issues.

##Fixing IOSpec

Now that everything *except* the problematic libraries are installed,
do a cabal install again to see which one fails first.

    $ cabal install lambdabot

For me, it was `IOSpec` first. So let's fix that.

    $ cabal unpack IOSPec
    $ cd IOSpec-0.2.4
    $ cabal configure
    $ cabal build

This will again reitrate the problem with IOSpec,
assuming a newer version has not yet been posted.
If this doesn't produce errors for you,
then skip this section.

    Illegal operator ...
      Use -XTypeOperators to allow operators in types

I love it when ghc knows exactly how to fix these problems!
Try replacing `IOSpec.cabal` with the one from this repo.
I've added `TypeOperators` to the list of Extensions.

    $ wget -O IOSpec.cabal https://raw.github.com/DanBurton/lambdabot-remedies/master/IOSpec.cabal
    $ cabal configure
    $ cabal build

Assuming no build errors this time, go ahead and install the library.

    $ cabal install
    $ cd ..

##Fixing readline

All right, on to the next one.

    $ cabal install lambdabot

The next problem I ran into was with `readline`.
I'm a bit of a Debian newb,
so after some guidance from #haskell irc,
I found this was all I needed to resolve that matter:

    $ sudo apt-get install libreadline6-dev

##Fixing numbers

Onwards!

    $ cabal install lambdabot

The next issue I had was with the `numbers` package.
You see, ghc 7.4.1 *removed* the `Eq` and `Show` constraints
from the `Num` class.
This package depended on those implicit constraints in some places,
so let's fix that.

    $ cabal unpack numbers
    $ cd numbers-2009.8.9
    $ cabal configure
    $ cabal build

The errors are usually along these lines:

    Could not deduce (Eq a) arising from the literal 0
    .. Possible fix: add (Eq a) to the context of the instance declaration

Again, ghc error messages ftw.
Working through `Dif.hs` and `Symbolic.hs` was tedious,
which is what inspired me to post my modified fies
for the benefit of all.

    $ cd Data/Number
    $ wget -O Dif.hs https://raw.github.com/DanBurton/lambdabot-remedies/master/Dif.hs
    $ wget -O Symbolic.hs https://raw.github.com/DanBurton/lambdabot-remedies/master/Symbolic.hs
    $ cd ../..
    $ cabal configure
    $ cabal build

Assuming no errors this time, go ahead and

    $ cabal install
    $ cd ..

##Fixing cabal install

Now then, I ran into no errors in dependencies
besides the ones I've listed.
The last error was in `lambdabot` itself:

    $ cabal unpack lambdabot
    $ cd lambdabot-4.2.3.2
    $ cabal configure
    $ cabal build

    Illegal instance declaration ..
      Use -XFlexibleInstances if you want to disable this.

ghc suggestions strike again!
Modify `lambdabot.cabal` yourself...or for the lazy

    $ wget -O lambdabot.cabal https://raw.github.com/DanBurton/lambdabot-remedies/master/lambdabot.cabal
    $ cabal configure
    $ cabal build

No errors?

    $ cabal install
    $ cd ..

Congratulations, you've got yourself a shiny new build of
lambdabot, installed with ghc 7.4.1!

##Thanks!

Special thanks to the many lambdabot contributors over the years.
Here's hoping for a clean `cabal install lambdabot`
very soon! (What are the chances of switching from
readline to haskeline?)

<hr />

danburton.email@gmail.com

(c) 2012 Dan Burton.

I release my additions to these files under
whatever license those files were originally released under.
(Probably BSD3)

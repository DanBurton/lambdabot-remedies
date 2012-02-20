Fixes needed to get lambdabot working on ghc 7.4.1

As of Mon Feb 20 15:04:25 MST 2012

* Add TypeOperators to extensions in IOSpec.cabal
* Add appropriate Eq and Show constraints to the following "numbers" files:
    * Dif.hs
    * Symbolic.hs
* Add FlexibleInstances to extensions in lambdabot.cabal
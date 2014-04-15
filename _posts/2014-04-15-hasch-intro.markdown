---
layout: post
title:  "Crypto-Hashing for edn on Clojure and ClojureScript"
date:   2014-04-15
categories: clojure clojurescript
---

# announcing *hasch*

I just want to briefly announce [hasch](http://github.org/hasch), a
small library to consistently crypto-hash edn in Clojure and
ClojureScript. Practically this means you can create a UUID5 from edn on
either platform consistently (patches for CLR or other Clojure hosts
welcome). Compared to just hashing a printed edn-string it respects maps
and sets as commutative and uses bitwise XOR to achieve the same
hash-value independant of order.

{% highlight clojure %}

(uuid ["hello world" {:a 3.14} #{42} '(if true nil \f)]) 
=> #uuid "18f594a3-be77-5449-a0b5-b71552e1d37a"

{% endhighlight %}

We are using it in a cross-platform prototype-level
[repository system](https://github.com/ghubber/geschichte) and it works
out fine so far, but should not yet be considered production-ready as I
want to get some feedback first and fix the following issue: I am not
yet sure whether to extend Clojure protocols or only collections (this
is how it is done in cljs atm.), as both can be considered edn at
runtime, but may not satisfy value semantics (e.g. mutable arrays).

# cryptography

I am also not a cryptography expert, I tried to get all bits of the
edn-data into sha-1 and trust it to avoid collisions then. You can also
use a different hashing algorithm and create similar UUIDs (but this
doesn't satisfy the UUID standard yet).  Our UUID hash is internally
versioned, so it can be fixed 4 times (before hopefully UUID5 will be
superseeded anyway). Don't consider it stable yet, but only after the
next release and possible feedback.

# future plans

One design idea is to be able to hash nested collections first with the
hash-function before they contribute to the overall hash-value, allowing
you to break them out and store them under their nested hash-value,
potentially building persistent data-structures which can share values
without needing to rehash all data on changes. This is not used yet.

In general we want to rely on it to build a distributed database.

# details

I have ported Utf-8 encoding from JavaScript to ClojureScript and you
can also create random UUIDs with:

{% highlight clojure %}

(uuid) 
=> #uuid "a948572d-4e2a-466d-9963-740824526e7e"

{% endhighlight %}


If you have ideas or find any flaws, please open an issue or leave a
comment. Patches are welcome!

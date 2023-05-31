# Lib

Growing collection of Convex Lisp tools and libraries, currently focused on
Off-Chain concerns as opposed to Smart Contracts.

- Helpers for managing clients connected to Convex peers
- Helpers for starting Convex Peers
- Running different kind of Convex networks
- Simulation scenarios for load testing against a Convex network
- Stress testing for Etch, the database for Convex data
- Simple math utilities

Find below some quick, key examples.


## Client pools

Especially for testing situations, it is sometimes required creating a pool of
clients connected to Convex peers.

E.g. Pool of 10 clients connected to 2 peers (5 each):

```clojure
{:deploy [$.client.pool (lib client pool)]}

(def client+
     ($.client.pool/start 10
                          [{:host "123.42.42.321"}
                           {:host "localhost"}]))

($.client.pool/close client+)
```


## Etch stress testing

E.g. Stress test generating at least 10GB of tuples of 4 Longs:

```clojure
{:deploy [$.etch.stress      (lib etch stress)
          $.etch.stress.long (lib etch stress long)]}

($.etch.stress/run $.etch.stress.long
                   {:n.long           4
                    :write.size.total 10e9})
```

E.g. Stress test generating at least 10GB of random Convex data:

```clojure
{:deploy [$.etch.stress     (lib etch stress)
          $.etch.stress.gen (lib etch stress gen)]}

($.etch.stress/run $.etch.stress.gen
                   {:write.size.total 10e9})
```
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


## Statistical functions

Only a handful at the moment, will grow over time.

E.g:

```clojure
{:deploy [$.math.stat (lib math stat)]}

($.math.stat/stddev [1 2 3 4 5]
                    {:type :population})
```


## Starting Peers

The Convex Shell provides all the fundamentals for running Convex peers and this
repository provides some helpers for making that experience more accessible.

E.g. Start a Genesis Peer to bootstrap a network:

```clojure
{:deploy [$.peer (lib peer)]}

(def key-pair
     (.kp.create))

(def peer
     ($.peer/start (.kp.seed key-pair)
                   {:dir   "/tmp/peer"
                    :state (.state.genesis {:peer+ [{:host "public IP of this Peer"
                                                     :key  (.kp.pubkey key-pair)}
                                                    {:host "public IP of another Peer"
                                                     :key  0x001122...}]})}))

(.peer.stop (:peer peer))

($.peer/resume (.kp.seed key-pair)
               {:dir "/tmp/peer"})
```

E.g. Start a Peer that will sync against the Genesis Peer above, using another Shell,
     commonly running on another machine:

```clojure
{:deploy [$.peer (lib peer)]}

;; Key pair must match the public key of that Peer provided in the Genesis State above.
;
(def key-pair
     (.kp.create 0x424242...))

(def peer
     ($.peer/start.sync (.kp.seed key-pair)
                        {:remote.host "public IP of Genesis Peer above"}))
```


## Local networks

Running test networks of N peers on a single machine for test purposes,
each peer running in its own process. Built on the Peer tooling exposed in the
previous example.

E.g. Network of 5 peers with default options:

```clojure
{:deploy [$.net.local (lib net local)]}

;; Assumes a Convex Shell can be started with `cvx`.
;
(def net
     ($.net.local/start "net"
                        {:dir "/tmp/dir"}))

;; Connects a REPL to the network for transacting using Account #12
;
($.net.local/repl.client net
                         #12)

;; Stop the network.
;
($.net.local/stop net)

;; Resumes the network.
;
($.net.local/resume net
                    {:dir "/tmp/dir"})
```

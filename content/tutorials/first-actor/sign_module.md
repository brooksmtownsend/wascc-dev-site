---
title: Sign the Actor Module
linktitle: Sign the Module
toc: true
type: docs
date: "2019-12-09T00:00:00Z"
draft: false
menu:
  first-actor:
#    parent: Example Topic
    weight: 3

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 3
---

In this step, we'll go through the process of signing an actor module. Signing modules is required because the **waSCC** host enforces that no actor can utilize capabilities to which it has not been granted access. 

Signing an actor module involves creating an _account key_ and a _module key_, and then signing the actor with those keys and a list of capabilities that actor will use.

## Install the wash Tool

The `wash`, or **waSCC Shell**, is a CLI that contains multiple utilities used in **waSCC** development. You can download the [wash source](https://github.com/wascc/wash) and compile the binary yourself, or you can install the CLI with cargo:

```shell
$ cargo install wash
```

To verify that the _wash_ tool has installed properly, type `wash` in your shell and you should see output similar to the following:

```shell
$ wash
wash 0.1.0
               __    ___   ___   __ _          _ _
__      ____ _/ _\  / __\ / __\ / _\ |__   ___| | |
\ \ /\ / / _` \ \  / /   / /    \ \| '_ \ / _ \ | |
 \ V  V / (_| |\ \/ /___/ /___  _\ \ | | |  __/ | |
  \_/\_/ \__,_\__/\____/\____/  \__/_| |_|\___|_|_|

A single CLI to handle all of your waSCC tooling needs

USAGE:
    wash <SUBCOMMAND>

FLAGS:
    -h, --help       Prints help information
    -V, --version    Prints version information

SUBCOMMANDS:
    claims     Utilities for generating and managing JWTs for waSCC Actors
    help       Prints this message or the help of the given subcommand(s)
    keys       Utilities for generating and managing keys
    lattice    Utilities for interacting with a waSCC Lattice
```

## Create an Account Key

An [account](/docs/security/accounts/) is an entity that issues a signed JWT (Json Web Token) which is then embedded in the module. In short, the account is a unit of trust. If you trust the account, then, assuming the signature is valid, you trust the module issued by that account.

To create a new account key, enter the following command:

```shell
$ wash keys gen account
Public Key: ABANVDKJYPTJZQTECHRJTTZONTJUWVPEG22T6DKLASAFYZ2DRGPQGJBV
Seed: SAAKPT6OUB3DUPKHGR4BXTTG6SZE5DEXIJ2LBOIF3CTLMOQ5F6FL2FH3NU

Remember that the seed is private, treat it as a secret.
```

Your output will vary since each key is unique.

Later on, you will need both the public key and the seed. Copy the public key to a location that you can reference later.

Copy the `seed` value (note the `SA` prefix) and store it in a file account `account.nk` as we'll need to use this file later when we sign modules. The default directory for `wash` keys is `$HOME/.wash/keys`, and if you put this file there `wash` will be able to find it for you.

## Create a Module Key

An [actor module](/docs/security/modules/) is an entity of execution. It inherits its trust from the trust of of the _issuer_ (the account). Every actor we create gets a public key derived from its seed key.

To create a module key, enter the following command:

```shell
$ wash keys gen module
Public Key: MCYQSR5WIOABHZP6Z3SG67REVC2QDCYAHUVXHUSSLFWNO55OZ3O33MKR
Seed: SMAI2EIJNR3ODSRTGXXBKZWRRU6HHT3CETVBZ246AVWJYZ6GSUIR43A4UE

Remember that the seed is private, treat it as a secret.
```

Later on, you will need both the public key and the seed. Copy the public key to a location that you can reference later.

Copy the `seed` value (note the `SM` prefix) into a file called `module.nk`. This will also be required for signing the module.

## Using wash to sign modules

The `claims` subcommand is used for embedding, extracting, and validating the signatures and capability attestations in actor modules.

## Sign your Module

Signing your module is just a matter of running `wash` with the keys you generated earlier and specifying which capabilities this actor is allowed to use. In our case, the only capability the actor needs is the HTTP server (indicated with the `-s` flag):

```shell
$ wash claims sign target/wasm32-unknown-unknown/debug/hellohttp.wasm hello_signed.wasm -u $HOME/.wash/keys/module.nk -i $HOME/.wash/keys/account.nk -n "Hello World" -s
Successfully signed hello_signed.wasm.
```

This will have produced a new file called `hello_signed.wasm`.

## Examine Module Signature

Before we continue on to the next step, let's take a look at what our signature looks like on the actor module:

```shell
$ wash claims inspect ./hello_signed.wasm 
╔════════════════════════════════════════════════════════════════════════════╗
║                          Hello World - Module                              ║
╠═══════════════╦════════════════════════════════════════════════════════════╣
║ Account       ║   ABANVDKJYPTJZQTECHRJTTZONTJUWVPEG22T6DKLASAFYZ2DRGPQGJBV ║
╠═══════════════╬════════════════════════════════════════════════════════════╣
║ Module        ║   MCYQSR5WIOABHZP6Z3SG67REVC2QDCYAHUVXHUSSLFWNO55OZ3O33MKR ║
╠═══════════════╬════════════════════════════════════════════════════════════╣
║ Expires       ║                                                      never ║
╠═══════════════╬════════════════════════════════════════════════════════════╣
║ Can Be Used   ║                                                immediately ║
╠═══════════════╩════════════════════════════════════════════════════════════╣
║                                Capabilities                                ║
╠════════════════════════════════════════════════════════════════════════════╣
║ HTTP Server                                                                ║
╠════════════════════════════════════════════════════════════════════════════╣
║                                    Tags                                    ║
╠════════════════════════════════════════════════════════════════════════════╣
║ None                                                                       ║
╚════════════════════════════════════════════════════════════════════════════╝
```

This gives us an idea of the kind of power we have in the embedded tokens. We didn't specify an expiration period or a time delay before the module can be used, so the token is valid right now. Note that the public keys for the account and module correspond to the public keys produced by the `wash keys`. 

The most important piece is that the `HTTP Server` capability is the only one our new actor is allowed to use. This means that no matter what code we write or how many messages we attempt to send, the actor module _will not_ be allowed to utilize any other capability.

In the next part of the tutorial, you'll create a host runtime to run this actor.

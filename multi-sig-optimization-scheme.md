## Multi-Sig Optimization Scheme

Some atomic swap models require one of the blockchain to support multi-sig. However, not all blockchain has multi-sig support, such as Nano/RaiBlocks.
Here we propose a way of removing the need for multi-sig support by using Elliptic Curve Cryptography's (ECC) Composite Property.

ECC Composite Property is defined as follow:  
Assume G is the generator point of an ECC group with order n.  
For any private key a,b and its public key aG,bG, the addition of a and b times G equals the ECC addition of its public key.  
```
(a+b)G = aG + bG = ((a+b)mod n) G
```

We will be using step 3) of the post from the link below as an example: https://bitcointalk.org/index.php?topic=1340621.msg13828271#msg13828271

In the third step, Alice pays a altcoins to the contract
```
OP_2 <bob_pub_n> <alice_pub_m> OP_2 OP_CHECKMULTISIG
```
The above contract can be simplified to:
```
<bob_pub_n + alice_pub_m> OP_CHECKSIG
```

In order for Alice to spend money from the above contract, she can compute the private key needed by adding (n modular addition) her private key (alice_priv_n) to Bob's private key which she will get later in the exchange process if Bob cancel the exchange.
Same for Bob, he can compute the private key needed by adding (n modular addition) his private key (bob_priv_n) to Alice's private key which he will get when Alice take his exchange money (not deposit).




## Combined-HASH-SIG


Assume  
The cryptocurrency of Blockchain 1 is COIN1  
The cryptocurrency of Blockchain 2 is COIN2  
Bob wants to exchange 'a' amount of COIN1 for 'b' amount of COIN2.  
Alice wants to exchange 'b' amount of COIN2 for 'a' amount of COIN1.  

In order for 'Combined-HASH-SIG' to work, Blockchain 1 needs to support branching, hashlock and timelock (CLTV or something equaivalent) while Blockchain 2 only need to support multi-sig.

---

1.Bob pays D to
Code:
```
OP_IF
  <now + 2 days> OP_CLTV OP_DROP <alice_pub_1001> OP_CHECKSIG
OP_ELSE
  OP_HASH160 <hash(bob_secret)> OP_EQUALVERIFY <bob_pub_1001> OP_CHECKSIG
OP_ENDIF
```
D should be more valuable than the value of the Bitcoins or altcoins.
This is a promise by Bob to release bob_secret within 2 days (or lose his deposit).

2.Alice pays a altcoins to
```
Code:
OP_IF
  OP_HASH160 <hash(bob_secret)> OP_EQUALVERIFY <alice_pub_1001> OP_CHECKSIG
OP_ELSE
  OP_HASH160 <hash(alice_secret)> OP_EQUALVERIFY <bob_pub_1001> OP_CHECKSIG
OP_ENDIF
```
3.Bob pays b Bitcoins to
Code:
```
OP_IF
  <now + 1 day> OP_CLTV OP_DROP <bob_pub_1002> OP_CHECKSIG
OP_ELSE
   OP_HASH160 <hash(alice_secret)> OP_EQUALVERIFY <alice_pub_key_1001> OP_CHECKSIG
OP_ENDIF
```
Alice has 1 day to claim her output, but that means she must provide alice_secret.
4.Alice spends the output (and provides alice_secret).
5.Bob can spend the altcoin output (since he has both keys)
6.Bob reclaims his deposit

---


This is atomic and it is recoverable if either party refuses to complete a step.

1. Bob refuses to pay deposit.  
Nothing has been sent to either chain, so no harm done.

2. Alice refuses to bail-in.  
Bob reclaims his D after 2 days.   
Releasing bob_priv_n has no effect since there was no bail-in by Alice, so the key is never used.

3. Bob refuses to bail-in  
If Bob reclaims his deposit, then Alice will have both private keys and can reclaim her bail-in.  
If Bob fails to claim his deposit, then Alice gets that as compensation instead of her bail-in.  In that case, Bob loses his deposit and can't claim the altcoins.  This means he should reclaim his output.

4. Alice refuses to claim her Bitcoins  
1 day later Bob can reclaim his Bitcoins  
Alice gets her altcoins back or she gets D instead (if Bob doesn't reclaim his deposit).

5. Bob refuses to claim his altcoins  
He loses his altcoins when he reclaims his deposit.  Alice already has his Bitcoins by this point.  
He has an incentive to complete this step.

6. Bob refuses to claim his deposit  
He loses his deposit and Alice gets it.  
He has an incentive to complete this step.  
For each step, there is either an incentive to complete the step or failure to complete the step causes the protocol to abort.  In an abort, the incentives are to perform a clean abort.


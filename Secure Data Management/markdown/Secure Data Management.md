# Secure Data Management

Why SDM? Data nowadays is everywhere, on the daily basis we have items on newspapers on privacy concerns, data breaches, ransomware...

Data is continuously used for profiling -> increased concern about data (economic value, possibilities to monitor citizen). The whole protection of data is getting more and more attention (also authenticity of data).

-----------

## Algebra Recap

Group is a set of elements on which there is a specif binary operation applicable to its members. There are some requirements to be fulfilled:

- closed
- associative
- identity element e
- contains inverses

an interesting way to limit the size of a group is to work with modulus

subtraction is not commutative

fields are comparable to groups but there are two operators -> mandatory to be commutative. we need identity for each operator (0 -> +, 1 -> *). Distribute operation over each others: how they interact with each other.

## Crypto recap

broadcast encryption: used in systems that are inherently not having an interactive communication (e.g. encrypt info on physical carriers, blue ray etc). 

important element in crypto systems i trust -> assumption you have on participants. 

-----

with the rapid increase of computing power also the key must be extended. 

- mathematical precise description: level of trust (honest, malicious etc)
- description of the attackers class: really important
- description of assumptions (e.g. Diffie Hellman)

Searchable encryption: magic, the whole idea of encryption is that nobody knows what's happening. trap doors <- how to avoid them to be misused etc. we will use to prove protocol security. 

public parameters rise from the key gen phase. the attacker is allowed to generate each keyword and ask for the trap door -> real attack is that after the attacker understands how the trap door works, he sends two keywords and the challenger is generating one trapdoor for one of those then the attacker can say to which original keyword the trapdoor belongs.

if we can prove that epsilon is very small than the attacker is only guessing and he does nt really know how they are constructed.



slide 23 if you can rewrite the proof intor terms that is equal to the formal definition then we can use the inequation to show that epsilon is negiglible and also my protocol.


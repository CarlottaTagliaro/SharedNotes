# SDM 5

## Searching keywords with wildcards on encrypted data

### Multiple Write - Single Reader 

A distributed system with public encryption  allows to have complex environments where a lot of parties interact with a single server. In the scenario we are analyzing, we assume to have several parties writing to a remote server but only one user can read the data. How can we store information and search for it in such a context?

The encrypted document itself is stored in the server and the encrypted trapdoor gives the searchable word to look for in that document. It is encrypted in such a way that it can be used to match the words in the encrypted document (granularity is at document level). If the searchable word is matched into the document then the encrypted document as a whole is returned.

![image-20201011094028682](../images/image-20201011094028682.png)

The setup steps of the algorithm are the same for each approach we have seen so far. Since all the approaches we will tackle during this lecture involve that either the readers or the writers are multiple (or even both), there is the challenge of distributing the keys since we have different parties interacting with the data.

#### Wildcard searches

Through this technique, expressiveness is enhanced. It is an extension of exact keyword searches (i.e. where $w=w’$) and is really handy for several applications since it provides additional flexibility in queries without having to repeat different similar queries. We like more flexibility, e.g. if we look for $ba*$ we want to find documents with keyword $bad$, $bag$ or $bat$ (that share the same leading chars). Thus, the support for a wildcard character (\*) in the search-queries is enabled (note: the $*$ means a single character).

How it works? We use an anonymous IBE (Identity-based Encryption)-like scheme where the encryption keyword is represented as a tuple of symbols $w_1,...,w_n$ (e.g. unique chars). The trapdoor is derived by putting a wildcard $*$ at certain positions in that tuple, $w_1',...,w_n'$. The test is made for each individual position and returns true iff for all $i$ either $w_i′ = w_i$ or $w_i′ =*$.

The way it works is comparable with what seen before: in the encryption phase, we have at least one group element for each symbol (e.g. character) and then for the trapdoor we create the different elements corresponding to the different individual symbols. No terms is created if the corresponding symbol is a wildcard. 

The number of terms and number of encryption elements can be optimized to a certain extent: some elements of the encryption and some of the trapdoors are grouped together (into group elements) to do less comparisons. There won't be anymore a 1-1 correspondence between elements. To solve this new challenge of finding matches in combinations of terms, a sort of hashing functions are introduced. 

ATTENTION: trapdoors for wildcards have no elements!! The slides are wrong!

Let's go more into details:

- encryption keyword: $w_ 1 , ... , w_n ∈ Σ ^n$
- trapdoor keyword: $w_ 1′ , ... , w_n′ ∈ Σ _∗^n$

Let $J ⊂ {1, ... , n}$ denote all the positions that contain a $*$. Now, a polynomial with all the elements in $J$ is built, $\prod_{j\in J} (i-j)$ (all the element in $J$ are roots of this polynomial). This can be rewritten in terms of coefficients and exponentiation as $∑ ^d _{k=1} a_k i^k$ for certain $a_k$ where $d = |J|$. The roots of these two different representations stay the same (at the wildcards positions).

Now, if $w_i′ = w_i$ for $i ∈ J$ then for all $i$ it holds that:
$$
w_i'\prod_{j\in J}(i-j)=w_i\sum_{k=0}^da_ki^k
$$


If we compare a wildcard position, the term on the left will degenerate to 0 (since it's a root), the terms on the right will be 0 but if we do an exponentiation to 0 we get a 1 and we can remove that term in the comparison (is the identity element in multiplication). We don't care if it resembles the same char in the ciphertext or not since it's a wildcard. 

We compute the left and right terms, if the remaining multiplications (the multiplications for the wildcards will be evaluated to 1 hence the identity element for multiplication) are equal at both sides then we have a match.

A random variable $r$ put against collisions. During the trapdoor generation we exclude the elements in $J$ since as previously said, for wildcards we do not generate searchable elements.

Security wise there is some additional overhead. Security is based on Decision Linear Assumptions:
$$
\text{Given} \space g^a,g^b,g^{ac}, g^d \space \text{cannot distinguish} \space \begin{cases}
    Z=g^{b(c+d)}\\
    Z \space \text{random in } G
  \end{cases}
$$
The inverse of the exponentiation is not feasible. The method hides the encrypted keywords until the search takes place. The security in this case is in the *selective* model.

Performance wise is important the number of pairing that needs to be done but the nice thing about this approach is that the number is fixed. 

Hidden Vector Encryption can be used in selective models. 

For comparison with other existing scheme please refer to the tables in the slides: additionally, note that the dimension of the public key (i.e. $n+N+2$) is less than $2n + 2$ given that $N\leq n$. We can limit the size of ciphertext by limiting the amount of allowed wildcards at setup/encryption time.

Additional comparisons for the number of groups (pairings) can be found in the material too. Prime order is always preferred to composite order. Note that the Test-algorithm needs a constant number of pairings, which makes the searching process fast(er).

The proof of security is in the so-called ‘selective’ model but Waters’ Dual System Encryption seems to fit well. Trapdoors reveal the location of wildcards: leaked through the set $J$. If we want to hide that, then additional mechanisms should be adopted. 

----------



### S/M -> SADS

Single Equality Test <- simple expression

main features: different granularities for different users hence we can play with "access rights". 

The scheme is set up with 2 HBC servers intermediaries:

- index server that stores the encrypted indexes and actually does the queries
- query router plays with granularity of accesses

the query router issues the trapdoors to the index server. 

The main issues are correctness, client/server security (what they learn about keywords or the clients itself -> queries must be anonymous), server access control (regulate with servers) and client anonymity (intermediate servers we can play with visibility given towards different servers).

IS -> Information Server

The server shouldn't learn anything about the data. 

Slide 7!

- Re-routable encryption: two servers we have, protect identitied
- Bloom filter: adds efficiency in the system, index structure on data that makes possible to have very fast searches

 TRANSlation -> double encryption

Deterministic to make it fast -> to bring some randomness they used hash functions. Same message encrypted with the same key is the same

in some cases padding might be needed when words have different lengths. 

Bloom filters: use a sort of hash function -> false positives but then the exceptions are checked

### M/M

expressivness more complex

DLDH -> groups and exponentiations, we multiply them can we decide which x and y are summed to give z as result? in PPT it is not possible. 

multiple parties can upload -> upload pk but keep sk hidden. send trapdoors queries and i need to find documents containint that keyword (using the pk).

in Q we can have a list of keywords -> conjunctive search

two words sent and we get only one encrypted back -> additional probing and then we have to make a guess on the keyword we got back -> if the choice was right then we won (should have the same probablity of tossing a coin).  
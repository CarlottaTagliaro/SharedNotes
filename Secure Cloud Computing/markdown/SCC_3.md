# SCC 3

## Attacking SSE and Oblivious RAM

Can the leakage of SSE be exploited to attack such schemes? In the (very) long run, SSE results as secure as deterministic encryption. For each query sent to the service provider, the latter learns the token hence learns the result. As soon as all possible keywords are queried then all result sets (and frequencies) are unveiled.

This problems is recognized by the research community: they tried to define attacks on SSE with the goal of recovering the keywords given the tokens. The idea is that, as soon as an attacker knows which keyword is behind a certain token (and the corresponding result set) then the attacker learns the content of matching documents. The attack strategy is quite straightforward: use background information about the data collection and then mount attacks similar to the ones to PPE but with an incomplete picture (assuming that the attacker has not seen the complete keywords queries yet).

Two possible attacks are present:

- Passive attacks: passive observation of queries and their corresponding results (combining that info with the background info)
- Active attacks: the attacker can inject files in the index and exploit this additional knowledge to extract info in a faster way. 

### Co-Occurrence attacks

The very first attack on SSE is stated in 2011 and the general idea is similar to attacks to PPE. The idea behind **Co-Occurrence attacks** is that instead of counting the frequencies of each individual keyword, we observe the co-occurrence of two keywords. Not all keywords will occur in combination with the same probability. What is define as attack is the probability that two keywords $a,b$ appear in one document: $|D[a] \cap D[b]| \setminus  | D|$

The first step in order to exploit such information is to compute the co-occurrence matrix $B$ for the background info (the keyword universe, assuming the attacker knows the content of the different files). On the diagonal we have the probability that one keyword appears in one document (frequency). Later on, assuming that a subset of all the possible keywords are queried by the client, the co-occurrence matrix $Q$ for query results is calculated. What is observed is that $Q$ contains a permuted sub-set of $B$'s rows: mapping these subsets successfully means attacking the tokens. This problem is however, NP-complete depending on the dimensions on the matrix (in the paper they proposed an heuristic solution). The authors tried to build a similar attack without knowing perfectly the underlying content, however, success rate was pretty low.

Which are the mitigations that can be adopted against such an attack? The result set needs to be modified by omitting results or adding fake entries (typically the second option is preferred since on client side, fake results can be removed without the server observing this post processing). What IKK defined is a $(\alpha,t)$-secure index. The general idea is that result sets are clustered in different partitions and each partition has at least $\alpha$ possible keywords. All result sets in a partition differ in $< t $ file IDs. If we have an $(\alpha,0)$-secure index then the attacker cannot distinguish between $\alpha$ different keywords candidates (they have the same frequencies in the results sets). This is done, as mentioned before, by adding fake results. With that method, they proved attacker success rate to be only 10%.

### File injection attacks

Those type of attacks have been proposed in 2016 and are based on an active attacker. The idea is to inject crafted documents and identify them in query results. 

A very simple example is to inject one document containing only a specific (interesting) keyword so to discover which other documents contain that keyword. The authors proposed an optimization to such an attack and instead of using a single keyword, they combined multiple keywords in a single documents. They encoded keywords by unique result sets (unique binary vector).

Additional research on SSE showed that is possible to alter the research index afterwards. Hence file injection attacks results to be extremely powerful against dynamic SSE schemes. In fact, the user can add encrypted documents even after the initial outsourcing. In most SSE schemes, the search token is deterministic: even search tokens can be attacked after they have been queried (the attacker can keep the token, add new documents with interesting keywords and run again the protocol with the same search token so to extract more valuable information). In this way, the attacker is able to re-identify injected files and reconstruct the underlying keyword.

A research proposed what is called *Forward private SSE*: previous search tokens should not be compatible with encrypted files added after their generation (in such a way the attacker cannot collect different tokens over time).

The idea behind Forward private SSE is to have different versions of search index and keep track of the current version. A new file $F_x$ is added according to current version $v$ using the update token $\mu_{F_x}^v$. Search tokens $\tau_q^o$ of older versions ($o<v$) are incompatible and when a new search operation is performed, then the version is increased. This can be implemented quite easily by using RSA encryption scheme with changed functionality of the keys: the secret key is used to encrypt the index while the public key will be used to decrypt it and go back to the previous history.


# SCC 3

## Attacking SSE and Oblivious RAM

Can the leakage of SSE be exploited to attack such schemes? In the (very) long run, SSE results as secure as deterministic encryption. For each query sent to the service provider, the latter learns the token hence learns the result. As soon as all possible keywords are queried then all result sets (and frequencies) are unveiled.

This problems is recognized by the research community: they tried to define attacks on SSE with the goal of recovering the keywords given the tokens. The idea is that, as soon as an attacker knows which keyword is behind a certain token (and the corresponding result set) then the attacker learns the content of matching documents. The attack strategy is quite straightforward: use background information about the data collection and then mount attacks similar to the ones to PPE but with an incomplete picture (assuming that the attacker has not seen the complete keywords queries yet).

Two possible attacks are present:

- Passive attacks: passive observation of queries and their corresponding results (combining that info with the background info)
- Active attacks: the attacker can inject files in the index and exploit this additional knowledge to extract info in a faster way. 

The very first attack on SSE is stated in 2011 and the general idea is similar to attacks to PPE. The idea behind **Co-Occurrence attacks** is that instead of counting the frequencies of each individual keyword, we observe the co-occurrence of two keywords. Not all keywords will occur in combination with the same probability. What is define as attack is the probability that two keywords $a,b$ appear in one document: $|D[a] \cap D[b]| \setminus  | D|$

The first step in order to exploit such information is to compute the co-occurrence matrix $B$ for the background info (the keyword universe, assuming the attacker knows the content of the different files). On the diagonal we have the probability that one keyword appears in one document (frequency). Later on, assuming that a subset of all the possible keywords are queried by the client, the co-occurrence matrix $Q$ for query results is calculated. What is observed is that $Q$ contains a permuted sub-set of $B$'s rows: mapping these subsets successfully means attacking the tokens. This problem is however, NP-complete depending on the dimensions on the matrix (in the paper they proposed an heuristic solution).
# SCC 3

## Attacking SSE

Can the leakage of SSE be exploited to attack such schemes? In the (very) long run, SSE results as secure as deterministic encryption. For each query sent to the service provider, the latter can store the token together with the result set. As soon as all possible keywords are queried then all result sets (and frequencies) are unveiled.

This problems is recognized by the research community: they tried to define attacks on SSE with the goal of recovering the keywords given the tokens. The idea is that, as soon as an attacker knows which keyword is behind a certain token (and the corresponding result set) then the attacker learns the content of matching documents. The attack strategy is quite straightforward: use background information about the data collection and then mount attacks similar to the ones to PPE (reminder, Property-Preserving Encryption) but with an incomplete picture (assuming that the attacker has not seen the complete keywords queries yet).

Two possible attacks are present:

- Passive attacks, only via passive observation of queries and their corresponding results (combining that info with the background info);
- Active attacks, in which the attacker can inject files in the index and exploit this additional knowledge to extract info in a faster way. 

### Co-Occurrence attacks

The very first attack on SSE was presented in 2011 and the general idea is similar to attacks to PPE. The idea behind **Co-Occurrence attacks** is that instead of counting the frequencies of each individual keyword, we observe the co-occurrence of two keywords. Not all keywords will occur in combination with the same probability. What is defined in the attack is the probability that two keywords $a,b$ appear in one document: $|D[a] \cap D[b]| \setminus  | D|$

The first step in order to exploit such information is to compute the co-occurrence matrix $B$ for the background info (the keyword universe, assuming the attacker knows the content of the different files). On the diagonal we have the probability that one keyword appears in one document (frequency). Later on, assuming that a subset of all the possible keywords are queried by the client, the co-occurrence matrix $Q$ for query results is calculated. What is observed is that $Q$ contains a permuted sub-set of $B$'s rows: mapping these subsets successfully means attacking the tokens. This problem is however, NP-complete depending on the dimensions on the matrix (in the paper they proposed an heuristic solution). The authors tried to build a similar attack without knowing perfectly the underlying content, however, success rate was pretty low.

Which are the mitigations that can be adopted against such an attack? The result set needs to be modified by omitting results or adding fake entries (typically the second option is preferred since on client side, fake results can be removed without the server observing this post processing). IKK defined the concept of $(\alpha,t)$-secure index. The general idea is that result sets are clustered in different partitions and each partition has at least $\alpha$ possible keywords. All result sets in a partition differ in $< t $ file IDs. If we have an $(\alpha,0)$-secure index then the attacker cannot distinguish between $\alpha$ different keywords candidates (they have the same frequencies in the results sets). This is done, as mentioned before, by adding fake results. With that method, they proved attacker success rate to be only 10%.

### File injection attacks

Those type of attacks have been proposed in 2016 and are based on an active attacker. The idea is to inject crafted documents and identify them in query results. 

A very simple example is to inject one document containing only a specific (interesting) keyword so to discover which other documents contain that keyword. The authors proposed an optimization to such an attack and instead of using a single keyword, they combined multiple keywords in a single documents. They encoded keywords by unique result sets (unique binary vector).

Additional research on SSE showed that is possible to alter the research index afterwards. Hence file injection attacks results to be extremely powerful against dynamic SSE schemes. In fact, the user can add encrypted documents even after the initial outsourcing. In most SSE schemes, the search token is deterministic: even search tokens can be attacked after they have been queried (the attacker can keep the token, add new documents with interesting keywords and run again the protocol with the same search token so to extract more valuable information). In this way, the attacker is able to re-identify injected files and reconstruct the underlying keyword.

A research proposed what is called *Forward private SSE*: previous search tokens should not be compatible with encrypted files added after their generation (in such a way the attacker cannot collect different tokens over time).

The idea behind Forward private SSE is to have different versions of search index and keep track of the current version. A new file $F_x$ is added according to current version $v$ using the update token $\mu_{F_x}^v$. Search tokens $\tau_q^o$ of older versions ($o<v$) are incompatible and when a new search operation is performed, then the version is increased. This can be implemented quite easily by using RSA encryption scheme with changed functionality of the keys: the secret key is used to encrypt the index while the public key will be used to decrypt it and go back to the previous history.

The bottom line is that research on SSE is following two goals: finding new schemes, with better security, performance and/or functionality; finding new attacks, exploiting these new functionalities or decreasing the amount of background info required to conduct an attack. The question that pops up is: can we hide even more information for outsourced data?

## Oblivious Random Access Machine

The general idea is to model a simple memory interface. Client A want to outsource data (partitioned in blocks, each identified by a unique address $a_i$) in encrypted form to the service provider. The client can then either read from or write to a specific memory address. This model has been proposed in 1996 about SW protection: small trusted party in CPU unaccessible (hiding how the internal program works). Applied to today's world the trusted environment is the client and the untrusted party is the cloud service provider. 

The server should not be able to learn information about which data is being accessed (access pattern), age of data (and when it was last accessed), whether the same data is being accessed again, of course the content of data etc.

Formally, the previous security goals can be defined as follows. Let $\overrightarrow{y} = ((o_M,a_M,d_M),...,(o_1,a_1,d_1))$ denote a data <u>request</u> sequences where $o_i$ denotes a read\write operation, $a_i$ denotes the address of the block being accessed, $d_i$ denotes the data being written. Let $A(\overrightarrow{y})$ denote the (possibly randomized) sequence of <u>accesses</u> to the remote storage given the sequence of data requests. An ORAM is secure if for any two data sequences $\overrightarrow{y}$ and $\overrightarrow{z}$ of the same length, $A(\overrightarrow{y})$ and $A(\overrightarrow{z})$ are computationally indistinguishable. In simpler words, sequences of accesses to the remote storage should not reveal any information on the requests themselves.

Let's see some of the fundamentals of ORAMs:

- **Virtual address**: the address $a_i$ stated by the client (to define the block he is interested in);
- **Virtual data value**: either a real data value $d_i$ or empty (dummy) block, denoted as $\bot$ (handy when doing read operations, always simulate a write operation but with dummy data);
- **Actual address**: the storage address accessed at server side;
- **Actual value**: the encrypted value stored at server side.

Let's see how to build such a model: we permute all the data blocks that we want to outsource (together with their data addresses), encrypt them and outsource the tuples. Each tuple consists of a virtual address and virtual data.

When the client wants to access a certain block, then they download all tuples, decrypt them, search locally and write (if write operation is performed) or read. Then the client samples a new key, re-encrypt the tuples and upload the new version. Since those two versions are completely different, an attacker is not able to define which type of access has been performed (and the attacker doesn't even see any modification on the data).

This trivial construction is not so useful since communication is linear in data size and also client storage is (then why doesn't the client store everything on his side? There is no point in outsourcing with this implementation). It might be easier to decrease the storage by streaming the data (block per block, but all block are streamed otherwise timings attacks are possible) and block are decrypted individually on client side. When the right block is received, the client access it, performs the operation needed, re-encrypts the data and uploads it again.

A requirement for a useful ORAM is that communication complexity must be sub-linear in data size. But how?

First proposal in 1996: divide the storage in a shelter and virtual memory. The latter contains real values and dummies and each address is accessed at most once. The shelter is a sort of cache for accessed data: after each access, the shelter is updated and streamed completely to the client. The trick is to defined their sizes so that the communication complexity is sub-linear. The shelter must be sublinear in data size since it's streamed completely.

What happens if all virtual memory addresses have been accessed? Or what happens if the shelter is full? The idea is that the virtual memory layout is valid only for one epoch and then it's changed. Every now and then, the whole table is downloaded, re-encrypted (permuted) and uploaded again.

Let's look at the details. Each epoch lasts $\sqrt n$ data access operations. After one epoch is passed, obliviously permute memory and then perform actual data access operations. For the data access operation, read the complete shelter linearly (download, read, re-encrypt and upload again). If the memory block is found in the shelter, then access a dummy element stored in $a_i+cnt$ in the memory layout and download it. Shelter is then re-encrypted linearly and the value is added if it was not in the shelter before. Finally, $cnt= cnt +1$ (this $cnt$ is used for non predictability). If instead the value is not found in the shelter, then the real element in $a_i$ is accessed. The following steps are the same. From the attacker perspective, shelter hit or miss doesn't look different. After the $\sqrt n$ access operation, the complete memory is updated in an oblivious way.

The main problem is that memory updated should happen with limited memory consumption on client side: how? The authors implemented an **Oblivious Permutation** using PRF: how to implement PRP $\prod: \{0...n\} \rightarrow \{0...n\}$ with $O(\log n)$ storage on client side (where $n = m + \sqrt m$ that is the size of the actual memory + dummy values)?

The idea is to use PRP mapping from $\{0...n\} \rightarrow T_n$ where $T_n$ is a larger set to minimize collisions. Tags for the elements are drawn at random from the previous set. Then to permute the virtual addresses, we sort them according to the virtual tags and since they are generated using a PRF then they can be considered random resulting in the order of the virtual addresses to be random too. Permutation can be implemented through oblivious sorting.

There is an algorithm (nominally, Batcher's sorting network) to perform sorting in place (constant size of temporary memory) and this is what is done with ORAM. This algorithm implements the permutation in $O(n\log (n)^2)$. 

When a virtual address $v$ is to be accessed, then the tag for $\tau(v)$ is calculated and a binary search over the permuted memory is performed (logarithmic in the number of elements).

At the end of each epoch we have a stash including $\sqrt n$ values, and we want to update the memory and empty the stash again. The first step is to replace virtual addresses $a_i$ of dummies with $\infin$. An additional bit $\sigma$ is added to the tuple to indicate whether the value is stored in the shelter ($\sigma = 0$, most recent values) or in the permuted memory ($\sigma = 1$). Then, sort according to tuple $(a_i,\sigma)$. For duplicates mark the second occurrence (the one stored in the permuted memory, not the ones in the shelter since those are the most recent) as dummy value and replace its virtual address with $\infin$. Then we apply the sorting algorithm again by virtual address, removing $\sigma$. We remove all the dummy elements and a new epoch starts. 

What is the communication overhead of this construction? For each epoch, three operations take place: permutation ($O(n(\log n)^2)$), data access for the complete period ($O(\sqrt n)$) and the memory update ($O(n(\log n)^2)$). The most expensive operations only appear once during the process (i.e. at the beginning and at the end of an epoch) so these are amortized so we divide their value by $\sqrt n$ and we obtain that the complexity is $O(\sqrt n (\log n)^2)$ per access. 

It was even possible to achieve logarithmic communication with more than constant memory storage. Memory storage is increased but then complexity is reduced: blocks are organized in a virtual tree with the following characteristics:

- $N$: number of data blocks;
- $Z$: bucket (node) size, each bucket can contain multiple data blocks;
- $L$: tree height ($=\lceil \log N \rceil$);
- $2^L$: number of leaves;
- $2^{L+1}-1$: number of buckets.

Each address is mapped to a random leaf in the tree:

- $Path(x)$: from leaf node $x$ to root, outputs the path;
- $Path(x,l)$: bucket at level $l$ along the path, returns the specific bucket on the corresponding level.

The idea is to introduce "client storage": it contains a position map mapping a block with address $a_i$ to a leaf $x$. Then the stash is used to contain blocks from overflowing buckets locally (temporally) at client side (in fact buckets may overflow during the access protocol).

How does the access protocol work? The client wants to store data somewhere on the path to assigned leaf $Path(position[a])$. The client looks for the mapping to understand which leaf is associated to the virtual address and reads all the buckets on the path to stash (logarithmic in data size). The access to the actual target block is performed on the stash and then the stash is emptied again and the block is written again in the corresponding bucket (previously of course, this is encrypted with a new key so that the attacker cannot distinguish which blocks have been accessed and/or modified).

If done in this way, the attacker can observe the bucket access pattern meaning that if we have two different paths, then the attacker can say for sure that those path contained different addresses. What is added is that at each access operation there is a reshuffle of the position map so that each path is different for each operation even when accessing the same block.

There is one problem with this approach: the client need to keep track of the position map ($O(n \log n)$) but we wanted a sublinear memory size on the client side, it requires too much storage. So the position map is stored recursively in ORAM (I can store multiple position mappings in one block compressing the needed storage) saving memory on client side.
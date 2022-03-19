---
title: "Term Paper"
date: 2022-03-18T23:59:28-07:00
draft: false
---

This is a copy of the term paper I wrote for a class on internet markets.
I'm copying it here because it distills the last 6 months of research I've
done into my current working opinions and plans. 

Mikayla Maki, CS-410: Internet Markets Term Paper	

	Managing byzantine faults is a fundamental challenge for any distributed system. From the double spend problem, to sybil attacks, their existence sets hard limits on what is possible to coordinate. The Proof Of X schemes that cryptocurrencies use to secure their ledgers (e.g. work, space, and stake) use cryptographically expensive operation to make it infeasible for an attacker with less than 1/3 network share to break them. But the field of distributed database design has a solution: invariant confluence. I-confluent transactions are impervious to byzantine attacks and make expensive Proof Of X systems irrelevant. But there’s a catch; the only ledger system that can be implemented with I-confluent transactions isn’t even recognizable as a currency: unlimited mutual credit. 

###Byzantine Faults & How Blockchains Manage Them

	Cryptocurrencies, as distributed and trustless systems, are subject to Byzantine Faults. Any particular node of the network may misbehave in arbitrarily destructive ways, such as spending the same coin twice. This vulnerability is managed by first constructing a causal chain of transactions, and then attaching economic rewards for maintaining that causality. Proof of Work and Proof of Space both rely on expensive physical processes, such as generating hashes, to make it harder to rearrange causality than it is to maintain it. Proof of Stake is less ecologically destructive but still relies on locking up capital in the staking process. As of 3/18/22, it costs $94,000 (2022) to purchase the 32 ETH required to participate in Ethereum’s Proof of Stake chain. With all these resources, (Pease et al. 1980) show that these networks can tolerate no more than 1/3 of their members being Byzantine Faulty before their system guarantees are compromised. Other techniques for scaling blockchains rely on having a Proof of X substrate, and all of their problems, to fallback on (Decker & Wattenhofer, 2015) (Poon & Dryja, 2016) or compromise trustlessness by relying Intel’s engineers and factories to produce a perfect SGX chip (Lind et al., 2019). However, there’s an alternative to wasting money or energy securing the network: Invariant Confluent Transactions.

###I-confluence and How Synchronization Relates to Byzantine Tolerance

	Cryptocurrencies are fundamentally a subset of the field of distributed database systems, tuned to the needs of an exceptionally hostile environment. Within this context, byzantine faults are a type of coordination problem. All of the correct peers in a network must temporarily stop processing, use some consensus mechanism to ensure that no byzantine faults have occurred and then resume accepting transactions. In non-byzantine environments, (Bailis et al. 2014) show that certain applications have a property called ‘invariant confluence’ (I-confluence) which allows certain transactions to be committed concurrently, without synchronization. I-confluence only applies when there is a database state, e.g. ’account balance’, an invariant, e.g. ‘must be > 0’, and a transaction which can only generate states that maintain the given invariant, even when combined in an arbitrary order,  e.g. ‘add a whole number to account balance’. (Kleppmann & Howard, 2020) show that I-confluence is necessary and sufficient for a property they call Byzantine Eventual Consistency (BEC). 


	Systems which are BEC can handle any number of byzantine faulty nodes without coordination. This is both far more resilient than cryptocurrency’s 1/3 limit, and far more efficient than buying datacenters full of GPUs. There’s only one problem: subtracting coins from ‘account balance’ is not an I-confluent transaction.  Let’s say ‘account balance’ is ₿100. database replica A preserves the ‘> 0’ invariant locally and processes a -₿60 transaction.  Database replica B also preserves it locally and processes a -₿50 transaction. When merged, these two states show an account balance of -₿10,  violating the ‘account balance > 0’ constraint. This is the double spend problem, and as this transaction and invariant are not I-confluent, it is impossible to implement a bitcoin-style cryptocurrency without expending massive resources to resist byzantine faulty peers.
### I-Confluent Alternatives

	But bitcoin is not the only option. If we could remove the ‘account balance > 0’ invariant, then transactions would be I-confluent and would no longer need expensive synchronization. This can be done with a money system known as ‘Mutual Credit’. Bitcoin, and most other cryptocurrencies, were designed specifically to be ‘digital gold’: with a limited and uniform supply of coins for exchange. Mutual credit lifts the limited and uniform constraints, any user can generate a credit, a negative balance, and a corresponding debit, a positive balance, in someone else’s account. The total money supply is always 0 but the total liquidity expands and contracts (‘breathes’) dynamically as user’s needs change. Rather than be limited by what they have, users are constrained by their trust for each other. If Alice is untrustworthy, she effectively hyper-inflates her supply of tokens. Bob, knowing she’s untrustworthy, has no reason to accept millions of Alice’s credits until Alice repairs her books. A mutual credit database would be fast, fair, cheap, and could provide a market based means of managing a money supply. If needed, say for international travel, further constrains could be opted-into with this system to provide different kinds of guarantees. One example could be a notary network that uses a proof of stake system to ensure that credit limit rules are being followed by its members. A lightning-network style micropayment channel mechanism could be used to implement cross-country and cross-currency exchanges over the mutual credit substrate. 

###Further reading

	Mutual credit has a long history, for example the Hawala System has been in use since the 8th century throughout South Asia and Africa and has many similarities (Kagan, 2022). Several mutual credit systems are currently operating today, like the Open Credit Network, in the U.K. Sardex, in Italy.  Today, the Holochain project is attempting to create a mutual credit based cryptocurrency (Aoust, 2020). They’ve even released a white paper with a similar title to this paper detailing a potential technical mechanism for such a currency (Brock & Harris-Braun), however their paper lacks the theoretical grounding in database design theory. 
Conclusion

	Cryptocurrency waste is here to stay. Investing astonishing amounts of capital, electricity, or other resources is the only way to maintain a limited and uniform supply of digital tokens. However, database design theory and alternative economics shows us a different path. One based in human relationships and trust instead of massive expenditures of scarce resources. 

Citations

“Ethereum USD (ETH-USD) price history & historical data,” Yahoo! Finance, 19-Mar-2022. [Online]. Available: https://finance.yahoo.com/quote/ETH-USD/history/. [Accessed: 18-Mar-2022]. 
M. Pease, R. Shostak, and L. Lamport, “Reaching agreement in the presence of faults,” Journal of the ACM, vol. 27, no. 2, pp. 228–234, 1980. 
C. Decker and R. Wattenhofer, “A fast and scalable payment network with Bitcoin duplex micropayment channels,” Lecture Notes in Computer Science, pp. 3–18, 2015. 
J. Poon and T. Dryja, “The Bitcoin Lightning Network: Scalable Off-Chain Instant Payments,” Jan. 2016. https://lightning.network/lightning-network-paper.pdf
J. Lind, O. Naor, I. Eyal, F. Kelbert, E. G. Sirer, and P. Pietzuch, “Teechain,” Proceedings of the 27th ACM Symposium on Operating Systems Principles, 2019. 
P. Bailis, A. Fekete, M. J. Franklin, A. Ghodsi, J. M. Hellerstein, and I. Stoica, “Coordination avoidance in database systems,” Proceedings of the VLDB Endowment, vol. 8, no. 3, pp. 185–196, 2014. 
M. Kleppmann and H. Howard, “Byzantine Eventual Consistency and the Fundamental Limits of Peer-to-Peer Databases,” arXiv, Dec. 2020.
J. Kagan, “How hawala works,” Investopedia, 08-Feb-2022. [Online]. Available: https://www.investopedia.com/terms/h/hawala.asp. [Accessed: 18-Mar-2022]. 
P. Aoust, “A new type of cryptocurrency, as old as civilisation,” Holochain Blog, 04-Aug-2020. [Online]. Available: https://blog.holochain.org/mutual-credit-part-1-a-new-type-of-cryptocurrency-as-old-as-civilisation/. [Accessed: 18-Mar-2022]. 

A. Brock and E. Harris-Braun, “Mutual credit cryptocurrencies: Beyond blockchain bottlenecks,” Ceptr. [Online]. Available: http://ceptr.org/whitepapers/mutual-credit. [Accessed: 18-Mar-2022].  

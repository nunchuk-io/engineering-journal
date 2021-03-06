<h1>"Not Your Keys, Not Your Coins"... Is Not Enough</h1>

This slogan (perhaps coined by Bitcoin evangelist Andreas Antonopoulos) has been popular among the Bitcoin community for some time. “Keys” here refer to the private keys, implying that those who are in possession of the private keys own the bitcoins controlled by those keys.

While this slogan was true in the early days of Bitcoin, it is less relevant today and doesn’t tell the whole story. The reason is the growing sophistication of key generation and smart contracts, including, but not limited to, multisig.

Since Nunchuk’s mission is the proliferation of multisig, it is crucial that the first step is to clear up this misconception. The rest of this article explains why the above slogan no longer suffices.

<h2>Back to basics: Bitcoin addresses</h2>

Bitcoin addresses are constructed using 2 components: a data component, and a script component that explains how those data should be used in locking up the bitcoins.

![Early days of Bitcoin: P2PK and P2PKH](keys_are_not_enough_01.jpg "Early days of Bitcoin: P2PK and P2PKH")

In the early days of Bitcoin, both the data component and the script component were incredibly simple. The data was usually one single public key in uncompressed form. The script component was similarly straightforward. It either involved a single operation, OP_CHECKSIG (P2PK), or a slightly longer list of operations (P2PKH), but still highly predictable. In those days, the addresses acted ultimately as aliases for the public keys.

In this context, “not your keys, not your coins” makes sense. Whoever possesses the private keys can deduce the public keys. And from the public keys, the addresses.

Possession of the private keys means that:
* We know which addresses our bitcoins are stored in, and 
* How to unlock the bitcoins, once we know the addresses.

<h2>BIP16/BIP32</h2>

![BIP16 (P2SH) and BIP32 (HD wallets)](keys_are_not_enough_02.jpg "BIP16 (P2SH) and BIP32 (HD wallets)")

Things started to change with the introduction of more advanced scripting capabilities, beginning with [BIP16 (P2SH)](https://github.com/bitcoin/bips/blob/master/bip-0016.mediawiki). With P2SH, the script component can be almost anything. A good example is [Peter Todd's bounties](https://bitcointalk.org/index.php?topic=293382.0) for finding cryptographic hash collisions. But a more typical use case for P2SH involves multisig wallets, where funds are controlled by more than one public key.

Addresses of a P2SH-enabled multisig wallet are no longer predictable, because the order of the public keys included matters. For example, a 2-of-3 P2SH multisig address could be built 6 different ways, depending on how we order the 3 public keys. If we don’t back up the redeemScript, which contains this multisig key order, we might not even know which addresses belong to us! All is not lost, since we can “try out” all permutations. But this brute-force approach is costly and non-scalable, as we shall see later on.

Another development occurred on the key component side of the address. Some time after P2SH was created, Hierarchical Deterministic (HD) wallets arrived, later standardized in [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki). Prior to HD wallets, a wallet is simply a collection of random private keys. HD wallets create a key hierarchy so that the private keys belong to the same family, all generated from the same root called the master key.

HD wallets also made addresses less predictable. For each address in a HD wallet, we need to know from which lineage that particular public key descended from the master key. This is called the BIP32 derivation path.

To sum up: the introduction of BIP16 and BIP32 meant that possession of the private keys no longer suffices. We might also need the redeemScript (for BIP16) and the derivation paths (for BIP32) in order to fully “own” bitcoins.

<h2>SegWit and Taproot</h2>

Things became even more complicated with the activation of [Segregated Witness](https://en.bitcoin.it/wiki/Segregated_Witness), a much-anticipated suite of protocol updates that fixed critical issues such as transaction malleability.

![Segregated Witness (SegWit)](keys_are_not_enough_03.jpg "Segregated Witness (SegWit)")

SegWit introduced a new and improved address format, called Bech32.

The problem is that now there are even more ways addresses can be generated from a single master key. For each address type (there are now 3: legacy, native segwit, and a hybrid called nested segwit), we face the same problems of BIP16 and BIP32. So we end up with *permutations upon permutations* of address possibilities!

Levels of permutations:
1. Address types
2. Script types
3. Derivation paths
4. Order of keys, if multisig

This is where we are today. Both the key component and the script component of the address have gotten highly complex that possession of the private keys makes up only a small part of ownership.

During this period, wallet vendors coped with this rising complexity through their own ad-hoc, proprietary ways, which led to unfortunate consequences. First, wallets became less compatible with one another. As an example, to recover a wallet created with one vendor in another requires looking up magic “recovery paths” and manually running conversion scripts, a process that is error-prone. Another negative side effect is the invention of poor concepts such as YPUB/ZPUB that further complicate the process and confuse the user. We will discuss YPUB/ZPUB and why they should be avoided separately in another article.

But it doesn’t stop there. Soon Bitcoin will have even more advanced scripting capabilities, such as [Taproot](https://github.com/bitcoin/bips/blob/master/bip-0341.mediawiki). When that happens, the number of address permutations increases even further.

<h2>Solution: Descriptor Language</h2>

![Output Descriptors](keys_are_not_enough_04.jpg "Output Descriptors")

Perhaps realizing the urgency of this problem, Core developer Pieter Wuille set out to solve it. Pieter realized what we ultimately lacked was a higher-level language to tame this monstrous complexity. His solution, the [Output Descriptor language](https://github.com/bitcoin/bitcoin/blob/master/doc/descriptors.md) (descriptor for short), elegantly solves this problem.

The purpose of the descriptor language is to express precisely how keys are derived and how they are used in creating addresses.

With descriptors, the user only needs to back up 2 things for their wallet: the master keys (or [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) seeds), and the descriptors. There would no longer be any ambiguity, either in knowing which addresses on the blockchain belong to us, or how to recover the wallet using third-party tools.

In the future, it is crucial that all Bitcoin wallets move to a descriptor-first architecture.

The days of "not your keys, not your coins" are over. Perhaps it is more fitting now to say:

**"Not your keys, not your descriptors, not your coins".**


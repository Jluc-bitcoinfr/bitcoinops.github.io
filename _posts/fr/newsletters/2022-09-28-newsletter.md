---
title: 'Bitcoin Optech Newsletter #219'
permalink: /fr/newsletters/2022/09/28/
name: 2022-09-28-newsletter-fr
slug: 2022-09-28-newsletter-fr
type: newsletter
layout: newsletter
lang: fr
---
La newsletter de cette semaine décrit une proposition visant à permettre
aux nœuds LN d'annoncer des feerates dépendant de la capacité et annonce
un fork logiciel de Bitcoin Core axé sur le test de changements majeurs
du protocole sur signet.  Vous trouverez également nos sections habituelles
avec des résumés des questions et réponses principales du Bitcoin Stack
Exchange, des annonces de nouvelles versions et de release candidates,
et des descriptions de changements notables dans les principaux logiciels
d'infrastructure Bitcoin.

## Nouvelles

- **Fees  Ratecards:** Lisa Neigut [a posté][neigut ratecards]
  sur la liste de diffusion de Lightning-Dev une porposition de *fee ratecards*
  qui permettrait à un noeud d'anoncer quatre niveaux de frais de transactions.
  Par exemple, un paiement transmis qui laisse à un canal moins de 25 % de sa
  capacité sortante disponible pour transmettre les paiements suivants devra
  payer proportionnellement plus qu'un paiement qui laisse 75 % de sa capacité
  de sortie disponible.

    Le développeur ZmnSCPxj [a décrit][zmnscpxj ratecards] une façon simple
    d'utiliser les ratecards, "vous pouvez modéliser une carte tarifaire
    comme quatre canaux distincts entre les deux mêmes nœuds, avec des coûts
    différents pour chacun. Si le chemin au coût le plus bas échoue, il suffit
    d'essayer une autre route qui peut qui peut avoir plus de sauts mais un coût
    effectif plus faible, ou bien essayer la même canal à un coût plus élevé."

    La proposition prévoit également des redevances négatives.  Par exemple,
    une chaîne pourrait subventionner les paiements qui lui sont transmis
    lorsqu'il a plus de 75 % de capacité sortante en ajoutant 1 satoshi pour
    chaque tranche de 1 000 satoshis de valeur de paiement.  Les frais négatifs
    pourraient être utilisés par les marchands pour inciter les autres à restaurer
    la capacité entrante des canaux fréquemment utilisés pour recevoir des paiements.
    
    Neigut note que certains nœuds ajustent déjà leurs tarifs en fonction de la
    capacité des canaux, et que les fee ratecards constitueraient un moyen plus
    efficace pour les opérateurs de nœuds de communiquer leurs intentions au réseau
    que d'annoncer fréquemment de nouveaux feerates via le gossp de LN.

- **Implémentation de Bitcoin conçue pour tester les soft forks sur signet:**
  Anthony Towns [posted][towns bi] to the Bitcoin-Dev mailing list a
  description of a fork of Bitcoin Core he's working on that will only
  operate on the default [signet][topic signet] and which will enforce
  rules for soft fork proposals with high-quality specifications and
  implementations.  This should make it simpler for users to experiment
  with the proposed change, including directly comparing one change to
  another similar change (e.g. comparing [OP_CHECKTEMPLATEVERIFY][topic
  op_checktemplateverify] to [SIGHASH_ANYPREVOUT][topic
  sighash_anyprevout]).  Towns also plans to include proposed major
  changes to transaction relay policy (such as [package relay][topic
  package relay]).

    Towns is seeking constructive criticism of the idea for this new
    testing implementation, called [Bitcoin Inquisition][], as well as
    reviews of the [pull requests][bi prs] adding the initial set of
    soft forks to it.

## Selected Q&A from Bitcoin Stack Exchange

*[Bitcoin Stack Exchange][bitcoin.se] is one of the first places Optech
contributors look for answers to their questions---or when we have a
few spare moments to help curious or confused users.  In
this monthly feature, we highlight some of the top-voted questions and
answers posted since our last update.*

{% comment %}<!-- https://bitcoin.stackexchange.com/search?tab=votes&q=created%3a1m..%20is%3aanswer -->{% endcomment %}
{% assign bse = "https://bitcoin.stackexchange.com/a/" %}

- [Is it possible to determine whether an HD wallet was used to create a given transaction?]({{bse}}115311)
  Pieter Wuille points out that while identifying UTXOs created using an [HD
  wallet][topic bip32] is not possible, other onchain data can be used to
  fingerprint wallet software including types of inputs used, types of outputs
  created, order of inputs and outputs in the transaction, [coin
  selection][topic coin selection] algorithm, and use of [timelocks][topic timelocks].

- [Why is there a 5-day gap between the genesis block and block 1?]({{bse}}115344)
  Murch notes that the gap in the timeline could be explained by the genesis
  block having a higher difficulty target than required, Satoshi setting the
  block's timestamp in the past, or the [original Bitcoin software waiting][github
  jrubin annotated] for a peer before starting to mine.

- [Is it possible to set RBF as always-on in bitcoind?]({{bse}}115360)
  Michael Folkson and Murch explain the `walletrbf` configuration option and
  list a series of related changes involving defaulting to [RBF][topic rbf] in the
  GUI, defaulting to RBF in RPCs, and using [`mempoolfullrbf`][news208
  mempoolfullrbf] to allow replacements without signaling.

- [Why would I need to ban peer nodes on the Bitcoin network?]({{bse}}115183)
  In contrast to [discouraging a peer][bitcoin 23.x banman], user RedGrittyBrick
  explains that a node operator could choose to manually ban a peer using the
  [`setban`][setban rpc] RPC if the peer is misbehaving, a suspected malicious or surveillance
  node, or part of a cloud provider's network, among other reasons.

## Releases and release candidates

*New releases and release candidates for popular Bitcoin infrastructure
projects.  Please consider upgrading to new releases or helping to test
release candidates.*

- [Core Lightning 0.12.1][] is a maintenance release containing several
  bug fixes.

- [Bitcoin Core 0.24.0 RC1][] is the first release candidate for the
  next version of the network's most widely used full node
  implementation.

## Notable code and documentation changes

*Notable changes this week in [Bitcoin Core][bitcoin core repo], [Core
Lightning][core lightning repo], [Eclair][eclair repo], [LDK][ldk repo],
[LND][lnd repo], [libsecp256k1][libsecp256k1 repo], [Hardware Wallet
Interface (HWI)][hwi repo], [Rust Bitcoin][rust bitcoin repo], [BTCPay
Server][btcpay server repo], [BDK][bdk repo], [Bitcoin Improvement
Proposals (BIPs)][bips repo], and [Lightning BOLTs][bolts repo].*

- [Bitcoin Core #26116][] allows the `importmulti` RPC to import a
  watch-only item even if the wallet's private keys are encrypted.  This
  matches the behavior of the old `importaddress` RPC.

- [Core Lightning #5594][] makes several changes, including adding and
  updating several APIs, to allow the `autoclean` plugin to delete old
  invoices, payments, and forwarded payments.

- [Core Lightning #5315][] allows the user to choose the *channel
  reserve* for a particular channel.  The reserve is the amount a node
  will normally not accept from a channel peer as part of a payment or
  forward.  If the peer later attempts to cheat, the honest node can
  spend the reserve.  Core Lightning defaults to a channel reserve of 1%
  of the channel balance, penalizing by that amount any peers that
  attempt to cheat.

    This merged PR allows the user to reduce the reserve for a specific
    channel, including reducing it to zero.  Although this can be
    dangerous---the lower the reserve, the less penalty there is for
    cheating---it can be useful for certain situations.  Most notably,
    setting the reserve to zero can allow the remote peer to spend all of their
    funds, emptying their channel.

- [Rust Bitcoin #1258][] adds a method for comparing two locktimes to
  determine whether one satisfies the other.  As described in the code
  comments, "A lock time can only be satisfied by n blocks being mined
  or n seconds passing. If you have two lock times (same unit) then the
  larger lock time being satisfied implies (in a mathematical sense) the
  smaller one being satisfied.  This function is useful if you wish to
  check a lock time against various other locks e.g., filtering out
  locks which cannot be satisfied. Can also be used to remove the
  smaller value of two `OP_CHECKLOCKTIMEVERIFY` operations within one
  branch of the script."

{% include references.md %}
{% include linkers/issues.md v=2 issues="26116,5594,5315,1258" %}
[bitcoin core 0.24.0 rc1]: https://bitcoincore.org/bin/bitcoin-core-24.0/
[Core Lightning 0.12.1]: https://github.com/ElementsProject/lightning/releases/tag/v0.12.1
[bitcoin inquisition]: https://github.com/bitcoin-inquisition/bitcoin/
[bi prs]: https://github.com/bitcoin-inquisition/bitcoin/pulls
[neigut ratecards]: https://lists.linuxfoundation.org/pipermail/lightning-dev/2022-September/003685.html
[zmnscpxj ratecards]: https://lists.linuxfoundation.org/pipermail/lightning-dev/2022-September/003688.html
[towns bi]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-September/020921.html
[github jrubin annotated]: https://github.com/JeremyRubin/satoshis-version/blob/master/src/main.cpp#L2255
[news208 mempoolfullrbf]: /en/newsletters/2022/07/13/#bitcoin-core-25353
[bitcoin 23.x banman]: https://github.com/bitcoin/bitcoin/blob/23.x/src/banman.h#L28
[setban rpc]: https://github.com/bitcoin/bitcoin/blob/97f865bb76a9c9e8e42e4ee1227615c9c30889a6/src/rpc/net.cpp#L675

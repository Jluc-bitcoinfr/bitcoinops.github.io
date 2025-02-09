---
title: 'Bitcoin Optech Newsletter #215'
permalink: /en/newsletters/2022/08/31/
name: 2022-08-31-newsletter
slug: 2022-08-31-newsletter
type: newsletter
layout: newsletter
lang: en
---
This week's newsletter describes a proposal for a standardized wallet
label export format and includes our regular sections with summaries of
recent questions and answers from the Bitcoin StackExchange, a list of
new software releases and release candidates, and descriptions of
notable changes to popular Bitcoin infrastructure software.

## News

- **Wallet label export format:** Craig Raw [posted][raw
  interchange] to the Bitcoin-Dev mailing list a proposed BIP to
  standardize the format wallets use to export labels for their addresses
  and transactions.  A standardized export format could in theory allow
  two pieces of wallet software that used the same [BIP32][topic bip32]
  account hierarchy to open each others' backups and recover not just
  funds but all of the information the user manually entered about their
  transactions.

    Given previous challenges to making BIP32 wallets compatible with
    each other, a perhaps more achievable use would be making it easier
    to export transaction history from a wallet and use it in other
    programs, such as for accounting.

    Developers Clark Moody and Pavol Rusnak [each][moody slip15] replied
    with a [reference][rusnak slip15] to [SLIP15][], which describes the
    open export format developed for Trezor brand wallets.  Craig Raw
    [noted][raw slip15] several significant differences between what his
    proposed format is attempting to achieve and what SLIP15 seems to
    provide.  Several other design aspects were also discussed, with
    discussion appearing to be ongoing as this summary was being
    written.

## Selected Q&A from Bitcoin Stack Exchange

*[Bitcoin Stack Exchange][bitcoin.se] is one of the first places Optech
contributors look for answers to their questions---or when we have a
few spare moments to help curious or confused users.  In
this monthly feature, we highlight some of the top-voted questions and
answers posted since our last update.*

{% comment %}<!-- https://bitcoin.stackexchange.com/search?tab=votes&q=created%3a1m..%20is%3aanswer -->{% endcomment %}
{% assign bse = "https://bitcoin.stackexchange.com/a/" %}

- [Why isn't it possible to add an OP_RETURN commitment (or some arbitrary script) inside a taproot script path with a descriptor?]({{bse}}114948)
  Antoine Poinsot explains that [script descriptors][topic descriptors] are
  currently being extended to use [miniscript][topic miniscript] in Bitcoin
  Core and are expected in the Bitcoin Core 24.0 release. While initial miniscript
  features will only include segwit v0 support,
  eventually support for [tapscript][topic tapscript] and
  [partial descriptors][Bitcoin Core #24114] could make it possible to
  add commitments inside tapscript without solely using the `raw()` descriptor.

- [Why does Bitcoin Core rebroadcast transactions?]({{bse}}114973)
  Amir reza Riahi wonders why the Bitcoin Core wallet rebroadcasts transactions
  and why there is a delay. Pieter Wuille points out the P2P network's lack of
  transaction propagation guarantee as the reason for rebroadcasting being
  necessary and notes work done to remove rebroadcasting responsibilities from
  the wallet to the mempool. Readers interested in rebroadcasting can also review the [24 Aug
  2022][prreview 25768], [07 Apr 2021][prreview 21061], and [27 Nov
  2019][prreview 16698] PR Review Club meetings.

- [When did Bitcoin Core deprecate the mining function?]({{bse}}114687)
  Pieter Wuille provides a historical overview of mining-related features within Bitcoin
  Core over the years.

- [UTXO spendable by me or deposit to exchange after 5 years?]({{bse}}114901)
  Stickies-v provides an overview of Bitcoin Script operators, how [taproot][topic
  taproot] enabled with [MAST][topic mast] improves upon spending conditions
  from a privacy and feerate perspective, and points out that Script's lack of
  [covenants][topic covenants] makes the proposed conditions impossible solely in
  Script. Vojtěch Strnad points out that pre-signed transactions can help
  accomplish the proposed spending conditions.

- [What was the bug for the Bitcoin value overflow in 2010?]({{bse}}114694)
  Andrew Chow summarizes the [value overflow bug][] and its multiple
  inflationary effects: the large outputs created as well as the miscalculated
  transaction fee.

## Releases and release candidates

*New releases and release candidates for popular Bitcoin infrastructure
projects.  Please consider upgrading to new releases or helping to test
release candidates.*

- [LND 0.15.1-beta][] is a release that "includes support
  for [zero conf channels][topic zero-conf channels], scid [aliases][],
  [and] switches to using [taproot][topic taproot] addresses everywhere".

## Notable code and documentation changes

*Notable changes this week in [Bitcoin Core][bitcoin core repo], [Core
Lightning][core lightning repo], [Eclair][eclair repo], [LDK][ldk repo],
[LND][lnd repo], [libsecp256k1][libsecp256k1 repo], [Hardware Wallet
Interface (HWI)][hwi repo], [Rust Bitcoin][rust bitcoin repo], [BTCPay
Server][btcpay server repo], [BDK][bdk repo], [Bitcoin Improvement
Proposals (BIPs)][bips repo], and [Lightning BOLTs][bolts repo].*

- [Bitcoin Core #23202][] extends the `psbtbumpfee` RPC with the ability
  to create a [PSBT][topic psbt] that fee bumps a transaction even if
  some or all of the inputs to the transaction don't belong to the
  wallet.  The PSBT can then be shared with the wallets that can sign
  it.

- [Eclair #2275][] adds support for fee bumping a [dual funded][topic
  dual funding] LN setup transaction.  The PR notes that, with this PR,
  "dual funding is fully supported by Eclair!"  Though it also notes
  that dual funding is disabled by default and that tests for [cross
  compatibility with Core Lightning][news143 cln df] will be added in
  the future.

- [Eclair #2387][] adds support for [signet][topic signet].

- [LDK #1652][] updates support for [onion messages][topic onion
  messages] with the ability to send *reply paths*, and to decode them
  when received.  The onion message protocol doesn't require a node
  which relays an onion message to track any information about that
  message after relay, so it can't automatically send a reply back
  along the path the original message took.  That means a node which
  wants a reply to its onion message needs to provide hints to the
  receiver about what path to use for sending a reply.

- [HWI #627][] adds support for [P2TR][topic taproot] keypath spends using
  the BitBox02 hardware signing device.

- [BDK #718][] begins verifying both ECDSA and [schnorr][topic schnorr
  signatures] signatures immediately after the wallet creates them.
  This is a recommendation of [BIP340][] (see [Newsletter #87][news87
  verify]), was discussed in [Newsletter #83][news83 verify], and was
  previously implemented in Bitcoin Core (see [Newsletter #175][news175
  verify]).

- [BDK #705][] and [#722][bdk #722] give software using the BDK library
  the ability to access additional server-side methods available from
  Electrum and Esplora services.

{% include references.md %}
{% include linkers/issues.md v=2 issues="23202,2275,2387,1652,627,718,705,722,24114" %}
[lnd 0.15.1-beta]: https://github.com/lightningnetwork/lnd/releases/tag/v0.15.1-beta
[news175 verify]: /en/newsletters/2021/11/17/#bitcoin-core-22934
[news87 verify]: /en/newsletters/2020/03/04/#bips-886
[news83 verify]: /en/newsletters/2020/02/05/#safety-concerns-related-to-precomputed-public-keys-used-with-schnorr-signatures
[raw interchange]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-August/020887.html
[moody slip15]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-August/020888.html
[rusnak slip15]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-August/020892.html
[raw slip15]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-August/020893.html
[aliases]: /en/newsletters/2022/07/13/#lnd-5955
[slip15]: https://github.com/satoshilabs/slips/blob/master/slip-0015.md
[news143 cln df]: /en/newsletters/2021/04/07/#c-lightning-0-10-0
[prreview 25768]: https://bitcoincore.reviews/25768
[prreview 21061]: https://bitcoincore.reviews/21061
[prreview 16698]: https://bitcoincore.reviews/16698
[value overflow bug]: /en/topics/soft-fork-activation/#fix-value-overflow-bug-august-2010

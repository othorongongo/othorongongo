---
layout: page
title: Trading Rules
subtitle: we suggest reading these before trading on UbiTok.io
---

This page explains in detail the types of orders offered by Exchangers, and how they behave, followed by the terms and conditions for trading on Exchangers.
Limit Orders

Exchangers uses Limit Orders to ensure you get a fair price.

A Limit Order lets you set your own price when buying or selling.

Exchangers guarantees Best Execution - for example, if you enter an order to buy at a price of 1.30, and there's an existing order in the book to sell at 1.25, you'll get the better (for you) price of 1.25. If your order matches several resting orders in the book, price-time priority is applied to decide which orders get matched first.

The size of the order is always specified in the base currency internally (whether buying or selling). For example, on an UBI/ETH book where UBI tokens are being bought and sold for Ether, you would enter the number of UBI tokens you want to buy or sell.

Most limit orders can be partially filled - this can happen if, say, you enter an order to buy 10,000 UBI but only 4,000 UBI are available at the price you want.
Market Orders and Stop Orders

Because the time taken to get the transaction containing your order into the Ethereum blockchain can vary considerably, we feel Market Orders are too dangerous and do not support them. We suggest using an Immediate or Cancel Limit Order with a generous price instead.

We do not currently support Stop Orders (one difficulty is how to pay the gas to trigger the order when the stop is reached), but are keen to offer them (as Stop-Limit orders) in future.
Order Terms

Exchangers supports the following Order Terms for your orders, which let you control what happens when your order can or cannot be filled:

    Good Till Cancel - Your order will be matched against existing orders. If it cannot be completely filled, the remaining unfilled portion of your order will be added to the book and remain valid until you cancel it. These are the default terms.
    Immediate or Cancel - Your order will be matched against existing orders. If it cannot be completely filled, the remaining unfilled portion will be cancelled. The order will never be added to the book. Popular for quick, small trades.
    Maker Only - Your order will be rejected immediately (without matching) if any part of it would be filled. Otherwise, it will be added to the book and remain valid until you cancel it. Popular with market makers. Also known as Post Only.

Some exchanges call these the Time in Force of the order.
Gas Costs

Exchangers performs all matching using a smart contract running on the Ethereum blockchain. This is good - it means you don't need to worry about our servers failing, being shutdown or being hacked.

However, one downside is that running code on the Ethereum blockchain is slow and expensive - every operation costs "gas", which is paid for by the user via their Ethereum wallet. This helps keep the network running - thousands of nodes all need to agree on the results.

We've done our best to keep gas usage of our smart contract as low as possible. It can still use a lot of gas if you place an order that matches a large number of resting orders on the book.

For example, if the book contains a hundred different open orders to sell 1 UBI token each @ 2.0, and you place an order to buy 100 UBI @ 2.0, your order will match those other 100 orders - that's 101 clients who need to be paid.

To let you stay in control of gas costs (and avoid running out of gas), we limit the number of matches allowed when placing a new order (there's no limit for orders already in the book). It works like this:

    For Immediate or Cancel orders, the remaining unfilled portion of the order is cancelled if the limit is reached;
    For Maker Only orders, the limit doesn't apply - they're always rejected if they would match an order;
    For Good Till Cancel orders, you can choose what to do if the maximum number of matches is reached - see Gas Top Up section.

Most of our books have a reasonably high minimum order size to avoid them getting cluttered up with tiny orders.
Gas Top Up

If Allow Gas Top-Up is disabled (the default), and there are so many matching orders in the book that matching your Good Till Cancel order against them all is too expensive to do in one go, the remaining unfilled portion of the order will be cancelled after matching as many as possible. The order will not be added to the book if this happens.

If Allow Gas Top-Up is enabled, and there are so many matching orders in the book that matching your Good Till Cancel order against them all is too expensive to do in one go, your order will be moved to a special 'Needs Gas' status after matching as many as possible. You can then either cancel the remaining unmatched portion of the order, or 'Continue Placing' the order, adding more gas.

This mostly affects very large orders at a generous price - the exchange UI will warn you if your order looks like it may be expensive to match, and suggest enabling gas-top up.
Fees

A fee of 0.05% of the matched amount is deducted from the amount the taker receives from each trade. For buy orders, the fee is in the base currency; for sell orders it is in the counter currency. The maker (provider of liquidity) pays no fees.

Example 1: The FOO/ETH Book has an offer to sell 10,000 FOO @ 1.50. You place an order to buy 2000 FOO @ 1.50, which costs you 3000 ETH. You are the taker on this trade, so you pay a fee of 1 FOO (0.05% of 2000) and receive the remaining 1999 FOO.

Example 2: You place an order to buy 200 FOO @ 1.50, which costs you 300 ETH. It is not matched and rests on the book. Another client places an order to sell 1000 FOO @ 1.50, of which 200 FOO (300 ETH) can be matched with you. They are the taker, so they pay a fee of 0.15 ETH (0.05% of 300), and receive the remaining 299.85 ETH. You receive the full 200 FOO.

Alternatively, and subject to availability, Exchangers Reward Tokens (UBI) can be used instead of ETH (at a fixed exchange rate of 1000:1) to pay Exchangers exchange fees on all Exchangers markets that have ETH as a currency (base or counter).

Example 3: You prefer to pay fees using your UBI tokens, so you deposit 200 UBI into the FOO/ETH book contract to cover future fees. You then sell some FOO tokens for 100 ETH. Instead of 0.05 ETH being deducted from your 100 ETH, 50 UBI is deducted from your UBI balance.

Fees paid by traders are held in the book contract on behalf of the contract creator.
Fees and Gas Summary

|Action|Exchangers Fee|Ethereum Gas Amount| |Deposit Eth|none|100,000| |Deposit Token|none|200,000| |Withdraw Eth|none|100,000| |Withdraw Token|none|100,000| |Place Order (Maker-Only)|none|300,000| |Place Order (GTC / IoC)|0.05% of liquidity taken|300,000 + 100,000 for each order matched| |Cancel Order|none|150,000|
Order Lifecycle

Orders have a Status, which can be one of:

    Sending - Your order is being sent via the Ethereum network to the exchange contract;
    Failed Send - Your order could not be sent to the Ethereum network;
    Failed Txn - Your order was sent to the Ethereum network but was not processed by the exchange contract;
    Rejected - The exchange contract could not place your order (e.g. size too small);
    Needs Gas - See Gas Top Up section;
    Open - Your order is resting on the book and waiting for others to fill it (or you to cancel it);
    Done - Your order has either been completely filled, or it has been cancelled. Nothing else can happen to it.

Orders in some Statuses (such as Rejected and Done) have a further Reason Code explaining why they are in that state, which can be:

    Invalid Price - The price of the order was too low or too high;
    Invalid Size - The size of the order (either in base or counter currency) was too small;
    Insufficient Funds - Your exchange balance does not have enough funds to place this order;
    Would Take - Your Maker Only order would immediately match another order;
    Unmatched - Your Immediate or Cancel order was cancelled because it could not be matched;
    Too Many Matches - The limit on matches prior to entering the book has been reached (see Gas Costs section);
    Client Cancel - You cancelled the order.

Confirmations, Forks, and Orphaned Transactions

Exchangers runs entirely on the Ethereum blockchain. The nature of the blockchain means that thousands of Ethereum nodes around the world are busy working to collectively agree on the state of the blockchain - in particular, to agree on which Exchangers orders are in the order book, and on the state of each order.

Like all blockchains, sometimes this agreement (or consensus) isn't reached - groups of Ethereum nodes can temporarily disagree about which transactions (such as orders being placed) have been accepted into the blockchain. Normally, these disagreements are quickly resolved (in under a minute) and one group's view of the world wins, with transactions from the losing group being merged in where possible. (You might find the terms "Uncle Block" or "Chain Re-organization" useful if you want to read more).

Unfortunately, it is still possible (though rare) that a transaction can appear to have been accepted into the blockchain, but a short while later turn out to be rejected (that is, not confirmed).

If this occurs for an order-related transaction then clients may see odd behaviour in the Exchangers Web UI such as:

    depth appearing in the order book, only to disappear again;
    ‎an open order appearing to be filled, only for it to return to an unmatched state;
    ‎an apparently cancelled order returning to an open state.

We are working with the Ethereum development community to establish and apply best practices for how to shield clients from this uncertainty, or to convey it meaningfully. Unfortunately the approach taken by centralized exchanges to wait for a certain number of confirmations on deposit doesn't work on-chain - it's not just deposits that can be reverted.

For now, we recommend clients mitigate against this risk by waiting several Ethereum blocks (e.g. 12 blocks) before treating a trade or order state as final. This is particularly important for clients arbitraging between Exchangers and off-chain exchanges.

Clients should be aware that pending transactions are visible to other Ethereum nodes, and that it is possible for Ethereum miners to re-order pending transactions.
New Book Formation

From time to time, Exchangers's software suppliers may release a new exchange contract for a trading pair and make the Exchangers web UI point to the new contract. For example, we might do this to improve performance, add features, or adjust minimum order sizes.

When this happens, orders from the old contract will not be copied to the new contract - the new book will start empty. Exchangers's software suppliers will try to provide several days notice before introducing a new contract, though exceptions may be made for security issues.

We aim to provide easy web UI access to each old contract for at least 3 months so orders / balances in the old contract can be cancelled, withdrawn or inspected. The contract itself is unstoppable - clients can continue to use the old contract via other interfaces.
Other Market Participants

The Exchangers exchange contract has no notion of sign-up, approval, or indeed identity. Exchangers's software suppliers cannot and do not intend to police behaviour of clients of the exchange contract.

However, we ask that clients:

    act honestly in dealings other market participants;
    act fairly, dealing with other market participants in a consistent and appropriately transparent manner;
    act with integrity, avoiding questionable practices and behaviours;
    act cautiously, aware that some other market particpants may not follow these guidelines.

Terms and Conditions

The Exchangers Solidity Contracts, Web UI, and software libraries ("the Dapp") are provided "as is", without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose and noninfringement.

In no event shall the authors or copyright holders ("the software suppliers") be liable for any claim, damages or other liability, whether in an action of contract, tort or otherwise, arising from, out of or in connection with the Dapp or the use or other dealings in the Dapp.

The Dapp should be considered its own autonomous entity as far as permitted by law - it is your responsiblity to study its likely behaviour before interacting with it, including taking into account that the actual behaviour of the compiled contract in a real Ethereum node may differ from the assumed behaviour based on the apparent intent of the Solidity source code, or differ from the trading rules in this page.

The Dapp contracts will live as long as the Ethereum blockchain, but no warranty is given that the Exchangers website will continue to provide access to the contracts.

The software suppliers are unable to and will not cancel, undo, or make good any erroneous trades - all trades are final, even in the case of palpable errors made by market participants or the software suppliers themselves. The software suppliers are not responsible for the behaviour of other market participants.

The software suppliers do not in any way endorse the suitability of tokens offered for sale on the exchange. Nothing on this website should be taken as financial advice.

By interacting with the Dapp you agree to accept the conditions in this section as well as those listed at ethereum.org/agreement.

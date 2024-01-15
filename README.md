# ClarionPool

## Introduction
**Bitcoin on-chain metrics** offer insights into the Bitcoin network, protocol and market participant psychology. Examining and gleaning insights from these metrics enable the ability to predict and front run market-level events within some margin of accuracy. 

Today most (not all) on-chain metrics are derived from looking at block statistics after the block has been confirmed. The metrics are showing you what happened on-chain. 


## ClarionPool
ClarionPool moves the on-chain focus to the mempool before transactions are confirmed in the next block providing a preditive view into what WILL happen. Clarionpool proposes using the mempool analysis as a means to predict what the on-chain metric values will be before the block is confirmed. This has the potential to be very valuable to market participants, traders and investors. 


## ClarionPool Implementation
To achieve this a high frequency analysis of the mempool and the updating of metrics will need to occur as close to real time as possible. Ideally if ClarionPool metrics are updated every second that is about as fast as human can make use of it. If one second samples are not possible because of the scale involved up to 60 seconds would still provide immense value. 


### Implementation Considerations
- High performance hardware
- High performance code, reduce abstraction, code at lowest level possible while still being practicle.
- High performance databases. Postgres + Timescale, Prometheus, MongoDB, which ever is the highest performance for this use case.
- High performance front end to visualize the metrics. Grafana, node.js + react, needs more research.
- Parallel design. Discover a way to group mempool transactoins and process in parallel rather than seuqential. We'll have to think about how to do this best, a few ideas might be to sort by feerate because the higher the feerate the faster that TX will be selected by a miner, which means those TX are most likely to be in the next block. So we should have a real time sorting and process TX will highest fee first. More research.
- Specialized or customize version of Bitcoin core. There is no reason we can't use the Bitcoin source code to implement our tool set. There are a few customized versions of Bitcoin core that are built to either showcase a feature, provide additional metrics, etc. Perhaps this is something we consider initially or perhaps it is a phase 2. We may want to build using the regular or one of the existing custom verisons to learn as we build and through that we discover we should have our own version of Bitcoin core with our tools natively incorporated providing higher performance for ClarionPool. 


## How ClarionPool Provides an Edge
1. **Be First:** Know about market changes before that occur. 
2. **Self Refining Metrics:** Track the accuracy of the predictions. What did ClarionPool say would happen and how accurate was it when compared to the block confirmation?
3.  **AI Interface:** Incorporate a LLM like ChatGPT or an open source version like AutoGPT or AutoEngineer, etc. This gives us and clients of ClarionPool an easy way to request insights from the data. Instead of the human needing to sift through 100s of charts, or make sense of many threshold alerts have an AI agent that has a narrow scope of knowledge only about Bitcoin, mempool, data analytics, etc. We can train the AI with any data. 


## Summary
Extend on-chain analysis to the mempool, create predictive versions all on-chain metrics that already exist today, measure accuracy in real time and refine and correct in real time, use AI as a backend tool to interate faster and as a UI for subscribers to quickly get meaningful insights and skip the need to manually review charts and metrics on the fly. 


## Testimonials
> Bro...do you subscribe to ClarionPool?  
> What's that?  
> Get it, trust me. Use my referral code.




## Background Fundamental Bitcoin Mechanics
This section is a short description of how Bitcoin transaction are constructed, how miners select transactions and how the miners confirm blocks. 

1. UTXOs are unspent transatcion outputs. This is Bitcoin's accounting system. In simple terms a UTXO is just an amount of Bitcoin, it can be any amount. An analogy to USD is you have paper dollar bills in your pocket in the demoniations of 2x $1, 3x $10 and 1x $100. In BItcoin UTXO terms that would mean you have 6 UTXOs, added together it's $132 spread across 6 bills. In Bitcoin instead of fixed amount "bills" or "UTXOs" a UTXO can be any amount.
  
   The UTXOs are used as inputs for transactions. Your wallet, or you can select them manually if you want, select the UTXOs you want to use to make your payment transaction. If you don't have the exact combination of UTXOs to have the exact amount you want to spend you use a combination of UTXOs that exceed the amount you want to spend and you get change back.

   This is exactly like having a $20 and needing to pay for $18 for beers. You hand the $20 to the cashier (for a brief moment you spent $20) but you get your $2 change right back. Bitcoin works the same way, you spend or use as inputs the UTXOs you need to satisfy the transaction and get change back in the form of a new UTXO.

   When UTXOs are spent they are destroyed. A UTXOs life is from the time it was created as an output from a transaction up to the time it gets spent which destroys. Your change for example comes back to you as a new UTXO. 


2. Miners are free to select any transactions that exist in the mempool. Miners get paid the transaction fees and that is their incentive for selecting a transaction. The higher your transaction fee is the more likely it is a miner will select it. Usually this is referred to as "how fast do you want your transaction to be confirmed?" The faster you need or want it confirmed the higher your fee should be. If you don't need your transaction confirmed quickly you are free to use a lower fee and wait longer for it to be confirmed.
  
   There are cases where maybe you set the fee too low and your transaction could be waiting in the mempool for a long time, days, weeks even months. This is not that common because most people use a competitive fee associated with some amoutn of time. Some wallets will provide and esitmate that says something like..."If you want your TX confirmed in the next block pay 100 sats/vByte, if you want it confirmed in the next 10 blocks pay 75 sats/vByte".

   The fee market is dynamic and always changing from moment to moment. A fee that gets you into the next block now may be ore or less to get into the next block later. It's also important to know that the transaction fee you pay is for block space, it's not related to the amount of BTC you are spending.

   The more UTXOs you use as inputs the more you have to pay in fees because each UTXO input requires block space. For example if I have 1 UTXO for 10k BTC and I want to spend it I only have 1 input so I pay for one vbyte of block space. If I have 10k BTC but it's spread across 100 UTXOs then I have to pay for 100 UTXO inputs and use up 100 vbytes.

   There is more to how block space is consumed. There are differnt types of UTXOs that are more efficient about the use block space. I won't get into that here, but good to know that over the years there have been improvements that result in a single UTXO taking up less block space than it use to. You generally identify this by the type of Bitcoin address also known as the spending script your coins/UTXOs are coming from. There is the legacy which are the first generation of Bitcoin address scripts that are the least efficient. Then came segregated witness address scripts that rearange the transaction header making more room for data so these UTXOs take up less space which means they cost less to use. 


3. How miners confirm a block. Miners create a block template. The template is filled with transactoins the miners select from their mempool. The template represents the candidate block, so it also includes the block header information. This is information like the block number, the hash of the previous block, merkle root, timestamp, nonce, difficulty target, etc.
  
   The miners use the block template header as input to the hashing algorithm. The output value of that hash is either above or below the difficulty target. If the hash value is above the target it DOES NOT satisfy the protocol and the miner just change the nonce field only and send the new nonce with the same block header through the hash algorithm again producing a new value, it's either above or below, and this repeats until a miner produces a hash that is BELOW the difficulty target.

   With this understanding you can see how it's impossible to spoof mining which in turn means this network/protocol is very secure from this perspective. There is no way to win a block reward by faking it, you have to produce the hashes to participate in the search for the satisfying hash value. This mechanism of harnessing real world energy to produce hashes is referred to as Proof of Work PoW. The work of producing hashes must be done to compete for the block reward. You can not flip a bit somewhere and fake it convincing the protocol you won. There is no one to convince, no one choose you as the winner, you have to find the value and the only way to search for it is to produce the hashes. 

***Side note***: Originally a Bitcoin node was also the miner. As the network grew in scale and GPU then ASICs started being used for minging lead to the separation of Bitcoin nodes and miners. Today they are different hardware, separate phyiscal machines. 

A Bitcoin node performs the function of enforcing the Bitcoin consensus rules and it stores a copy of the block chain. A miner just produces the hashes using the block template it was given by the Bitcoin node they are configured to work with. Bitcoin nodes hold the mempool, not miners. 





















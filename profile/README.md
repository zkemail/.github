# ZK Email
This is the official organization for Proof of Email, created by [yush_g](https://twitter.com/yush_g), [sora suegami](https://twitter.com/SoraSue77), and [sampriti](https://twitter.com/sampriti0). Thanks to the long list of contributors, including [Vivek](https://twitter.com/viv_boop), [Tyler](https://twitter.com/AtHeartEngineer), [Saleel](https://github.com/saleel), and [Andy](https://twitter.com/AndyGuzmanEth) for helping to improve this technology, and to 0xPARC and EF PSE for providing grants to support this work! You can see applications and links at our main website hub, [prove.email](https://prove.email).

## Circom zk-email
We have [NPM SDKs](https://www.npmjs.com/search?q=%40zk-email) for the frontend functions, circuits, and smart contracts, that should allow you to quickly deploy new circom instances of zk-email.

We have an [end-to-end circom implementation](https://github.com/zk-email-verify/zk-email-verify/) with a live demo for Proof-of-Twitter NFTs on Goerli at [https://zkemail.xyz](https://zkemail.xyz). Circom shines with it's succinctness and server-side speed, which proves zk-email circuits in under 30 seconds. On the client side, it takes about 6 minutes to run in browser and does a 1gb download, hence our desire to work on a halo2 version. We also have a [regex library that does circom codegen for arbitrary regex strings on CLI](https://github.com/zkemail/zk-email-verify/tree/main/libs/regex_to_circom), as well as a [DFA visualization site](https://zkregex.com/min_dfa) to help you convert a regex into the same states as our circom generator \[thanks to CyberZHG for the base repo\]. 

## Halo2 zk-email
We have finished a halo2 version of zk-email, which we expect to 10x improve proving speed for zk-email applications that require client side privacy. To do so, we pair our zk-regex library with the [halo2-regex circuits](https://github.com/zk-email-verify/halo2-regex/) that create arbitrary regex circuits in halo2. We also have [base64 encoding + decoding circuit](https://github.com/zk-email-verify/halo2-base64) and [halo2 RSA + SHA256](https://github.com/zk-email-verify/halo2-rsa) built with Axiom's flexgates and Brechtpd + PSE's SHA256. We have heavily optimized for proving-time, and we expect all of our circuits to be verified recusively on chain cheaply. 

We currently have a fast client side proof (~20 seconds) with split-up deployed contracts to allow L2 deployments. This can be proved on an L2 for 48M gas [~about $8 on Arbitrum]. We hope to optimize this over time. You can alternatively feed into an autoscaling recursive halo2 aggregator in the cloud (~300 seconds on a massive machine) that maintains zero knowledge and compresses the proof to about 500K gas. We will work on improving both performance and memory over time, and think we can 10x the aggregator server performance.

## Email Wallet
Using circom zk-email, we have a demo of a wallet signing flow only using emails instead of traditional seed phrases. You can try a demo of sending ERC20s via sending emails at [sendeth.org](https://sendeth.org), view the docs at [docs.sendeth.org](http://docs.sendeth.org), and see the slides at [slides.prove.email](http://slides.prove.email). We are adding an extensions layer and privacy layer to allow developers to trigger arbitrary Ethereum functions from email subjects. We intend for this to be a powerful web3 onboarding protocol.

## Relayer
Our open source [relayer](https://github.com/zkemail/relayer/) allows anyone to self-host or cloud-host with the ability to:
1) Use our Dockerimage to immediately deploy any ZK proof to a rapid, autoscaled, 64 core proving instance to do proofs in the cloud. Note that privacy will be leaked to AWS in this case, so the only usecase is succinctness.
2) Interface with the ZK proving protocol via sending emails, via built-in SMTP and IMAP servers that can authenticate with any gmail account

## Other public goods
We have produced serveral OSS public goods. One is a [halo2 wasm benchmarking repo](https://github.com/Divide-By-0/halo2-secp) that runs any halo2 wasm code in the browser on 100 instances in parallel for any browser on any operating system (mobile or desktop), and spits out the mean running time and variance -- we intend to make this an easy to use NPM package soon for others to use. 

## Help out!
We are an open source project and decentralized protocol. If you are interested in giving, support us on [Gitcoin Grants](https://explorer.gitcoin.co/#/round/10/0x9331fde4db7b9d9d1498c09d30149929f24cf9d5/0x9331fde4db7b9d9d1498c09d30149929f24cf9d5-62)!

If you are interested in helping, reach out us on [email](mailto:aayushg@mit.edu) or telegram/twitter (yush_g)! We are giving grants for full or part time work to deploy libraries of our work to npm, integrate into zk repl, novel zk optimizations, applications in the proof of identity and defi space, and a rewrite via lasso/jolt and/or new versions of nova. We are hoping in Q3-Q4 2023 to ship the zk email wallet prototype with extensions, end to end, and hope to open the protocol to extension developers in 2023 as well as sponsor hackathons and bigger teams to build atop the protocol. Here are specific project ideas, for which we will give a grant for any successful implementation.

Note that our SDK has not reached 1.0 yet, so we will almost certainly push backwards-incompatible breaking changes, especially after our audit in October. We recommend starting work on the following in November.

Core:
| Title | Description |
|-------|-------------|
| **Issue Resolution** | Resolve issues in [zk-email-verify](https://github.com/zkemail/zk-email-verify/issues) or in the relayer [note: working on full relayer refactor right now]. |
| **Audit Fixes** | There are a few issues with some of our circom dependencies: reach out to me for bugs! |
| **Tests on Packages** | Add tests to our [circom packages](https://github.com/zkemail/zk-email-verify/tree/main/packages) to add more circuit tests and circuit unit tests, as well as a test to make sure witness gen works on our twitter-verifier app on each PR, and miscellaneous circuit and contract tests. We are also looking for end to end tests on [sendeth](https://github.com/zkemail/sendeth). |
| **Deploy Halo2 Benchmarking Library** | Deploy our [halo2 wasm benchmarking library](https://github.com/Divide-By-0/halo2-secp) to cargo, and optionally to an easy-to-use frontend. |
| **Fix ZK Regex** | Our [Javascript regex to circom library](https://github.com/zkemail/zk-regex) is presently broken, but [regex_to_circom works](https://github.com/zkemail/zk-email-verify/tree/main/libs/regex_to_circom). Add tests to both repos to keep them in sync, and fix the bugs in the Javascript version (and enable the ability to read the regex from a file, not command line).

Projects:
| Title | Description |
|-------|-------------|
| **Bountied Whistleblowing Project** | A bounty platform for leaks where people can bid on leaks with specific sources (i.e. from `@___`) and specific text (i.e. includes the phrase "`___`"). It creates an escrow system on chain where others can upload those anonymized leaks along with zk-email proofs that they satisfy the regexes, and reveal only the parts that they want to reveal. |
| **Arxiv Donation Project** | Put in an arxiv link, and a bot scrapes all the emails out of the PDF/Arxiv itself. It then scrapes all of the emails off of all of the dependencies and allows the donor to reweight them based on where they appeared in the text (i.e. it defaults to something like, cited in previous work or methods splits 40% of the donation, authors cited in intro split 10% of the funds or whatever). It then deploys zk-email wallets for all of them and sends them the money. |
| **AttestationStation Attestations for Twitter etc** | Convert the Twitter demo to an official AttestationStation/PolygonID attestation. |
| **Automatic Spotify Splits for AI Voice Royalities** | Prove via zk-email on Spotify confirmation emails and EZKL proof of voi e via ML, that you used an artists voice and split profit with them. Details at https://hackmd.io/Nf8mSSKwRIu3GYyhGq5f9A |
| **Proof of Residence** | Prove via some emailed confirmation sent by some government or service that you filed taxes or payed for property in that country, and make only the country public. |
| **Proof of Credit Score** | Enable undercollateralized, crypto lending by allowing someone to prove their credit score on-chain via a confirmaton email from a credit score provider. |
| **General Oracles** | Prove any oracled data in emails, such as a NASDAQ daily ticker email for the price of some stock, or a Robinhood spot price confirmation. Tradingview Alerts should be able to support this. |
| **Investor Interest** | Prove some investor emailed you a term sheet, and mint a credential that lets you anonymously disclose how your interactions with them were. |
| **2FA On-Chain** | Account transactions on-chain above a certain amount need to be verified via an email from your email address as well. |
| **Multisig Control via Email** | Native integration with SAFE or other multi-sig wallets to allow zk-emails to be direct multisig signers, interacting and approving transactions via emails. |

<img width="601" alt="proofofemail coming 10.13.22" src="https://user-images.githubusercontent.com/4804438/195414950-629e0e13-dea2-4f0c-a433-2bf3b2151533.png">


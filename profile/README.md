# [ZK Email](https://prove.email)
This is the official organization for [Proof of Email](https://prove.email), created by [yush_g](https://twitter.com/yush_g), [sora suegami](https://twitter.com/SoraSue77), and [sampriti](https://twitter.com/sampriti0). Thanks to all current core contributors, including [Saleel](https://twitter.com/_saleel), [Aditya](https://github.com/Bisht13), [Elo](https://github.com/Metachaser24), and [Wataru](https://github.com/wshino). Thanks to [Vivek](https://twitter.com/viv_boop), [Tyler](https://twitter.com/AtHeartEngineer), [Rasul](https://curryrasul.com/), and [Andy](https://twitter.com/AndyGuzmanEth) for helping out from the PSE side, and to the [many  open source contributors](https://github.com/zkemail/zk-email-verify/graphs/contributors) who have made pull requests, and to [0xPARC](https://0xparc.org), [EF PSE](https://pse.dev), and [Gitcoin supporters](https://explorer.gitcoin.co/#/round/137/0xa1d52f9b5339792651861329a046dd912761e9a9/0xa1d52f9b5339792651861329a046dd912761e9a9-17) for providing grants to support this work! You can see applications and links at our main website hub, [prove.email](https://prove.email).

## Goals for Q1 2024
By April 2024, we expect to release 1) a custom account recovery solution via email guardians for account abstraction wallets, and 2) a login with zk email flow with ECDSA key authorization. 
1) The flow of account recovery will be that AA wallets specify email addresses as guardians, those email addresses receive emails to accept gaurdianship, and if the keys are ever lost and the wallet needs to be recovered, then guardians can simply send an email with the users new public key, to save their assets after a timelock.
2) The flow of login with email will be that users put in their email addresses into a website, they get a magic link style email in their inbox from a relayer with a hidden link authorizing an ephemeral key, and any reply to that email confirms that ephemeral key as the signer for that email address on any website. That website can continue to use that browser-specific temporary ECDSA keypair until the user switches devices, at which point they can do the flow again to authorize additional ECDSA keypairs. This will be a completely decentralized drop-in replacement for signin with email for crypto-native apps that expect ECDSA signers.

## Circom zk-email
We have [NPM SDKs](https://www.npmjs.com/search?q=%40zk-email) for the frontend functions, circuits, and smart contracts, that should allow you to quickly deploy new circom instances of zk-email.

We have an [end-to-end circom implementation](https://github.com/zkemail/zk-email-verify/) with a live demo for Proof-of-Twitter NFTs on Goerli at [https://zkemail.xyz](https://zkemail.xyz). Circom shines with it's succinctness and server-side speed, which proves zk-email circuits in under 30 seconds. On the client side, it takes about 6 minutes to run in browser and does a 1gb download, hence our desire to work on a halo2 version. 

## ZK Regex
In order construct new proofs, we also have a [regex library that does circom/halo2 codegen for arbitrary regex strings via configuration files and a CLI](https://github.com/zkemail/zk-regex), as well as a [DFA visualization site](https://zkregex.com/min_dfa) to help you convert a regex into the same states as our circom generator \[thanks to CyberZHG for the base repo\]. Thanks to Javier for a [work in progress UI](https://zk-regex-ui.pages.dev/) to convert regexes to circom circuits rapidly. 

## Halo2 zk-email
We have finished a halo2 version of zk-email, which we expect to 10x improve proving speed for zk-email applications that require client side privacy. To do so, we pair our zk-regex library with the [halo2-regex circuits](https://github.com/zk-email-verify/halo2-regex/) that create arbitrary regex circuits in halo2. We also have custom [base64 encoding + decoding circuit](https://github.com/zk-email-verify/halo2-base64) and the first-ever [halo2 RSA + SHA256](https://github.com/zk-email-verify/halo2-rsa) built with Axiom's flexgates and optimizing Brechtpd + PSE's SHA256. These public goods have been used for several applications regarding provenant data in the wild, but the halo2 work has not been audited yet. We have heavily optimized for proving-time, and we expect all of our circuits to be verified recusively on chain cheaply. 

We currently have a fast client side proof (~20 seconds) with split-up deployed contracts to allow L2 deployments. This can be proved on an L2 for 48M gas [~about $8 on Arbitrum]. We hope to optimize this over time. You can alternatively feed into an autoscaling recursive halo2 aggregator in the cloud with a GPU prover (~300 seconds on a massive machine without a GPU, ~60 seconds with a GPU) that maintains zero knowledge and compresses the proof to about 500K gas, with a ~500MB zkey. We will work on improving both performance and memory over time, and think we can massively improve performance if we continue to invest in this.

## SP1 ZK Email

For server side flows, this has the potential to be very customizable and extensible due to the insane speed of plonky3. We benchmarked RSA-SHA256 and found the unoptimized version to be 11M constraints, an order of magnitude over Tendermint. We think this can be majorly optimized via optimized libraries. We intend to ship an MVP of ZK Email in SP1 as soon as an on-chain verifier is released. However, it will be several months after that until this is as fully audited and battle-tested as circom to be mainnet-ready. However, we are fully ready to throw away our entire serverside circuit codebase if we see performance + security here match circom, due to increased ease of use. We do not think performance will be comparable to halo2.

## Email Wallet
Using circom zk-email, we have a demo of a wallet signing flow only using emails instead of traditional seed phrases. You can try our current testnet V0 deployed demo of sending ERC20s via sending emails at [emailwallet.org](https://emailwallet.org), view the docs at [docs.sendeth.org](http://docs.sendeth.org), and see the slides at [slides.prove.email](http://slides.prove.email). We are adding an extensions layer and privacy layer to allow developers to trigger arbitrary Ethereum functions from email subjects, decentralizing and allowing self-hosting the relayer and DKIM key update code, and auditing it for a V1 release. We intend for this to be a powerful web3 onboarding protocol.

## Relayer
Our open source [relayer](https://github.com/zkemail/email-wallet/tree/main/packages/relayer) allows anyone to self-host or cloud-host with the ability to:
1) Use our Dockerimage to immediately deploy any ZK proof to a rapid, autoscaled, 64 core proving instance to do proofs in the cloud. Note that privacy will be leaked to AWS in this case, so the only usecase is succinctness.
2) Interface with the ZK proving protocol via sending emails, via built-in SMTP and IMAP servers that can authenticate with any gmail account

## Other public goods
We have produced serveral OSS public goods. We would like to publish them on NPM and Cargo soon, but till then you can directly refer to these repos.

- **Halo2 Benchmarking**: We have open sourced a [halo2 wasm benchmarking repo](https://github.com/Divide-By-0/halo2-secp) that runs any halo2 wasm code in the browser on 100 instances in parallel for any browser on any operating system (mobile or desktop), and spits out the mean running time and variance -- we intend to publish this as an easy to use Cargo and NPM package soon for others to use.
- **Halo2 Optimizations**: We have various halo2 circuits to split verifiers, divide circuits for faster parallel client side proving, and many others.
- **DKIM Selector Scrapers**: We have a [client-side-only selector scraper website](https://github.com/zkemail/selector-scraper) that we internally use to get selectors for uncommon websites which we have received emails from in the past, which stores it to a database.
- **Circom Hash to Curve on the Grumpkin Curve**: Our [circuits](https://github.com/zkemail/circom-grumpkin) enable proving the private set intersection (PSI) protocol in ZK, which we use for decentralized relayer communication, so that relayers cannot censor unfavorable queries.  

## Help out!
We are an open source project and decentralized protocol. If you are interested in giving, support us on [Gitcoin Grants](https://explorer.gitcoin.co/#/round/10/0x9331fde4db7b9d9d1498c09d30149929f24cf9d5/0x9331fde4db7b9d9d1498c09d30149929f24cf9d5-62)!

If you are interested in helping, reach out us on [email](mailto:aayushg@mit.edu) or telegram/twitter (yush_g)! We will give a small grant (between $50 - $200) for **all good-faith implementations** of any of the projects or issues below, or any completed end-to-end original idea. 

For more information on how to contribute look at our [contribution guide](https://zkemail.gitbook.io/zk-email/contributing).

Roadmap:
Q2 2023: Release initial ZK email SDKs. Release MVP of email wallet at Zuzalu. [DONE]
Q3 2023: Expand our core repos into more robust SDK, interate with developers, continue to user test the email wallet V0, prototype the email wallet V1. [DONE]
Q4 2023: Shipping a V1 zk email wallet on mainnet (with extensions!) on a short-term mainnet demo end to end. Test-drive SDK at Zuconnect Hackathon 2023. Make ZK regex easier to use. [DONE]
Q1 2024: Release polished, one-click button integrations for applications with email wallets. Publish a specialized wallet recovery plugin as well as a magic.link-style ephemeral key account abstraction login with zkemail solution.
Q2 2024: Finish halo2 code audit, as well as a full rewrite with Nova + recursive verification. This will allow a generic email circuit to live on each chain, where people can swap in and out regexes that can be recursively proven in Nova. This will obliviate the need for trusted setups for new zk email proofs, and make audits of future zk email ideas more tighly scoped! 

Our SDK has released a stable 1.0 version as of November, with a first round of audit fixes implemented from Secbit Labs. Note that there may still be breaking, back-incompatible changes pushed.

Here are specific project ideas, for which we will give a grant for any successful implementation.

### Core Infrastructure Ideas

| Title | Description |
|-------|-------------|
| **Issue Resolution** | Resolve issues in [zk-email-verify](https://github.com/zkemail/zk-email-verify/issues) or [zk-regex](https://github.com/zkemail/zk-email-verify/issues) repo, which can make the core protocol both faster and more secure! We have marked a number of accessible issues with 'Good First Issue' and 'Help Wanted'. |
| **Deploy Halo2 Benchmarking Library** | Deploy our [halo2 wasm benchmarking library](https://github.com/Divide-By-0/halo2-secp) to Cargo and NPM as a CLI tool. Alternatively upstream it directly to halo2. Ideally, it comes with an easy-to-use frontend where you can drag-and-drop a WASM or halo2 repo in. Also, add this as a button to [Axiom's halo2repl](https://docs.axiom.xyz/zero-knowledge-proofs/halo2-repl). |
| **Add Features to ZK Regex** | Our [regex to circom/halo2 library](https://github.com/zkemail/zk-regex) supports a limited set of regex syntax (specified on [zkregex.com](https://zkregex.com/min_dfa)). Add features like repitition, unmatched groups, and more robust parsing. Also add a feature that uploads it to a registry thats auto-published to NPM once a teammate approves it. |
| **Recursive Verifier in Nova** | Jordi began a Nova recursive verifier for Circom but never finished it. Lev and Arnacube have various ideas to finish it, which I am optimistic about. This will let us massively accelerate client-side circom proving |
| **Attachment Parsing Circuit** | Add a second base64 decoding as well as a simple image, txt, pdf, or other file format decoding algorithm to allow running regex inside of attachments in emails. |
| **Deploy Package/Website to Auto-deploy Autoscaled Circom Prover Endpoint** | Our [relayer coordinator]([https://github.com/zkemail/zk-regex](https://github.com/zkemail/relayer/blob/main/coordinator.py)) has a [Docker image](https://github.com/zkemail/relayer/blob/main/Dockerfile) for a rapidsnark prover that can store a circom zkey, then have a modal API endpoint and which to receive an input.json. This infrastructure can easily be made into a broader public good with a simple frontend that lets anyone upload whatever circom file they want, then automatically deploy an endpoint for them that would zk-prove it, or a button on zkrepl. |
| **ZK Repl on Cloud** | [zkrepl](https://zkrepl.dev) now supports @zk-email packages on NPM. However, it often exceeds the 4GB browser proving limit. Deploy a server-based zk-repl that autospins up an on-demand [modal](https://modal.com) server to run proving. You can base your modal instance on our [rapidsnark Dockerfiles](https://github.com/iden3/rapidsnark/pull/16) -- you can also take inspiration from how our modal-based [relayer coordinator]([https://github.com/zkemail/zk-regex](https://github.com/zkemail/relayer/blob/main/coordinator.py)) builds a [staged circom Dockerfile](https://github.com/zkemail/relayer/blob/v0/emailwallet.Dockerfile), then runs on-demand fast proving with an API endpoint. |
| **DNSSEC Advocacy** | Help advocate to companies that run big mailservers to enable DNSSEC, as less than 2% of companies have it on -- with DNSSEC, DKIM key verification on-chain will be significantly better. |
| **Halo2 Yul Memory Optimization** | Axiom and EZKL have Yul verifiers in halo2 that are quite wasteful with memory accesses, and make contracts inefficient and hard to deploy on-chain. Make them more efficient. |

### Project Ideas

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
| **2FA On-Chain** | Account transactions on-chain above a certain amount need to be verified via an email from your email address as well. Utilize a relayer to automatically prompt users when they see a transaction on chain, and proof of a confirmation reply with a later timestamp will auth the transaction. Will likely be easiest to fork email wallet V1 relayers and parsing to create this. |
| **Multisig Control via Email** | Native integration with SAFE or other multi-sig wallets to allow zk-emails to be direct multisig signers, interacting and approving transactions via emails. |
| **Legal Discovery** | When subpoena'd, people can turn over only a relevant subset of their emails, not all of them. I don't know if this is robust though, since unless Gmail commits to a Merkle root of all your emails or something, people can always hide whatevver emails they want from the proving process. |
| **DNSSEC Lobbying** | Lobby providers like Google, Outlook etc, to enable DNSSEC on their keys in order to have more permissionless key upgrades. This is a great fit for someone with less ZK experience or who is really good at talking to people. |
| **Emailing an Image Mints an NFT** | Add an extra base64 decoding step atop the attachments, and then reformat it to have the image on-chain. This will let someone email you an image, and it automatically goes on-chain (i.e. can directly mint an NFT for it, if you so wish). |
| **EZKL + ZK Email** | Use machine learning to parse an email contents and reveal only the outputs of the ML model. This can concern NLP summary of an email, or parsing of a document (again if you add the base64 decoding first). |

<img width="601" alt="proofofemail coming 10.13.22" src="https://user-images.githubusercontent.com/4804438/195414950-629e0e13-dea2-4f0c-a433-2bf3b2151533.png">


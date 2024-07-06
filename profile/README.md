# [ZK Email](https://prove.email)
This is the official organization for [ZK Email](https://prove.email), created by [yush_g](https://twitter.com/yush_g), [sora suegami](https://twitter.com/SoraSue77), and [sampriti](https://twitter.com/sampriti0). Thanks to all current core contributors, including [Saleel](https://twitter.com/_saleel), [Aditya](https://github.com/Bisht13), [Elo](https://github.com/Metachaser24), [Shubham](https://github.com/wryonik/), and [Wataru](https://github.com/wshino). Thanks to [Vivek](https://twitter.com/viv_boop), [Tyler](https://twitter.com/AtHeartEngineer), [Rasul](https://curryrasul.com/), and [Andy](https://twitter.com/AndyGuzmanEth) for helping out from the PSE side, to the [many open source contributors](https://github.com/zkemail/zk-email-verify/graphs/contributors) who have made pull requests, and to [0xPARC](https://0xparc.org), [EF PSE](https://pse.dev), and [Gitcoin supporters](https://explorer.gitcoin.co/#/round/137/0xa1d52f9b5339792651861329a046dd912761e9a9/0xa1d52f9b5339792651861329a046dd912761e9a9-17) for providing grants to support this work! You can see applications and links at [prove.email](https://prove.email) and follow our progress on Twitter on [@zkemail](https://twitter.com/zkemail).

On this guide, you can see a breakdown of each main product/repository, a [timeline](https://github.com/zkemail#roadmap) describing our future roadmap, and a set of [related project ideas](https://github.com/zkemail#grants)!

## Circom zk-email
We have [NPM SDKs](https://www.npmjs.com/search?q=%40zk-email) for the frontend functions, circuits, and smart contracts, that should allow you to quickly deploy new circom instances of zk-email.

We have an [end-to-end circom implementation](https://github.com/zkemail/zk-email-verify/) with a live demo for Proof-of-Twitter NFTs on Goerli at [https://zkemail.xyz](https://zkemail.xyz). Circom shines with it's succinctness and server-side speed, which proves zk-email circuits in under 30 seconds. On the client side, it takes about 6 minutes to run in browser and does a 1gb download, hence our desire to work on a halo2 version. 

## ZK Regex
In order construct new proofs, we also have a [regex library that does circom/halo2 codegen for arbitrary regex strings via configuration files and a CLI](https://github.com/zkemail/zk-regex), as well as a [DFA visualization site](https://zkregex.com/min_dfa) to help you convert a regex into the same states as our circom generator \[thanks to CyberZHG for the base repo\]. Thanks to Javier for a [work in progress UI](https://zk-regex-ui.pages.dev/) to convert regexes to circom circuits rapidly. 

## Halo2 zk-email
We have finished a halo2 version of zk-email, which we expect to 10x improve proving speed for zk-email applications that require client side privacy. To do so, we pair our zk-regex library with the [halo2-regex circuits](https://github.com/zk-email-verify/halo2-regex/) that create arbitrary regex circuits in halo2. We also have custom [base64 encoding + decoding circuit](https://github.com/zk-email-verify/halo2-base64) and the first-ever [halo2 RSA + SHA256](https://github.com/zk-email-verify/halo2-rsa) built with Axiom's flexgates and optimizing Brechtpd + PSE's SHA256. These public goods have been used for several applications regarding provenant data in the wild, but the halo2 work has not been audited yet. We have heavily optimized for proving-time, and we expect all of our circuits to be verified recusively on chain cheaply. 

We currently have a fast client side proof (~20 seconds) with split-up deployed contracts to allow L2 deployments. This can be proved on an L2 for 48M gas [~about $8 on Arbitrum]. We hope to optimize this over time. You can alternatively feed into an autoscaling recursive halo2 aggregator in the cloud with a GPU prover (~300 seconds on a massive machine without a GPU, ~60 seconds with a GPU) that maintains zero knowledge and compresses the proof to about 500K gas, with a ~500MB zkey. We will work on improving both performance and memory over time, and think we can massively improve performance if we continue to invest in this.

## ZK Email in other Higher Level Languages

For server side flows, SP1 has the potential to be very customizable and extensible due to the insane speed of plonky3. We benchmarked RSA-SHA256 and found the unoptimized version to be 11M constraints, 2 orders of magnitude bigger than ours (and their SHA256 precompile example). We think this can be majorly optimized via optimized libraries like big integer math in SP1. Regex is also similarly 10-100x slower. We expect these library optimizations, on-chain SP1 verifiers, and end to end audits to take until the end of 2024. We intend to ship an MVP of ZK Email in SP1 once those are ready. We are fully ready to throw away our entire circuit codebases if we see performance + security in any higher level language match circom.

## Account Recovery

You can use decentralized email gaurdians via zk email to recover your AA wallets, as we describe in [this ZK Summit 11 explainer video](https://www.youtube.com/watch?v=rZTNzvLXB14). The account recovery module is 7579-compatible and 4337-compatible, but can also be used natively. We are currently auditing integrations into Safe, Clave, Soul, Rhinestone, and so on, and our audits should be finished by July 2024. You can try the Base Sepolia testnet demo for Gnosis Safe at [https://prove.email/recovery](prove.email/recovery).

The flow of account recovery will be that AA wallets specify email addresses as guardians, those email addresses receive emails to accept gaurdianship, and if the keys are ever lost and the wallet needs to be recovered, then guardians can simply send an email with the users new public key, to save their assets after a timelock. You can read more at [our blog post on acccount recovery with ZK Email](https://prove.email/blog/recovery).

## Email Wallet

You can send tokens and NFTs to and from email addresses, even ones that haven't signed up for this service yet. This makes this integration particularly useful for airdrops to email addresses, for instance.

Using circom zk-email, we have a demo of a wallet signing flow only using emails instead of traditional seed phrases. You can try our current Base Sepolia demo of sending ERC20s via sending emails at [emailwallet.org](https://emailwallet.org), view the docs at [emailwallet.org/docs](http://emailwallet.org/docs), and see the slides at [prove.email/slides](http://prove.email/slides). You can use email wallet extensions to allow developers to trigger arbitrary Ethereum functions from email wallets, decentralizing and allowing self-hosting the relayer and DKIM key update code, and auditing it for a V1 release. We intend for this to be a powerful web3 onboarding protocol.

## Safe Signers

You can use email addresses directly as signers on existing Safe{Wallet}s by following the flow demonstrated at [this video](https://www.youtube.com/watch?v=rT7zLiOYX8s&t=2715s) on [emailwallet.org](https://emailwallet.org). We are publicly announcing this at EthCC 2024.

## Login with ZK Email

The flow of login with email will be that users put in their email addresses into a website, and then replying to a confirmation email logs them in. Behind the scenes, they are getting an ephemeral ECDSA session key with temporary permissions to the assets in their email wallet. The magic-link-style email comes from a relayer and authorizes the ephemeral key, and any reply to that email confirms that ephemeral key as the signer for that email address on any website. That website can continue to use that browser-specific temporary ECDSA keypair until the user switches or loses their device, at which point they can do the flow again to authorize additional ECDSA keypairs. This will be a completely decentralized drop-in replacement for signin with email for crypto-native apps that expect ECDSA signers. We currently have a spec and expect to release this in Q3 2024.

## Relayer

Our open source [relayer](https://github.com/zkemail/email-wallet/tree/main/packages/relayer) allows anyone to self-host or cloud-host with the ability to:
1) Use our Dockerimage to immediately deploy any ZK proof to a rapid, autoscaled, 64 core proving instance to do proofs in the cloud. Note that privacy will be leaked to AWS in this case, so the only usecase is succinctness.
2) Interface with the ZK proving protocol via sending emails, via built-in SMTP and IMAP servers that can authenticate with any gmail account

## Other public goods

We have produced several OSS public goods. We would like to publish them on NPM and Cargo soon, but till then you can directly refer to these repos.

- **Halo2 Benchmarking**: We have open sourced a [halo2 wasm benchmarking repo](https://github.com/Divide-By-0/halo2-secp) that runs any halo2 wasm code in the browser on 100 instances in parallel for any browser on any operating system (mobile or desktop), and spits out the mean running time and variance. We intend to publish this as an easy to use Cargo and NPM package soon for others to use.
- **Halo2 Optimizations**: We have various halo2 circuits to split verifiers, divide circuits for faster parallel client side proving, and many others.
- **DKIM Selector Scrapers**: We have a [client-side-only selector scraper website](https://github.com/zkemail/selector-scraper) that we internally use to get selectors for uncommon websites which we have received emails from in the past, which stores it to a database.
- **Circom Hash to Curve on the Grumpkin Curve**: Our [circuits](https://github.com/zkemail/circom-grumpkin) enable proving the private set intersection (PSI) protocol in ZK, which we use for decentralized relayer communication, so that relayers cannot censor unfavorable queries.  

## Audits

Our SDK has released a stable 1.0 version as of November, with a first round of audit fixes implemented from Secbit Labs. Note that there may still be breaking, back-incompatible changes pushed.

##  Roadmap

Our goal is to make the blockchains more accessible via email interfaces, and to make private identity attestations easy and accessible. 

**2023 High Level Plan:** Experiment with anonymous email wallet at a production level, and discover what on-chain tooling to focus on.

Q2 2023: Release initial ZK email SDKs. Release MVP of email wallet at Zuzalu. [DONE]

Q3 2023: Expand our core repos into more robust SDK, interate with developers, continue to user test the email wallet V0, prototype the email wallet V1. [DONE]

Q4 2023: Shipping a V1 zk email wallet on mainnet (with extensions!) on a short-term mainnet demo end to end. Test-drive SDK at Zuconnect Hackathon 2023. Make ZK regex easier to use. [DONE]

**2024 High Level Plan:** Release and scale production level deployments for account recovery, 2fa, and oauth email login. Innovate and expand on easy and scaled email proofs for identity attestations.

Q1 2024: Release polished, one-click button integrations for applications with email wallets. [DONE] Release account recovery SDKs and SDKs for people to make new zk email apps, via writing [code in Solidity to parse email subjects](https://github.com/zkemail/ether-email-auth). [DONE] 

Q2 2024: Build the [largest DKIM key registry on Earth](https://archive.prove.email) for storing historical DKIM keys. [DONE] Release an MVP for 2FA for gnosis safe email signers. [DONE, live on emailwallet.org] Publish a specialized wallet recovery plugin [DONE], as well as a magic.link-style ephemeral key account abstraction login with zkemail solution [SPEC DONE]. Attempt a rewrite for fast client-side proofs via ZKBoo [DONE]/VOLE/Binius + Nova-based [DONE] recursive verifications. Audit latest ZK Email circuits and regex updates again. [DONE]

Q3 2024: Release a registry for all existing ZK email proofs and regexes, and an SDK by which to make new proofs or access existing proofs in a small handful of lines of Typescript. Focus on scaling up our production deployments of account recovery, 2fa, and oauth. Do halo2 rewrites, and replace the recursive proving code with recursive proofs with Axiom or Nebra, and optimistic ZK with AlignedLayer. Focus on shipping many identity attestations via a single email login into Gitcoin Passport and Metrics Garden for OP RPGF. If we have extra time, try to upgrade halo2 to use Axiom's passthrough recursive verifiers, to avoid redeploying new proving keys or infra for each new regex.

Q4 2024: Rewrite all of zk email in Binius? Continue to scale production level code? Start exploring FHE on top of zk email with cursive.team?

## Help out!
We are an open source project and decentralized protocol. If you are interested in giving, support us on [Gitcoin Grants](https://explorer.gitcoin.co/#/round/10/0x9331fde4db7b9d9d1498c09d30149929f24cf9d5/0x9331fde4db7b9d9d1498c09d30149929f24cf9d5-62)!

For more information on how to contribute look at our [contribution guide](https://zkemail.gitbook.io/zk-email/contributing).

## Grants

Here are specific project ideas, for which we will give a grant for any successful implementation. Feel free to contact us ([twitter](twitter.com/yush_g), [telegram](t.me/zkemail)) with what you're interested in, for more info on grant amounts. Grants can range from $50 - $4000. You can also suggest your own project!

### Project Ideas

| Title | Description |
|-------|-------------|
| **Bountied Whistleblowing Project** | A bounty platform for leaks where people can bid on leaks with specific sources (i.e. from `@___`) and specific text (i.e. includes the phrase "`___`"). It creates an escrow system on chain where others can upload those anonymized leaks along with zk-email proofs that they satisfy the regexes, and reveal only the parts that they want to reveal. |
| **Arxiv Donation Project** | Put in an arxiv link, and a bot scrapes all the emails out of the PDF/Arxiv itself. It then scrapes all of the emails off of all of the dependencies and allows the donor to reweight them based on where they appeared in the text (i.e. it defaults to something like, cited in previous work or methods splits 40% of the donation, authors cited in intro split 10% of the funds or whatever). It then deploys zk-email wallets for all of them and sends them the money. |
| **Automatic Liquidity Provider for ZKP2P** | Run a service to provide liquidity that automatically does the counterparty email proof in the background as a liquidity provider, so that the user just has to provide a Venmo username and send the money, and only provide the email proof as a backup.
| **AttestationStation Attestations for Twitter etc** | Convert the Twitter demo to an official AttestationStation/PolygonID attestation. |
| **Automatic Spotify Splits for AI Voice Royalities** | Prove via zk-email on Spotify confirmation emails and EZKL proof of voi e via ML, that you used an artists voice and split profit with them. Details at https://hackmd.io/Nf8mSSKwRIu3GYyhGq5f9A |
| **Proof of Residence** | Prove via some emailed confirmation sent by some government or service that you filed taxes or payed for property in that country, and make only the country public. |
| **Proof of Credit Score** | Enable undercollateralized, crypto lending by allowing someone to prove their credit score on-chain via a confirmaton email from a credit score provider. |
| **General Oracles** | Prove any oracled data in emails, such as a NASDAQ daily ticker email for the price of some stock, or a Robinhood spot price confirmation. Tradingview Alerts should be able to support this. |
| **General Redaction Interface** | Either via a Chrome extension sidebar on Gmail, or sign in with Gmail via using zkp2p's Typescript module, pull up a person's email and let them choose any email, then drag points they want to reveal, and set the mask + text as a public output but hide the redacted full input, so anyone can prove and publish redacted emails. 
| **Investor Interest** | Prove some investor emailed you a term sheet, and mint a credential that lets you anonymously disclose how your interactions with them were. |
| **Legal Discovery** | When subpoena'd, people can turn over only a relevant subset of their emails, not all of them. I don't know if this is robust though, since unless Gmail commits to a Merkle root of all your emails or something, people can always hide whatevver emails they want from the proving process. |
| **DNSSEC Lobbying** | Lobby providers like Google, Outlook etc, to enable DNSSEC on their keys in order to have more permissionless key upgrades. This is a great fit for someone with less ZK experience or who is really good at talking to people. |
| **Emailing an Image Mints an NFT** | Add an extra base64 decoding step atop the attachments, and then reformat it to have the image on-chain. This will let someone email you an image, and it automatically goes on-chain (i.e. can directly mint an NFT for it, if you so wish). |
| **EZKL + ZK Email** | Use machine learning to parse an email contents and reveal only the outputs of the ML model. This can concern NLP summary of an email, or parsing of a document (again if you add the base64 decoding first). |

### Core Infrastructure Ideas

| Title | Description |
|-------|-------------|
| **ZK PDA** | Build a push down automata the same way we built zk-regex with DFAs, allowing us to parse non regular languages such as JSON as well. |
| **ZK Quoted Printable Encoding Circuit** | Build a circuit that verifies if a string has been correctly quoted-printable encoded. We need this for many emails i.e. from Twitter or Instagram. |
| **zkscan** | Improve [zscan](https://github.com/zkHubHQ/zscan/issues/2) to make it work, or build your own zk etherscan-style scanner that lets you see, parse, and verify proofs more easily from on-chain transactions. Make it so that anyone can automatically parse public inputs from the calldata within the explorer, you can contribute to the trusted setup yourself (on an unofficial fork for the team to adopt if they wish), you can view who contributed to the trusted setup in the past, you can verify what the 'nullifier' value is, you can check what the downstream libraries and circuits used in the circuits are, and you can see the instances of that proof or contract on other blockchains. |
| **Fix Protonmail DKIM Signatures** | Fix the base64 decoding, decryption, header ordering, and UTF 8 decoding to support Protonmail DKIM verification. More details [here](https://github.com/ProtonMail/proton-bridge/issues/321).
| **Issue Resolution** | Resolve issues in [zk-email-verify](https://github.com/zkemail/zk-email-verify/issues) or [zk-regex](https://github.com/zkemail/zk-email-verify/issues) repo, which can make the core protocol both faster and more secure! We have marked a number of accessible issues with 'Good First Issue' and 'Help Wanted'. |
| **Deploy Halo2 Benchmarking Library** | Deploy our [halo2 wasm benchmarking library](https://github.com/Divide-By-0/halo2-secp) to Cargo and NPM as a CLI tool. Alternatively upstream it directly to halo2. Ideally, it comes with an easy-to-use frontend where you can drag-and-drop a WASM or halo2 repo in. Also, add this as a button to [Axiom's halo2repl](https://docs.axiom.xyz/zero-knowledge-proofs/halo2-repl). |
| **Add Registry to ZK Regex** | Our [regex to circom/halo2 library](https://github.com/zkemail/zk-regex) supports a limited set of regex syntax (specified on [zkregex.com](https://zkregex.com/min_dfa)). Add a registry that lets people choose zk regex circuits. Might be good to approach with Succinct Alpha. |
| **Add Automatic Email to Regex Converter** | Add a frontend that lets someone upload two emails, highlight the common things they want to expose, then automatically calculate the regex that would constrain the rest and output that single value, and generate the zk circuit for it. |
| **ZK Regex Feature Parity** | Convert zkregex.com to use our new zk-regex library. |
| **Recursive Verifier in Nova** | Use [Sonobe](https://github.com/dmpierre/sonobe-workshop) to generate an EVM verifier for a Hypernova/Protogalaxy version of zk-email. This will let us potentiallly accelerate client-side circom proving. |
| **Attachment Parsing Circuit** | Add a second base64 decoding as well as a simple image, txt, pdf, or other file format decoding algorithm to allow running regex inside of attachments in emails. |
| **Deploy Package/Website to Auto-deploy Autoscaled Circom Prover Endpoint** | Our [relayer coordinator]([https://github.com/zkemail/zk-regex](https://github.com/zkemail/relayer/blob/main/coordinator.py)) has a [Docker image](https://github.com/zkemail/relayer/blob/main/Dockerfile) for a rapidsnark prover that can store a circom zkey, then have a modal API endpoint and which to receive an input.json. This infrastructure can easily be made into a broader public good with a simple frontend that lets anyone upload whatever circom file they want, then automatically deploy an endpoint for them that would zk-prove it, or a button on zkrepl. |
| **ZK Repl on Cloud** | [zkrepl](https://zkrepl.dev) now supports @zk-email packages on NPM. However, it often exceeds the 4GB browser proving limit. Deploy a server-based zk-repl that autospins up an on-demand [modal](https://modal.com) server to run proving. You can base your modal instance on our [rapidsnark Dockerfiles](https://github.com/iden3/rapidsnark/pull/16) -- you can also take inspiration from how our modal-based [relayer coordinator]([https://github.com/zkemail/zk-regex](https://github.com/zkemail/relayer/blob/main/coordinator.py)) builds a [staged circom Dockerfile](https://github.com/zkemail/relayer/blob/v0/emailwallet.Dockerfile), then runs on-demand fast proving with an API endpoint. |
| **DNSSEC Advocacy** | Help advocate to companies that run big mailservers to enable DNSSEC, as less than 2% of companies have it on -- with DNSSEC, DKIM key verification on-chain will be significantly better. |
| **Halo2 Yul Memory Optimization** | Axiom and EZKL have Yul verifiers in halo2 that are quite wasteful with memory accesses, and make contracts inefficient and hard to deploy on-chain. Make them more efficient. |


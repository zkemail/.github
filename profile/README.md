# [ZK Email](https://prove.email)
This is the official organization for [ZK Email](https://prove.email), created by [yush_g](https://twitter.com/yush_g), [sora suegami](https://twitter.com/SoraSue77), and [sampriti](https://twitter.com/sampriti0). Thanks to all current core contributors, including [Saleel](https://twitter.com/_saleel), [Aditya](https://github.com/Bisht13), [Elo](https://github.com/Metachaser24), [Shubham](https://github.com/wryonik/), and [Wataru](https://github.com/wshino). Thanks to [Vivek](https://twitter.com/viv_boop), [Tyler](https://twitter.com/AtHeartEngineer), [Rasul](https://curryrasul.com/), and [Andy](https://twitter.com/AndyGuzmanEth) for helping out from the PSE side, to the [many open source contributors](https://github.com/zkemail/zk-email-verify/graphs/contributors) who have made pull requests, and to [0xPARC](https://0xparc.org), [EF PSE](https://pse.dev), and [Gitcoin supporters](https://explorer.gitcoin.co/#/round/137/0xa1d52f9b5339792651861329a046dd912761e9a9/0xa1d52f9b5339792651861329a046dd912761e9a9-17) for providing grants to support this work! You can see applications and links at [prove.email](https://prove.email) and follow our progress on Twitter on [@zkemail](https://twitter.com/zkemail).

On this guide, you can see a breakdown of each main product/repository, a [timeline](https://github.com/zkemail#roadmap) describing our future roadmap, and a set of [related project ideas](https://github.com/zkemail#grants)!

## [ZK Email Proof Registry](https://registry-dev.zkregex.com/)

We have released a simple registry and SDK where you can define a new kind of proof in just a few minutes, and will automatically deploy infrastructure, a base repository, and on-chain verifiers for you, and then expose an SDK where you can use it from your frontend in just a few lines. We have [a beta version](https://registry-dev.zkregex.com/) up for server-side circom, but are working on increasing scalability, making the SDK, and adding more prover backends (Noir, SP1) to make it easier to use. We hope that all proofs in the future go through this infrastructure due to its ease of use.

## Circom zk-email
We have [NPM SDKs](https://www.npmjs.com/search?q=%40zk-email) for the frontend functions, circuits, and smart contracts, that should allow you to quickly deploy new circom instances of zk-email.

We have an [end-to-end circom implementation](https://github.com/zkemail/zk-email-verify/) with a live demo for Proof-of-Twitter NFTs on Goerli at [https://zkemail.xyz](https://zkemail.xyz). Circom proofs are short to verify on chain and very fast on a server with a GPU -- zk-email circuits server side are under 8 seconds with a GPU proof in general, and under 20 seconds even for the beefy Twitter proofs. On the client side, small proofs like subject proofs, or JWT proofs can happen in under 20-30 seconds. If you want to do body parsing, you should expect those client side proofs in Circom to not be very performant -- Twitter body proofs take about 4 minutes to run in browser after a 1gb download. Note that Noir proofs are substantially faster client side and will soon be the default in the SDK. We are fully ready to throw away our entire circuit codebases if we see performance + security in any other language match circom performance in the browser.

## ZK Regex
In order construct bespoke regexes or manually generate circom code, we also have a [regex library that does circom/halo2 codegen for arbitrary regex strings via configuration files and a CLI](https://github.com/zkemail/zk-regex), as well as a [DFA visualization site](https://zkregex.com/min_dfa) for the old regex compiler that gives you an idea of how we visualize the DFA states (though that latter website is now a bit outdated). Note that the new compiler will be doubly audited by mid October 2024.

## ZK Email in Noir

With Zac's latest sprints on ZK Email primitives like RSA, JSON, and SHA256, we have been able to release a [ZK Email Noir](https://github.com/zkemail/zkemail.nr) version in collaboration with Mach34. It is extremely performant on the client side, being ~2x faster on small proofs and ~5-10x speedups on the larger proofs. We will migrate all of our client side proofs to this soon. At the current moment, due to their slow witness generation, our circom ZK Email code on GPU is still faster on the server side. Soon, our Registry + SDK will compile to Noir as well!

## ZK Email in other Higher Level Languages

For server side flows, SP1 has the potential to be very customizable and extensible and performant. The latest RSA and SHA precompiles actually have comparable performance to circom on the server side (maybe like 2x slower?), but regex is still about 10x slower. We expect that a regex library optimization, on-chain SP1 verifiers, and end to end audits to take until the end of 2024, and we hope to ship MVPs of ZK Email in SP1 once those are ready. 

## Account Recovery

You can use decentralized email gaurdians via zk email to recover your AA wallets, as we describe in [this ZK Summit 11 explainer video](https://www.youtube.com/watch?v=rZTNzvLXB14). The account recovery module is 7579-compatible and 4337-compatible, but can also be used natively. We are currently auditing integrations into Safe, Clave, Soul, Rhinestone, and so on, and our audits should be finished by July 2024. You can try the Base Sepolia testnet demo for Gnosis Safe at [https://prove.email/recovery](prove.email/recovery).

The flow of account recovery will be that AA wallets specify email addresses as guardians, those email addresses receive emails to accept gaurdianship, and if the keys are ever lost and the wallet needs to be recovered, then guardians can simply send an email with the users new public key, to save their assets after a timelock. You can read more at [our blog post on acccount recovery with ZK Email](https://prove.email/blog/recovery).

## Email Wallet

You can send tokens and NFTs to and from email addresses, even ones that haven't signed up for this service yet. This makes this integration particularly useful for airdrops to email addresses, for instance.

Using circom zk-email, we have a demo of a wallet signing flow only using emails instead of traditional seed phrases. You can try our current Base Sepolia demo of sending ERC20s via sending emails at [emailwallet.org](https://emailwallet.org), view the docs at [emailwallet.org/docs](http://emailwallet.org/docs), and see the slides at [prove.email/slides](http://prove.email/slides). You can use email wallet extensions to allow developers to trigger arbitrary Ethereum functions from email wallets, decentralizing and allowing self-hosting the relayer and DKIM key update code, and auditing it for a V1 release. We intend for this to be a powerful web3 onboarding protocol.

## Safe Signers

You can use email addresses directly as signers on existing Safe{Wallet}s by following the flow demonstrated at [this video](https://www.youtube.com/watch?v=rT7zLiOYX8s&t=2715s) on [emailwallet.org](https://emailwallet.org). We are publicly announcing this at EthCC 2024.

## Login with ZK Email

The flow of login with email will be that users put in their email addresses into a website, and then replying to a confirmation email logs them in. Behind the scenes, they are getting an ephemeral ECDSA session key with temporary permissions to the assets in their email wallet. The magic-link-style email comes from a relayer and authorizes the ephemeral key, and any reply to that email confirms that ephemeral key as the signer for that email address on any website. That website can continue to use that browser-specific temporary ECDSA keypair until the user switches or loses their device, at which point they can do the flow again to authorize additional ECDSA keypairs. This will be a completely decentralized drop-in replacement for signin with email for crypto-native apps that expect ECDSA signers. We currently have a beta MVP you van try out at [prove.email/docs](https://prove.email/docs) > Oauth API.

## Audits

Our SDK has had several audits -- one by Y Academy and Secbit Labs in November 2023, three by PSE Security on the circuits, email-wallet, and account recovery, one by Ackee on account recovery smart contracts, one by ZKSecurity on the circuits, and one by Zellic on ether-email-auth. We released a stable 1.0 version in November 2023, our latest audited versions in Fall 2024. You can aee report PDFs and fixes in our [docs](https://zkemail.gitbook.io/zk-email/audits).

##  Roadmap

Our goal is to make the blockchains more accessible via email interfaces, and to make private identity attestations easy and accessible. 

**2023 High Level Plan:** Experiment with anonymous email wallet at a production level, and discover what on-chain tooling to focus on.

Q2 2023: Release initial ZK email SDKs. Release MVP of email wallet at Zuzalu. [DONE]

Q3 2023: Expand our core repos into more robust SDK, interate with developers, continue to user test the email wallet V0, prototype the email wallet V1. [DONE]

Q4 2023: Shipping a V1 zk email wallet on mainnet (with extensions!) on a short-term mainnet demo end to end. Test-drive SDK at Zuconnect Hackathon 2023. Make ZK regex easier to use. [DONE]

**2024 High Level Plan:** Release and scale production level deployments for account recovery, 2fa, and oauth email login. Innovate and expand on easy and scaled email proofs for identity attestations. Make all SDKs simple enough to implement in 10 lines of code.

Q1 2024: Release polished, one-click button integrations for applications with email wallets. [DONE] Release account recovery SDKs and SDKs for people to make new zk email apps, via writing [code in Solidity to parse email subjects](https://github.com/zkemail/ether-email-auth). [DONE] Rewrite all of our infra in Kubernetes to enable scaling. [DONE] Deploy a booth at EthDenver for users to tap NFC badges and claim NFTs to their email directly. [DONE]

Q2 2024: Build the [largest DKIM key registry on Earth](https://archive.prove.email) for storing historical DKIM keys. [DONE] Release an MVP for 2FA for gnosis safe email signers. [DONE, live on emailwallet.org] Publish a specialized wallet recovery plugin [DONE], as well as a magic.link-style ephemeral key account abstraction login with zkemail solution [SPEC DONE]. Attempt a rewrite for fast client-side proofs via ZKBoo [DONE]/VOLE/Binius + Nova-based [DONE] recursive verifications. Audit latest ZK Email circuits and regex updates again. [DONE]

Q3 2024: Release a [registry for all existing ZK email proofs](https://registry-dev.zkregex.com/) and regexes, and [an SDK](https://zkemail.gitbook.io/zk-email/zk-email-sdk) by which to make new proofs or access existing proofs in a small handful of lines of Typescript. [DONE] Focus on auditing and scaling up our production deployments of account recovery [DONE], 2fa, and oauth. Finish [a Noir version](https://github.com/zkemail/zkemail.nr) [DONE]. Deploy account recovery to mainnet for Soul Wallet [DONE] and Clave Wallet.

Q4 2024: Rewrite all of zk email in Noir and SP1, and natively integrate that into the registry. Deploy better, more scalable infra for the registry proofs. Continue to scale production level code, and add account recovery to more wallets. Add anonymous domain validation for full anonymity, and support more builders building Noir proofs. Maybe add a public key registry with an AVS? Add aggregation for the recursive proofs with Axiom or Nebra?

## Help out!
We are an open source project and decentralized protocol. If you are interested in giving, support us on [Gitcoin Grants](https://explorer.gitcoin.co/#/round/10/0x9331fde4db7b9d9d1498c09d30149929f24cf9d5/0x9331fde4db7b9d9d1498c09d30149929f24cf9d5-62)!

For more information on how to contribute look at our [contribution guide](https://zkemail.gitbook.io/zk-email/contributing).

## Grants

Here are specific project ideas, for which we will give a grant for any successful implementation. Feel free to contact us ([twitter](twitter.com/yush_g), [telegram](t.me/zkemail)) with what you're interested in, for more info on grant amounts. Grants can range from $50 - $4000. You can also suggest your own project! If you want to start with something much smaller, consider resolving issues in one of our repos like [zk-email-verify](https://github.com/zkemail/zk-email-verify/issues) or [view all the open issues through our org](https://github.com/search?o=desc&q=org%3Azkemail++&s=created&type=Issues&state=open) instead, which can make the core protocol both faster and more secure! We have marked a number of accessible issues with 'Good First Issue' and 'Help Wanted'.

### Project Ideas

These ideas primarily use our existing SDKs to build new classes of applications.

| Title | Description |
|-------|-------------|
| **Restaurant Reservation Trader** | Verify an email to ensure you have a reservation booked, then escrow and release payment only once that reservation has been correctly transferred (via another proof of transfer email confirmation). |
| **Bountied Whistleblowing Project** | A bounty platform for leaks where people can bid on leaks with specific sources (i.e. from `@___`) and specific text (i.e. includes the phrase "`___`"). It creates an escrow system on chain where others can upload those anonymized leaks along with zk-email proofs that they satisfy the regexes, and reveal only the parts that they want to reveal. |
| **Uniswap V4 Gated by Proofs** | A Uniswap pool that requires holding a ZK Email proof of a thing -- can be NFT gated as well if we use the proof-minted emails. If you can find an email for Proof of Accredited Investor, that might be a very interesting one, or even meme-y ones would be interesting. |
| **Gated Message Boards** | Gate a message board posting via just allowing people who can post valid proofs to pass. |
| **Anti-Arbitrage Uniswap Pools** | Make the fee much higher for trades that can prove proof of humanity via ZK Email proofs of KYC or Worldcoin proofs, disincentivizing arbitrage by bots that are unlinked to humans (i.e. maybe each proof only allows one trade, making bots harder and creating more 'local' notions of markets.) |
| **Arxiv Donation Project** | Put in an arxiv link, and a bot scrapes all the emails out of the PDF/Arxiv itself. It then scrapes all of the emails off of all of the dependencies and allows the donor to reweight them based on where they appeared in the text (i.e. it defaults to something like, cited in previous work or methods splits 40% of the donation, authors cited in intro split 10% of the funds or whatever). It then deploys zk-email wallets for all of them and sends them the money. |
| **Automatic Liquidity Provider for ZKP2P** | Run a service to provide liquidity that automatically does the counterparty email proof in the background as a liquidity provider, so that the user just has to provide a Venmo username and send the money, and only provide the email proof as a backup. |
| **AttestationStation Attestations for Twitter etc** | Convert the Twitter demo to an official AttestationStation/PolygonID attestation. |
| **Automatic Spotify Splits for AI Voice Royalities** | Prove via zk-email on Spotify confirmation emails and EZKL proof of voice via ML, that you used an artists voice and split profit with them. Details at https://hackmd.io/Nf8mSSKwRIu3GYyhGq5f9A |
| **Proof of Residence** | Prove via some emailed confirmation sent by some government or service that you filed taxes or payed for property in that country, and make only the country public. |
| **Proof of Credit Score** | Enable undercollateralized, crypto lending by allowing someone to prove their credit score on-chain via a confirmaton email from a credit score provider. |
| **General Oracles** | Prove any oracled data in emails, such as a NASDAQ daily ticker email for the price of some stock, or a Robinhood spot price confirmation. Tradingview Alerts should be able to support this. |
| **General Redaction Interface** | Either via a Chrome extension sidebar on Gmail, or sign in with Gmail via using zkp2p's Typescript module, pull up a person's email and let them choose any email, then drag points they want to reveal, and set the mask + text as a public output but hide the redacted full input, so anyone can prove and publish redacted emails. We are currently building this. |
| **Investor Interest** | Prove some investor emailed you a term sheet, and mint a credential that lets you anonymously disclose how your interactions with them were. |
| **Legal Discovery** | When subpoena'd, people can turn over only a relevant subset of their emails, not all of them. I don't know if this is robust though, since unless Gmail commits to a Merkle root of all your emails or something, people can always hide whatevver emails they want from the proving process. |
| **Emailing an Image Mints an NFT** | Add an extra base64 decoding step atop the attachments, and then reformat it to have the image on-chain. This will let someone email you an image, and it automatically goes on-chain (i.e. can directly mint an NFT for it, if you so wish). |
| **EZKL + ZK Email** | Use machine learning to parse an email contents and reveal only the outputs of the ML model. This can concern NLP summary of an email, or parsing of a document (again if you add the base64 decoding first). |

### Research Ideas

These ideas revolve around core cryptographic improvements, new standards, or interesting theoretical ideas that lead to concrete new abilities or speedups. 

| Title | Description |
|-------|-------------|
| **ZK Quoted Printable Decoding Circuit** | Build a circuit that verifies if a string has been correctly [quoted-printable decoded](https://github.com/mathiasbynens/quoted-printable/blob/7d490479723245f61c816c584fac41ba8e9cbdb5/src/quoted-printable.js#L21). We need this for many emails, especially from companies, and proving decodings are easier than encodings. We already have [newline removal](https://github.com/zkemail/zk-email-verify/pull/202/) (and will also have that in Noir soon) via a clever RLC trick, but not general decoding. |
| **DNSSEC Automation** | Less than 2% of companies use DNSSEC, making it very hard to verify old DKIM keys.  In order to help companies with big mailservers to enable DNSSEC, we should add an implementation of parent automation as specified by RFC 9615 to the CZ FRED DNS server implementation -- this is the open source library that many country TLDs use. With DNSSEC, DKIM key verification on-chain will be significantly more trustless. |
| **Make a PR to Protonmail to Not Mangle DKIM Signatures** | Fix the base64 decoding, decryption, header ordering, and UTF 8 decoding to support Protonmail DKIM verification. More details [here](https://github.com/ProtonMail/proton-bridge/issues/321). Note that it is impossible to do proofs of mails sent to Protonmail because they mangle the delimiters by [adding a random 128 bit hex value](https://github.com/ProtonMail/mimemessage.js/blob/d822999942ba10d504b9d0214314ff508517009f/lib/Entity.js#L175). You can see that they [decrypt with a fixed random string](https://github.com/ProtonMail/protoncore_ios/blob/38bda3e7b171049eba3ac57917d1c20bb733e8a5/libraries/Features/Sources/MailSending%2BBuilder.swift#L283) as well. This will likely be a PR to the Protonmail repo. |
| **Add Automatic Email to Regex Converter** | Add a frontend that lets someone upload two emails, highlight the common things they want to expose, then automatically calculate the regex that would constrain the rest and output that single value, and generate the zk circuit for it. |
| **Base64 Multipart Decoding Circuit** | Add a second base64 decoding of part of an email body, as well as a simple image, txt, pdf, or other file format decoding algorithm for individual multipart sections. Note that sometimes, individual sections inside emails are base64 encoded, like Booking.com's text. |
| **Attachment Parsing Circuit** | Combine the above mulitpart decoder with a simple image, txt, pdf, or other file format decoding algorithm to allow running regex inside of attachments in emails. Note that the Gmail preview does not show the signed attachments (you need to download the .eml file to see it), but those are signed. This might be easiest to first benchmark in a ZKVM before building fully. |
| **SIMD Optimizations in SnarkJS** | Implement SIMD optimizations like the zPrize WASM MSM winner, for all cryptographic operations running in WASM in a browser to greatly speed up SNARK proof computations. Note that WebGPU has a memory limit of 128MB in practice with their APIs, so I think SIMD is more promising. Maybe there's a way to include this automatically for Rust SIMD code in the Rust to Wasm conversion step with bindgen. |
| **Precompiles in SP1** | Further optimize the bigint library, RSA implementation, and regex implementation in SP1. Note that we have been working with the SP1 team and already have initial precompiles, but they can likely be optimized further. |
| **Automatic Solidity Contract Splitter** | It is very hard to deploy contracts over the 24kB limit, but it should be possible to just break up any function into smaller ones -- any contracts larger than 24kB that can have a generalized algorithm that determines where memory accesses are, and automatically splits up the function and puts any intermediate memory onto the stack when calling the rest of the function. |
| **ZK CFRGs** | Build a push down automata/cfrgs the same way we built zk-regex with DFAs, allowing us to parse non regular languages such as JSON as well. Edit: This is currently being worked on already. |
| **Device Specific Nullifiers via Webauthn** | Use webauthn/passkey zk proofs ([halo2](https://github.com/zkwebauthn/webauthn-halo2), [circom](https://github.com/privacy-scaling-explorations/webauth-circom)) to provide a proof of unique device, so that you can have per-device reputation with anonymous posts on places like [nozee.xyz](https://nozee.xyz).

### Core Infrastructure Ideas

These ideas primarily revolve around making our infrastructure faster or more general, without detailed cryptographic knowledge needed.

| Title | Description |
|-------|-------------|
| **create-next-app with signatures** | Make a PR to create-next-app to add SXG or RFC 9421 signature support by default, so that all frontend pages can be easily signed in Vercel. Related [SXG issue here](https://github.com/vercel/next.js/discussions/37640) and [here](https://github.com/google/sxg-rs/issues/272), and [9421 implementation here](https://github.com/pyauth/http-message-signatures). We recommend to use RFC 9421 as it is more modern, although there are more SXG implementations right now. Alternatively, make a npm package where in one install and import, you can add RFC 9421 to your website.
| **zkscan** | Improve [zscan](https://github.com/zkHubHQ/zscan/issues/2) to make it work, or build your own zk etherscan-style scanner that lets you see, parse, and verify proofs more easily from on-chain transactions. Make it so that anyone can automatically parse public inputs from the calldata within the explorer, you can contribute to the trusted setup yourself (on an unofficial fork for the team to adopt if they wish), you can view who contributed to the trusted setup in the past, you can verify what the 'nullifier' value is, you can check what the downstream libraries and circuits used in the circuits are, and you can see the instances of that proof or contract on other blockchains. |
| **ZK Repl on Cloud** | [zkrepl](https://zkrepl.dev) now supports @zk-email packages on NPM. However, it often exceeds the 4GB browser proving limit. Deploy a server-based zk-repl that autospins up an on-demand [modal](https://modal.com) server to run proving. You can base your modal instance on our [rapidsnark Dockerfiles](https://github.com/iden3/rapidsnark/pull/16) -- you can also take inspiration from how our modal-based [relayer coordinator]([https://github.com/zkemail/zk-regex](https://github.com/zkemail/relayer/blob/main/coordinator.py)) builds a [staged circom Dockerfile](https://github.com/zkemail/relayer/blob/v0/emailwallet.Dockerfile), then runs on-demand fast proving with an API endpoint. |
| **Deploy Package/Website to Auto-deploy Autoscaled Circom Prover Endpoint** | Our [relayer coordinator]([https://github.com/zkemail/zk-regex](https://github.com/zkemail/relayer/blob/main/coordinator.py)) has a [Docker image](https://github.com/zkemail/relayer/blob/main/Dockerfile) for a rapidsnark prover that can store a circom zkey, then have a modal API endpoint and which to receive an input.json. This infrastructure can easily be made into a broader public good with a simple frontend that lets anyone upload whatever circom file they want, then automatically deploy an endpoint for them that would zk-prove it, or a button on zkrepl. |
| **ZK Regex Feature Parity** | Convert zkregex.com to use our new zk-regex library. |
| **Rust IMAP** | The implementations of rust imap in the default Rust imap crate are very bad and often drop the connection and stop tracking emails. Resolve issues on that repo. |

## Other public goods

We have produced several OSS public good repositories.

- **Halo2 Benchmarking**: We have open sourced a [halo2 wasm benchmarking repo](https://github.com/Divide-By-0/halo2-secp) that runs any halo2 wasm code in the browser on 100 instances in parallel for any browser on any operating system (mobile or desktop), and spits out the mean running time and variance. We intend to publish this as an easy to use Cargo and NPM package soon for others to use.
- **Halo2 Optimizations**: We have various halo2 circuits to split verifiers, divide circuits for faster parallel client side proving, and many others.
- **DKIM Selector Scrapers**: We have a [DKIM key and selector archive](https://archive.prove.email) that anyone can contribute to, and that we use to get selectors for uncommon websites which we have received emails from in the past, and stores it to a database.
- **Circom Hash to Curve on the Grumpkin Curve**: Our [circuits](https://github.com/zkemail/circom-grumpkin) enable proving the private set intersection (PSI) protocol in ZK, which we use for decentralized relayer communication, so that relayers cannot censor unfavorable queries.

### Halo2 zk-email
We had previously prioritized a halo2 version of zk-email, but unfortunately halo2 is not being particularly actively maintained in the long term. It did roughly 10x improve browser proving speed for zk-email applications that required client side privacy. To do so, we paired our zk-regex library with the [halo2-regex circuits](https://github.com/zk-email-verify/halo2-regex/) that create arbitrary regex circuits in halo2. We also made custom [base64 encoding + decoding circuit](https://github.com/zk-email-verify/halo2-base64) and the first-ever [halo2 RSA + SHA256](https://github.com/zk-email-verify/halo2-rsa) built with Axiom's flexgates and optimizing Brechtpd + PSE's SHA256. These public goods have been used for several applications regarding provenant data in the wild, but the halo2 work has not been audited yet. We have heavily optimized for proving-time, and we expect all of our circuits to be verified recusively on chain cheaply. 

We currently have a fast client side proof (~20 seconds) with split-up deployed contracts to allow L2 deployments. This can be proved on an L2 directly for 48M gas [~about $8 on Arbitrum], or fed into an autoscaling recursive halo2 aggregator in the cloud with a GPU prover (~60 seconds with a GPU, probably less with multiple GPUs) that maintains zero knowledge and compresses the proof to about 500K gas, with a ~500MB zkey. We hoped to improve both performance and memory over time.

### Relayer

Our open source [relayer](https://github.com/zkemail/email-wallet/tree/main/packages/relayer) allows anyone to self-host or cloud-host with the ability to:
1) Use our Dockerimage to immediately deploy any ZK proof to a rapid, autoscaled, 64 core proving instance to do proofs in the cloud. Note that privacy will be leaked to AWS in this case, so the only usecase is succinctness.
2) Interface with the ZK proving protocol via sending emails, via built-in SMTP and IMAP servers that can authenticate with any gmail account.

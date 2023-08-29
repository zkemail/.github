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

If you are interested in helping, reach out [us](mailto:aayushg@mit.edu)! We are giving grants for full or part time work to deploy libraries of our work to npm, integrate into zk repl, novel zk optimizations, applications in the proof of identity and defi space, and a rewrite via lasso/jolt and/or new versions of nova. We are hoping in Q3-Q4 2023 to ship the zk email wallet prototype with extensions, end to end, and hope to open the protocol to extension developers in 2023 as well as sponsor hackathons and bigger teams to build atop the protocol.

<img width="601" alt="proofofemail coming 10.13.22" src="https://user-images.githubusercontent.com/4804438/195414950-629e0e13-dea2-4f0c-a433-2bf3b2151533.png">


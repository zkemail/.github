# ZK Email
This is the official organization for zk proof of email projects, funded by 0xPARC and EF PSE; it is created and maintained by [yush_g](https://twitter.com/yush_g) and [sora suegami](https://twitter.com/SoraSue77). 

## Circom zk-email
We have an [end-to-end circom implementation](https://github.com/zk-email-verify/zk-email-verify/) with a live demo on Goerli at [https://zkemail.xyz](https://zkemail.xyz). We also have a [regex library that does circom codegen for arbitrary regex strings](https://github.com/zk-email-verify/zk-regex/). It takes about 6 minutes to run in browser and does a 1gb download, hence our desire to work on a halo2 version.

## Halo2 zk-email
We also working on a halo2 version of zk-email, which we expect to 10x improve proving speed. To do so, we pair our zk-regex library with the [halo2-regex circuits](https://github.com/zk-email-verify/halo2-regex/) that create arbitrary regex circuits in halo2. We also have [base64 encoding + decoding circuit] (https://github.com/zk-email-verify/halo2-base64) and [halo2 RSA + SHA256](https://github.com/zk-email-verify/halo2-rsa) built with Axiom's flexgates and Brechtpd  + PSE's SHA256. We have heavily optimized for proving-time; everything fits in 2^13 rows, and we expect all of our circuits to be verified recusively on chain cheaply. 

## Other public goods
Our final OSS public good is a [halo2 wasm benchmarking repo](https://github.com/Divide-By-0/halo2-secp) that runs any halo2 wasm code in the browser on 100 instances in parallel for any browser on any operating system (mobile or desktop), and spits out the mean running time and variance -- we intend to library-ify this soon for others to use.

We are hoping in Q1-Q2 2023 to finish a halo2 zk-email circuit end to end.

<img width="601" alt="proofofemail coming 10.13.22" src="https://user-images.githubusercontent.com/4804438/195414950-629e0e13-dea2-4f0c-a433-2bf3b2151533.png">


## Peer list for irishub-1 (25 Feb 2021)

### Script for validating node availability
```bash
curl -s https://raw.githubusercontent.com/irisnet/mainnet/master/config/community-peers.md | grep '@' | grep -v 'raw.githubusercontent.com' | cut -d'@' -f2 | sed 's/:/ /' | xargs -n2 nc -zvw5 2>&1 | grep open
```

### Cosmostation (not seed) open
fdc0406afdd3acc63f74f5439e09104f663a7c1f@44.241.177.178:26656
090bcbe5302e6104821a96c4899912870db04cb9@52.11.128.123:26656
83b3f989f3ce089afdf733f8aa06e792d7e00c08@3.34.6.30:26656

### Node-A-Team (not seed) open
87f18756b93d835c59fe5ce2a8da51858837eb5b@54.180.15.28:26656

### Figment (not seed) open
90e48220190b16cad95145b6213d512d703e5617@138.197.158.189:26656

### melea (not seed)
7fad2da10c41b0c1e3c2ce6e708f7fa817b5e19d@135.181.56.26:46656

### Dokia (persistent peer)
ebfb43ca1b592b5f8a1faf3e2aa1a34e8e1099cc@iris01.dokia.cloud:26656

### IRISnet (seed) open
a17d7923293203c64ba75723db4d5f28e642f469@seed-2.mainnet.irisnet.org:26656

### justin sun (go figure) open
fbaf634a899c7aab3c159ce1a345122bbeca3717@209.133.200.154:26656

### syncnode (persistent) open
92fadc989ed29aee0d46afce3226f8565d1f36cb@144.91.116.17:46656

### BTC.Secure (not seed) open
895d5a5009d042108783a6aeb0991c5186a46617@144.76.96.47:26656

### Appeal2Heaven (not seed) open
40821d0ade83d582b29d748f37ecf7bef0a823d0@66.42.116.195:26656

### QOSGroup (not seed) open
f4af63df2f5c63428be776e56ffc2899fa47afdf@47.101.160.78:26656

### Masternode24 (not seed) open
08e2f9453541b104df84efa68ab2f0d242eb829b@176.9.47.69:26656

### Nodeasy (not seed)
db3dacf404840e067b2f59e304cb2b6662ec0cea@173.212.212.252:26656

### 01node (not seed) open
87525da8c830da2c3a861638a77f601278efd353@185.181.103.142:26656

### P2P.ORG (not seed) open
4ac6200974d3fd80a8e49d145a2c254ed37a9b9a@159.69.106.156:26656
4e02a4b4f4350ea2b770cd03dc41fedcadb13176@159.69.55.206:26656

### dolphintwo (not seed)
5d659b3edde90344489210f5be90c0682b54b997@45.32.72.122:16656

### AUDIT.one (not seed)
63f5646b5f9ce927241383a091b60f797796588f@143.110.240.198:26656

### stake.fish (not seed) open
84cc32adca3986b35953886ad075431d318a98b5@52.214.130.28:46656

### StakeLab (Persistent Peer)
5387e8f13cee43b47526d2d6a9d8ee65b14e4b8f@161.97.187.139:26656


## Peer list for irishub-2

### Script for validating node availability
```bash
curl -s https://raw.githubusercontent.com/irisnet/mainnet/master/config/community-peers.md | grep '@' | grep -v 'raw.githubusercontent.com' | cut -d'@' -f2 | sed 's/:/ /' | xargs -n2 nc -zvw5 2>&1 | grep -E "open|succeed"
```

### Seed

**IRISnet**

- 94d9a37f02c9157841e57a407800b8629c88de83@seed-1.mainnet.irisnet.org:26656

### Fullnodes

**IRISnet**

- c06fcd09af264a7aa73a88d149acf64abfd7b28d@sentry-0.mainnet.irisnet.org:26656
- 73c35b939102b6a4ea0657e79d423953073109fe@sentry-1.mainnet.irisnet.org:26656

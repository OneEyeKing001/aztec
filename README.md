# Aztec Network Sequencer Node
A step by step guide on How to Run `Sequencer Node` on Aztec Network Testnet & Earn `Apprentice` & `Guardian`Role.

## Roles Info
Sequecner Nodes will receive a certain role for their contribution on Discord.

After running and syncing your Sequencer node, You can go through [Get Role](https://github.com/OneEyeKing001/aztec?tab=readme-ov-file#get-apprentice-discord-role) step.

---

## 1. Install Dependecies
* Update packages:
```bash
sudo apt-get update && sudo apt-get upgrade -y
```

* Install Packages:
```bash
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y
```

* Install Docker:
```bash
sudo apt update
sudo apt install ca-certificates curl gnupg lsb-release
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null


sudo apt update
apt install docker.io
sudo apt install docker-compose-plugin

# Test Docker
sudo docker run hello-world
sudo systemctl enable docker
sudo systemctl restart docker
```

---

## 2. Install Aztec Tools
```bash
bash -i <(curl -s https://install.aztec.network)
```
```bash
echo 'export PATH="$HOME/.aztec/bin:$PATH"' >> ~/.bashrc

source ~/.bashrc
```

* Check if you installed successfully:
```bash
aztec
```

---

## 3. Update Aztec
```bash
aztec-up  0.87.2
```

---

## 4. Obtain RPC URLs
* You can get Sepolia `Geth` and `Beacon` RCP from TF Team at discounted price. (Limited Slots only!)

## 5. Generate Ethereum Keys
Get an EVM Wallet with `Private Key` and `Public Address` saved.

---

## 6. Get Sepolia ETH
Fund your Ethereum Wallet with [`ETH Sepolia`](https://cloud.google.com/application/web3/faucet/ethereum/sepolia).

---

## 7. Find IP
```bash
curl ifconfig.me
```
* Save it

---

## 8. Enable Firewall & Open Ports
```console
# Firewall
ufw allow 22
ufw allow ssh
ufw enable

# Sequencer
ufw allow 40400
ufw allow 8080
```

---
## 9. Run Sequencer Node
* Open TMUX
```bash
tmux new -s aztec
```

* Run Node
```
aztec start --node --archiver --sequencer \
  --network alpha-testnet \
  --l1-rpc-urls RPC_URL  \
  --l1-consensus-host-urls BEACON_URL \
  --sequencer.validatorPrivateKey 0xYourPrivateKey \
  --sequencer.coinbase 0xYourAddress \
  --p2p.p2pIp IP
  --p2p.maxTxPoolSize 1000000000
```
Replace the following variables before you Run Node:
* `RPC_URL` & `BEACON_URL`: Step 4
* `0xYourPrivateKey`: Your EVM wallet private key starting with `0x...`
* `0xYourAddress`: Your EVM wallet public address starting with `0x...`
* `IP`: Your server IP (Step 7)

### Optional Commands:
**Tmux Commands:**
* Minimze Tmux: `Ctrl` + `B` + `D`
* Return to Tmux: `tmux attach -t aztec`
* Kill Tmux (when inside): `Ctrl`+`B` + `X`
* Kill Tmux (when outside): `tmux kill-pane -t aztec`

---

## 10. Check Sync Status
After entering the command, your node starts running, It takes a few minutes for your node to get synced.

* Check the latest synced block number of your sequencer:
```
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' \
http://localhost:8080 | jq -r ".result.proven.number"
```
* Check the latest block number of Aztec network: https://aztecscan.xyz/

---

## 11. Verify Node's Peer ID:
**Find your Node's Peer ID:**
```bash
sudo docker logs $(docker ps -q --filter ancestor=aztecprotocol/aztec:latest | head -n 1) 2>&1 | grep -i "peerId" | grep -o '"peerId":"[^"]*"' | cut -d'"' -f4 | head -n 1

```
* This reveals your Node's Peer ID, Now search it on [Nethermind Explorer](https://aztec.nethermind.io/)
* Note: It might takes some hours for your node to show up in Nethermind Explorer after it fully synced.

---

## 12. Save P2P Private Key :
**Find your Node's P2P private key:**
```bash
nano .aztec/alpha-testnet/data/p2p-private-key
```
* This reveals your Node's P2P Private key, Save it for future use.
* You can use it to re-run your node on another vps with same Peer ID.
---



## 🔃 Update Sequencer Node
* 1- Stop Node:
```console
Ctrl + c
```

* 2- Update Node:
```bash
aztec-up alpha-testnet
```


* 3- Re-run Node

Return to [Step 9: Run Sequencer Node](https://github.com/OneEyeKing001/aztec?tab=readme-ov-file#9-run-sequencer-node) to re-run your node

---


## Get Apprentice Discord Role:
Go to the discord channel :[operators| start-here](https://discord.com/channels/1144692727120937080/1367196595866828982/1367323893324582954) and type `/operator start` then follow the prompts.

**Step 1: Get the latest proven block number:**
```bash
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' \
http://localhost:8080 | jq -r ".result.proven.number"
```
* Save this block number for the next steps
* Example output: 20905

**Step 2: Generate your sync proof**
```bash
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getArchiveSiblingPath","params":["BLOCK_NUMBER","BLOCK_NUMBER"],"id":67}' \
http://localhost:8080 | jq -r ".result"
```
* Replace 2x `BLOCK_NUMBER` with your number

**Step 3: Register with Discord**
* Type the following command in this Discord server: `/operator start`
* After typing the command, Discord will display option fields that look like this:
* `address`:            Your validator address (Ethereum Address)
* `block-number`:      Block number for verification (Block number from [Step 1](https://github.com/OneEyeKing001/aztec?tab=readme-ov-file#1-install-dependecies))
* `proof`:             Your sync proof (base64 string from [Step 2](https://github.com/OneEyeKing001/aztec?tab=readme-ov-file#2-install-aztec-tools))

Then you'll get your `Apprentice` Role.


## Get Guardian Discord Role:
Guardian Role snapshot takes place every 5-7 days, start you node today and wait for next snapshot.

**After Snapshot**

Go to the discord channel :[operators| start-here](https://discord.com/channels/1144692727120937080/1367196595866828982/1367323893324582954) and type `/check ip` then Enter your `IP` and `node_eth_address` 

Then you'll get your `Guardian` Role

* `IP`:                      Vps Ip you get in [Step 7](https://github.com/OneEyeKing001/aztec?tab=readme-ov-file#7-find-ip)

* `node_eth_address `:       ETH Public address you generated in [Step 5](https://github.com/OneEyeKing001/aztec?tab=readme-ov-file#5-generate-ethereum-keys)

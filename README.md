# ✅ Tahap 1 — Persiapan Awal (System Requirements & Dependencies)
1. Update sistem dan install dependencies:
```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt install curl ufw iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
# ✅ Tahap 2 — Install Docker
```
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Tes docker
sudo docker run hello-world
```
# ✅ Tahap 3 — Install CLI yang Dibutuhkan
1. Install Drosera CLI
```
curl -L https://app.drosera.io/install | bash
source ~/.bashrc
droseraup
```
2. Install Foundry CLI
```
curl -L https://foundry.paradigm.xyz | bash
source ~/.bashrc
foundryup
```
3. Install Bun
```
curl -fsSL https://bun.sh/install | bash
source ~/.bashrc
```
# ✅ Tahap 4 — Deploy Trap
1. Clone & setup project
"email_kamu@example.com" ganti emailmu

"username_kamu" ganti username github
```
mkdir my-drosera-trap
cd my-drosera-trap
git config --global user.email "email_kamu@example.com"
git config --global user.name "username_kamu"
forge init -t drosera-network/trap-foundry-template
```
2. Compile Trap
```
bun install
forge build
```
Abaikan warning saat compile.

3. Deploy Trap ke testnet
Ganti PRIVATE_KEY_MU dengan private key wallet yang berisi Holesky ETH
```
DROSERA_PRIVATE_KEY=PRIVATE_KEY_MU drosera apply
```
Ganti PRIVATE_KEY_MU dengan private key wallet yang berisi Holesky ETH

# ✅ Tahap 5 — Bloom Boost & Tes Trap
1. Buka: https://app.drosera.io/
2. Login → Wallet → Klik Traps Owned
3. Pilih Trap kamu → Klik "Send Bloom Boost" dan deposit sedikit Holesky ETH
4. Jalankan dryrun test:
```
drosera dryrun
```
# ✅ Tahap 6 — Jalankan sebagai Operator Node
1. Edit whitelist operator:
```
nano drosera.toml
```
Tambahkan di bawah:
pastikan :
private_trap = true
whitelist = ["0xALAMAT_OPERATOR"] isi alamat addresmu (bukan privatekey)
2. Update config trap:
```
DROSERA_PRIVATE_KEY=PRIVATE_KEY_MU drosera apply
```
# ✅ Tahap 7 — Install & Register Operator
1. Download & install CLI Operator
```
cd ~
curl -LO https://github.com/drosera-network/releases/releases/download/v1.16.2/drosera-operator-v1.16.2-x86_64-unknown-linux-gnu.tar.gz
tar -xvf drosera-operator-v1.16.2-x86_64-unknown-linux-gnu.tar.gz
sudo cp drosera-operator /usr/bin
drosera-operator --version
```
2. Register Operator
```
drosera-operator register --eth-rpc-url https://ethereum-holesky-rpc.publicnode.com --eth-private-key PRIVATE_KEY_MU
```
# ✅ Tahap 8 — Jalankan sebagai systemd service
Ganti:
PV_KEY = Private key wallet

VPS_IP = IP VPS kamu (atau localhost jika lokal)
```
sudo tee /etc/systemd/system/drosera.service > /dev/null <<EOF
[Unit]
Description=drosera node service
After=network-online.target

[Service]
User=$USER
Restart=always
RestartSec=15
LimitNOFILE=65535
ExecStart=$(which drosera-operator) node --db-file-path $HOME/.drosera.db --network-p2p-port 31313 --server-port 31314 \
    --eth-rpc-url https://ethereum-holesky-rpc.publicnode.com \
    --eth-backup-rpc-url https://1rpc.io/holesky \
    --drosera-address 0xea08f7d533C2b9A62F40D5326214f39a8E3A32F8 \
    --eth-private-key PV_KEY \
    --listen-address 0.0.0.0 \
    --network-external-p2p-address VPS_IP \
    --disable-dnr-confirmation true

[Install]
WantedBy=multi-user.target
EOF
```
# ✅ Tahap 9 — Open Port + Start Service
Buka port firewall:
```
sudo ufw allow ssh
sudo ufw allow 22
sudo ufw allow 31313/tcp
sudo ufw allow 31314/tcp
sudo ufw enable
```
Jalankan service:
```
sudo systemctl daemon-reload
sudo systemctl enable drosera
sudo systemctl start drosera
```
# ✅ Tahap 10 — Monitor & Opt-in Trap
Lihat log:
```
journalctl -u drosera.service -f
```
Abaikan warning Failed to gossip message: InsufficientPeers

masuk ke web:
Opt-in ke Trap:
Buka https://app.drosera.io

Pilih Trap kamu

Klik "Opt-in" untuk menghubungkan Operator ke Trap

done 

# Join Channel Airdrop Sambil Rebahan : https://t.me/kingfeeder

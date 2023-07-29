# bonus-block
<h1 align="center"> Gereksinimler </h1>

* Tavsiye edilen:
```
4 CPU  8 RAM  400 SSD
```
<h1 align="center"> Güncellemeler ve gerekli paketler </h1>


```sh
# Sistemi güncelliyoruz
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y

# go'yu yüklüyoruz
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME

curl https://dl.google.com/go/go1.20.1.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF

source $HOME/.profile
go version
```
<h1 align="center"> Node'u yüklüyoruz </h1>

```sh
cd $HOME
rm -rf BonusBlock-chain/
git clone https://github.com/BBlockLabs/BonusBlock-chain

cd BonusBlock-chain/
git checkout v0.1.39

make install

bonus-blockd version
```

<h1 align="center"> İnitalizasyon işlemleri </h1>

```sh
# monikerName'i kendi isminizle değişin değiştirin.
bonus-blockd init monikerName --chain-id=blocktopia-01

# Genesis
curl -Ls https://ss-t.bonusblock.nodestake.top/genesis.json > $HOME/.bonusblock/config/genesis.json 

# Addrbook
curl -Ls https://ss-t.bonusblock.nodestake.top/addrbook.json > $HOME/.bonusblock/config/addrbook.json
```

<h1 align="center"> Servis dosyası oluşturma </h1>

```
sudo tee /etc/systemd/system/bonus-blockd.service > /dev/null << EOF
[Unit]
Description=Bonusblock Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which bonus-blockd) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable bonus-blockd
```

<h1 align="center"> Snapshot </h1>

```
SNAP_NAME=$(curl -s https://ss-t.bonusblock.nodestake.top/ | egrep -o ">20.*\.tar.lz4" | tr -d ">")
curl -o - -L https://ss-t.bonusblock.nodestake.top/${SNAP_NAME}  | lz4 -c -d - | tar -x -C $HOME/.bonusblock

sudo systemctl restart bonus-blockd
journalctl -u bonus-blockd -f
```

> node sync olduktan sonra:

```
# kendi cüzdan isminizi oluşturun
bonus-blockd keys add cuzdanismi
```

> [Buradan](https://faucet.bonusblock.io/). test tokeni alın.

> Sync olunca da validatörünüzü oluşturun.:

```
bonus-blockd tx staking create-validator \
--amount 900000ubonus \
--pubkey $(bonus-blockd tendermint show-validator) \
--moniker "monikeriniz" \
--identity "yourKeybaseId" \
--details "yourDetails" \
--website "yourWebsite" \
--chain-id blocktopia-01 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from cuzdanismi \
--gas-adjustment 1.4 \
--gas auto \
-y
```

> [Explorer](https://explorer.nodestake.top/bonusblock-testnet/staking/bonusvaloper1sf2v5sk5eqks8jsypeznryxgtucfxr32muv78z)

> [Twitter](https://twitter.com/bonus_block)

> [Telegram](https://t.me/bonusblock)

> [Docs](https://docs.bonusblock.io)

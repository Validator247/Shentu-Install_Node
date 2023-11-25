# Celestia-Install_Node

Update system and install build tools

    sudo apt update

    sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y

Install Go

    rm -rf $HOME/go
    sudo rm -rf /usr/local/go
    cd $HOME
    curl https://dl.google.com/go/go1.21.1.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
    cat <<'EOF' >>$HOME/.profile
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/go
    export GO111MODULE=on
    export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
    EOF
    source $HOME/.profile
    go version

Install Node

    cd $HOME
    rm -rf celestia-app
    git clone https://github.com/celestiaorg/celestia-app.git 
    cd celestia-app/ 
    git checkout v1.3.0
    make install
    celestia-appd version

Initialize Node
Replace NodeName with your own moniker.

    celestia-appd init NodeName --chain-id=mocha-4

Download Genesis

    curl -Ls https://ss-t.celestia.nodestake.top/genesis.json > $HOME/.celestia-app/config/genesis.json 

Download addrbook

    curl -Ls https://ss-t.celestia.nodestake.top/addrbook.json > $HOME/.celestia-app/config/addrbook.json

Create Service

    sudo tee /etc/systemd/system/celestia-appd.service > /dev/null <<EOF
    [Unit]
    Description=celestia-appd Daemon
    After=network-online.target
    [Service]
    User=$USER
    ExecStart=$(which celestia-appd) start
    Restart=always
    RestartSec=3
    LimitNOFILE=65535
    [Install]
    WantedBy=multi-user.target
    EOF
    sudo systemctl daemon-reload
    sudo systemctl enable celestia-appd

Download Snapshot(optional)

    SNAP_NAME=$(curl -s https://ss-t.celestia.nodestake.top/ | egrep -o ">20.*\.tar.lz4" | tr -d ">")
    curl -o - -L https://ss-t.celestia.nodestake.top/${SNAP_NAME}  | lz4 -c -d - | tar -x -C $HOME/.celestia-app

Launch Node

    sudo systemctl restart celestia-appd
    journalctl -u celestia-appd -f

# Add New Key

    celestia-appd keys add wallet

Query Wallet Balance

    celestia-appd q bank balances $(celestia-appd keys show wallet -a)

# Create New Validator

    celestia-appd tx staking create-validator \
    --amount=1000000utia \
    --pubkey=$(celestia-appd tendermint show-validator) \
    --chain-id=mocha-4 \
    --commission-rate="0.05" \
    --commission-max-rate="0.20" \
    --commission-max-change-rate="0.20" \
    --min-self-delegation=1 \
    --from mykey \
    --yes \
    --gas-adjustment=1.2 \
    --gas=auto \
    --gas-prices=0.005utia \
    --evm-address=$EVM_ADDRESS \
    --moniker="YOUR_MONIKER_NAME" \
    --website="YOUR_WEBSITE" \
    --security-contact="YOUR_EMAIL" \
    --details="YOUR_DETAIL"

      

      

        

# Installation for Service version

## ${\color{orange}Create \space User}$	(optional)
```css
adduser tenderduty
adduser tenderduty sudo
su - tenderduty
```
## ${\color{orange}Install \space GO}$	
My Version : go version go1.20.6 linux/amd64
```css
sudo rm -rf /usr/local/go && \
curl -Ls https://go.dev/dl/go1.20.6.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

## ${\color{orange}Install \space Binary}$	
```css
git clone https://github.com/blockpane/tenderduty
cd tenderduty
go install
```
## ${\color{orange}Configurations}$	
copy config.yml
```css
cp example-config.yml config.yml
```
or create new one
```css
touch config.yml
```
### ${\color{aqua}Edit \space Configurations}$	
Here my configurations
```css
enable_dashboard: yes
listen_port: 8888
hide_logs: no
node_down_alert_minutes: 3
node_down_alert_severity: critical

prometheus_enabled: no
prometheus_listen_port: 28686

# Telegram settings
telegram:
  # Alert via telegram? Note: also supersedes chain-specific settings
  enabled: yes
  # API key ... talk to @BotFather
  api_key: "bot token"
  # The group ID for the chat where messages will be sent. Google how to find this, will include better info later.
  channel: "channel id"
```

### ${\color{aqua}Create \space Chains \space Configurations}$	
Create `chains.id` directory
```css
mkdir chains.d
```
Create Your chain `yml` files to be monitored. Here is example i'm creating `planq.yml`, edit it according to your configurations.
```css
tee ./chains.d/planq.yml > /dev/null << EOF
    chain_id: planq_7070-2                                                      # Chain ID
    valoper_address: plqvaloper1kk4j6t8gsd6t3x2l8pgpa5hee2nw3xrlfqah            # Your Valoper Address
    public_fallback: no

    alerts:
      stalled_enabled: yes
      stalled_minutes: 10
      consecutive_enabled: yes
      consecutive_missed: 5
      consecutive_priority: critical
      percentage_enabled: no
      percentage_missed: 10
      percentage_priority: warning
      alert_if_inactive: yes
      alert_if_no_servers: yes

      # Telegram settings
      telegram:
        enabled: yes                                              
        api_key: ""              # BOT TOKEN, Leave it if you wont configure it
        channel: ""              # Channel ID

    nodes:
      - url: https://rpc.planq.network:443
        alert_if_down: yes
      - url: https://rpc.planq.nodestake.top:443
        alert_if_down: no

EOF
```
You can create another chains in the same directory, just rename `planq.yml` with another chain name

## ${\color{orange}Create \space Service \space Configurations}$	
```css
sudo tee /etc/systemd/system/tenderduty.service > /dev/null << EOF
[Unit]
Description=Composable-testnet Testnet
After=network-online.target
[Service]
User=$USER
ExecStart=$(which tenderduty) run start
WorkingDirectory=$HOME/tenderduty
Restart=on-failure
RestartSec=10
LimitNOFILE=infinity
Type=simple
TimeoutSec=180
[Install]
WantedBy=multi-user.target
EOF
```
```css
sudo systemctl enable tenderduty
sudo systemctl daemon-reload
sudo systemctl restart tenderduty && sudo journalctl -fu tenderduty -o cat
```
Open on your browser
http://IP-Address:8888

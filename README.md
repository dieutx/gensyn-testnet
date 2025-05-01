<h2 align=center>Gensyn Testnet Node Guide</h2>

## 💻 System Requirements

| Requirement                        | Details                                                                                      |
|-------------------------------------|---------------------------------------------------------------------------------------------|
| **CPU Architecture**                | `arm64` or `amd64`                                                                          |
| **Recommended RAM**                 | 25 GB                                                                                       |
| **CUDA Devices (Recommended)**      | `RTX 3090`, `RTX 4090`, `A100`, `H100`                                                      |
| **Python Version**                  | Python >= 3.10 (For Mac, you may need to upgrade) 


## 🌐 Rent GPU
- Visit : [Quick Pod Website](https://console.quickpod.io?affiliate=64e0d2b2-59ee-4989-a05f-f4c3b6dbb2e4)
- Sign Up using email address
- Go to your email and verify your Quick Pod account
- Click on `Add` button in the corner to deposit fund
- You can deposit using crypto currency (from metamask) or using Credit card
- Now go to `template` section and then select `Ubuntu 22.04 jammy` in the below
- Now click on `Select GPU` and search `RTX 4090` and choose it
- Now choose a GPU and click on `Create POD` button
- Your GPU server will be deployed soon
- Now click on `Connect` option and then choose `Connect to web terminal`

## 📥 Installation

1. **Install `sudo`**
```bash
apt update && apt install -y sudo
```
2. **Install other dependencies**
```bash
sudo apt update && sudo apt install -y python3 python3-venv python3-pip curl wget screen git lsof && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add - && echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list && sudo apt update && sudo apt install -y yarn
```
3. **Install Node.js and npm if not installed already**  
```bash
curl -sSL https://raw.githubusercontent.com/zunxbt/installation/main/node.sh | bash
```
4. **Clone this repository**
```bash
cd $HOME && [ -d rl-swarm ] && rm -rf rl-swarm; git clone https://github.com/zunxbt/rl-swarm.git && cd rl-swarm
```
5. **Create a `screen` session**
```bash
screen -S gensyn
```
6. **Run the swarm**
```bash
python3 -m venv .venv && . .venv/bin/activate && ./run_rl_swarm.sh
```
- It will ask some questions, you should send response properly
- ```Would you like to connect to the Testnet? [Y/n]``` : Write `Y`
- ```Would you like to push models you train in the RL swarm to the Hugging Face Hub? [y/N]``` : Write `N`
- When you will see interface like this, you can detach from this screen session

![Screenshot 2025-04-01 061641](https://github.com/user-attachments/assets/b5ed9645-16a2-4911-8a73-97e21fdde274)

7. **Detach from `screen session`**
- Use `Ctrl + A` and then press `D` to detach from this screen session.

## How to run multiple swarm nodes if you have multiple GPUs

Example you have 2 GPUs

1. Stop all your currrent nodes
2. Create a screen/tmux session for the 1st node
3. Start your 1st node again by use first GPU (from step 1 to step 4 above) then run the command:

`CUDA_VISIBLE_DEVICES=0 ./run_rl_swarm.sh`

Detach your tmux/screen session and create another session for the 2nd node:
1. Clone repo again to different folder: 

`git clone https://github.com/zunxbt/rl-swarm.git rl-swarm-2 && cd rl-swarm-2`

2. Copy to new script file to run: `cp run_rl_swarm.sh run_rl_swarm_2.sh`
3. Run command below "

```
export MODAL_PORT=3001
export SWARM_PORT=38332

sed -i "s|MODAL_PROXY_URL = \"http://localhost:3000/api/\"|MODAL_PROXY_URL = \"http://localhost:${MODAL_PORT}/api/\"|g" hivemind_exp/chain_utils.py

sed -i "s|/tcp/38331|/tcp/${SWARM_PORT}|g" run_rl_swarm_2.sh
sed -i "s|npm run dev > server.log 2>&1 &|npm run dev -- -p ${MODAL_PORT} > server.log 2>&1 &|g" run_rl_swarm_2.sh
sed -i "/PORT_LINE=\$(ss -ltnp | grep/s/\([\":]\)3000\([ \"']\)/\1$MODAL_PORT\2/" run_rl_swarm_2.sh
```
4. Start your 2nd node: 

```
python3 -m venv .venv
source .venv/bin/activate
CUDA_VISIBLE_DEVICES=1 ./run_rl_swarm_2.sh
```

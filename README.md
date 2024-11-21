# peaq

### Guide to Setting Up a Peaq Node for Becoming a Validator

---

#### **1. Prerequisites**

Before you start, ensure you meet the following requirements:

- **Hardware Requirements:**
  - CPU: 4+ cores
  - RAM: 16 GB
  - Disk Space: 200 GB SSD
  - Network: High-speed and reliable internet connection
- **Operating System:** Ubuntu 20.04 or similar Linux distribution.
- **Tools:**
  - `curl`
  - `docker` and `docker-compose`
  - `git`
  - Basic knowledge of terminal commands.

---

#### **2. Install Necessary Dependencies**

Update your package list and install dependencies:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl git build-essential
```

Install Docker:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker $USER
```

Log out and back in to apply Docker permissions.

Install Docker Compose:

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

---

#### **3. Clone the Peaq Node Repository**

Download the Peaq node repository:

```bash
git clone https://github.com/peaqnetwork/peaq-node.git
cd peaq-node
```

---

#### **4. Build the Node**

Build the Peaq node using Docker:

```bash
docker build -t peaq-node .
```

Alternatively, for manual builds, ensure you have Rust installed and use:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
rustup update
rustup target add wasm32-unknown-unknown --toolchain nightly
cargo build --release
```

---

#### **5. Configure and Run the Node**

##### **5.1. Configure Docker Compose**

Create a `docker-compose.yml` file in the project directory with the following content:

```yaml
version: "3.8"
services:
  peaq-node:
    image: peaq-node
    container_name: peaq-validator
    restart: always
    ports:
      - "30333:30333"
      - "9933:9933"
      - "9944:9944"
    volumes:
      - ./data:/data
    command: >
      peaq-node
      --base-path /data
      --chain peaq
      --validator
      --name "YourValidatorNodeName"
```

##### **5.2. Start the Node**

Run the Peaq node using Docker Compose:

```bash
docker-compose up -d
```

Check the logs to ensure the node is running correctly:

```bash
docker-compose logs -f
```

---

#### **6. Syncing the Node**

The node will take time to sync with the network. Use the logs to monitor progress:

```bash
docker-compose logs -f | grep -i sync
```

---

#### **7. Set Up Validator Account**

1. Generate a validator account using the Polkadot JS wallet or Peaq wallet.
2. Ensure your account is funded with the required amount of Peaq tokens to register as a validator.
3. Bond the tokens and nominate yourself as a validator.

---

#### **8. Monitor Your Node**

To ensure your validator node performs optimally, monitor it using:

- **Prometheus** and **Grafana:** Export metrics from the node.
- **Telemetry:** Register your node with Peaq Telemetry.

---

#### **9. Troubleshooting**

- **Check Container Status:**
  ```bash
  docker ps
  ```
- **Restart the Node:**
  ```bash
  docker-compose restart
  ```
- **View Detailed Logs:**
  ```bash
  docker-compose logs peaq-node
  ```

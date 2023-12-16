# NORun a Taiko node
This guide will help you start up a Taiko RPC node using simple-taiko-node.

Prerequisites
Docker is installed and running.
Git is installed.
If using Windows, you should install Git BASH or WSL to use as your terminal.
Meet the Geth minimum hardware requirements except for the storage requirement because Taiko nodes will require less storage. As of 2023-09-18 a node uses less than 10 GB. 100 GB should be future proof enough if you intend to run your node for a while.
Steps
Clone simple-taiko-node
git clone https://github.com/taikoxyz/simple-taiko-node.git
cd simple-taiko-node

Copy the sample .env files
cp .env.sample .env

Set the required values in the .env file
First, open the .env in your preferred text editor:

nano .env

Next, you will set the L1 archive node endpoints.

You can use any Sepolia L1 endpoint, but it must point to an archive node to access the state trie beyond the last 128 blocks.

It's recommended to follow Local below, otherwise you may need to use a paid account for a Sepolia RPC provider. Alchemy and Infura will work temporarily, but will eventually rate limit the requests for a free account, and your node will stop syncing.

If you run into rate limit issues, many community members have had success by changing the HTTPS endpoint to https://rpc.sepolia.org.

If you are running a local Sepolia node, you cannot reference the L1 endpoints as http://127.0.0.1:8545 and ws://127.0.0.1:8546 because that is local to inside the simple-taiko-node Docker image. Depending on your firewall setup, you can do a few things. You can try:

Using host.docker.internal (see: stack overflow)
Using the private ip address of your machine (use something like ip addr show to get this ip address)
Finally, set the following L1 node endpoints in your .env file:

L1_ENDPOINT_HTTP
L1_ENDPOINT_WS
Take a look at the comments above these values in the .env.sample to see how to set them up.

Start a node
Make sure Docker is running and then run the following command to start the node:

docker compose up -d

Note: You may need to use sudo docker compose up -d if you are not in the docker group.

If you ran a testnet node previously make sure to first run docker compose down -v to remove the old volumes.

Verify node is running
Option 1: Check with the node dashboard
A node dashboard will be running on localhost on the GRAFANA_PORT you set in your .env file, which defaults to 3001: http://localhost:3001/d/L2ExecutionEngine/l2-execution-engine-overview.


taiko node dashboard
You can verify that your node is syncing by checking that the chain head on the dashboard (see above) is increasing. Once the chain head matches what's on the block explorer, you are fully synced.

Option 2: Check with curl commands
Check if the Execution Layer client is connected to Taiko L2:
curl http://localhost:8547 \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"method":"eth_chainId","params":[],"id":1,"jsonrpc":"2.0"}'

which should return the chainId as 0x28c5f (167007):

{ "jsonrpc": "2.0", "id": 1, "result": "0x28c5f" }

Check if the Execution Layer client is synced by requesting the latest Taiko L2 / L3 block from the Execution Layer client:
curl http://localhost:8547 \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"method":"eth_blockNumber","params":[],"id":1,"jsonrpc":"2.0"}'

If the blockNumber response value is 0 or not growing, check the Taiko L2 logs here:
docker compose logs -f

Note: You may need to use sudo docker compose logs -f if you are not in the docker group.

Operate the node
You can find all node operations (eg. stop node, update node, remove node, view logs) in the Node runner manual.

Videos
Run a Taiko L2 node (YouTube) (deprecated, but still useful)
Troubleshooting
View the Node troubleshooting reference for help on any common error codes / issues.
View the logs of the node (see all log commands in the Node runner manual).DE-MAXI
Tutorial for running a node

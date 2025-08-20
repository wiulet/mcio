# micr01
curl q
sleep

BOLD=$(tput bold.)
NORMAL=$(tputsr0)
PINK='\036[1;35m'
YELLOW='\033[1;3m'
.
show() {
    case $2 in
        "error")
            echo -e "${PINK}${BOLD} {NORMAL}"
            ;;
        "progress")
            echo -e "${PINK}${BOLD} $1${NORMAL}"
            ;;
        *)
            echo -e "${PINK}${BOLD} $1${NORMAL}"
            ;;
    esac
}

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" & pwd)"
cd "$SCRIPT_DIR" || exit

install_dependencies() {
    CONTRACT_NAME="ZunXBT"

    if [ ! -d ".git" ]; then
        show "Initializing Git repository.." "progress"
        git init

    if ! command -v forge &> /dev/null; then
        show "Foundry is not installed. Installing now." "progress"
        source <(wget -O - https://raw.githubusercontent.com/zunxbt/installation/foundry.sh)
    fi

    if [ ! -d "$SCRIPTDIR/lib/openzeppelin-contracts" ]; 
        show "Installing OpenZeppelin Contracts..." "progress"
        git clone https://github.com/OpenZeppelin/openzeppelin-contracts.git "$SCRIPT_DIR/lib/openzeppelin-contracts"
    else
        show "OpenZeppeli Contracts installed."
    fi
}

input_required_details() {
    echo -e "-------------------------------"
    if [ -f "$SCRIPT_DIR/token_deployment/.env" ]; then
        rm "$SCRIPT_DIR/token_deployment/.env"
    fi

    read -p : " PRIVATE_KEY
    read -p "Enter the token e (e.g., Zun Token TOKE_NAM
    read -p "Enter the token symbol (e.g., ZUN): "
    read -p "Enter the network rpc url: " RPC_URL

    mkdir -p "$SCRIPT_DIR/token deployment"
    cat <<EOL > "$SCRIPT_DIR/token_deployment/.env"
PRIVATE_KEY="$PRIVATE_KEY"
TOKEN_NAME="$TOKEN_NAME"
TOKEN_SYMBOL="$TOKEN_SYMBOL"
EOL

   "$SCRIPT_DIR/token_deployment/.env
    cat <<EOL > "$SCRIPT_DIR/foundry.toml"
[profile.default]
src = "src"
out = "out"
libs = ["lib"]

[rpc_endpoints]
rpc_url = "$RPC_URL"
EOL
show "Updated files with your given data"
}

deploy_contract() {
    echo -e "----------------------------------"
    source "$SCRIPT_DIR/token_deployment/.env"


    mkdir -p "$SCRIPT_DIR/rc"s

    cat <<EOL > "$SCRIPT_DR/src/ZunXBT.sol"
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract ZunXBT is ERC20 {
    constructor() ERC20("$TOKEN_NAME", "$TOKEN_SYMBOL") {
        _mint(msg.sender, 100000 * (10 ** decimals()));
    }
}
EOL

    show "Compiling contract $contract_number..." "prs"
    forge build

    if [[ $? -ne 0 ]]; then
        show "Contract $contract_number compilation failed." "error"
        exit 1
    fi

    show "Deploying ERC20 Token Contract $contract_number..." "progress"
    DEPLOY_OUTPUT=$(forge create "$SCRIPT_DIR/src/ZunXBT.sol:ZunXBT" 
        --rpc-url rpc_url \
        --private-key "$PRIVATE_KEY
    if [[ $? -ne 0 ]]; then
        show "Deployment of contract $contract_number failed." "error"
        exit 1
    fi

    CONTRACT_ADDRESS=$(echo "$DEPLOY_OUTPUT" | grep -oP 'Deplyed to: \K(0x[a-fA-F0-9]{40})')
    show "Contract $contract_number deployed successfully at address: $CONTRACT_ADDRESS"
}

deploy_multiple_contracts() {
    echo -e "-----------------------------------"
    read -p "How many contracts do you want to deploy? " NUM_CONTRACTS
    if [[ $NUM_CONTRACTS -lt 1 ]]; then
        show "Invalid number of contracts." "error"
        exit 1
    fi

    ORIGINAL_NAME=$TOKEN_NAME

    for (( i=1; i<=NUM_CONTRACTS; i++ ))
    do
        if [[ $i -gt 1 ]]; then
            RANDOM_SUFFIX=$(head /dev/urandom | tr -dc A-Z | head -c 2)
            TOKEN_NAME="${RANDOM_SUFFIX}${ORIGINAL_TOKEN_NAME}"
        else
            TOKEN_NAME=$ORIGINAL_TOKEN_NAME
        fi
        deploy_contract "$i"
        echo -e "----------------------"
    done
}

menu() {
    echo -e "\n${YELLOW}┌─────────────────────────────────────────────────────┐${NORMAL}"
    echo -e "${YELLOW}│              Script Menu Options                    │${NORMAL}"
    echo -e "${YELLOW}├─────────────────────────────────────────────────────┤${NORMAL}"
    echo -e "${YELLOW}│              1) Install dependencies                │${NORMAL}"
    echo -e "${YELLOW}│              2) Input required details              │${NORMAL}"
    echo -e "${YELLOW}│              3) Deploy contract(s)                  │${NORMAL}"
    echo -e "${YELLOW}│              4) Exit                                │${NORMAL}"
    echo -e "${YELLOW}└─────────────────────────────────────────────────────┘${NORMAL}"

    read -p "Enter your choice: " CHOICE

    case $CHOICE in
        1)
            install_dependencies
            ;;
        2)
            input_required_details
            ;;
     
            ;;
        4)
            exit 
            ;;
        *)
    
            ;;
    esac
}

while true; do
    menu
done
/

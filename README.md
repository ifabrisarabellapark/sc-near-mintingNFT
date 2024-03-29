<p align="center">
  <a href="https://near.org/">
    <img alt="Near" src="https://github.com/irene-bbox/sc-near-mintingNFT/blob/main/images/near_oceanblue.png" 
    width="900" />
  </a>
</p>

# Minting an NFT on NEAR Protocol :ringed_planet: :roller_skate: :kick_scooter:

In this guide, you'll learn how to mint an NFT on NEAR Protocol blockchain (testnet) via a Rust Smart Contract. This guide walks you through all steps to quickly deploy the contract, yet the guide doesn't explain the actual logic of the NFT-minting contract :nerd_face: :shipit: :bowtie:, if you want to learn how to write a smart contract or mint NFTs on near, then follow [this](https://docs.near.org/tutorials/nfts/introduction) tutorial in the official NEAR documentation. 

---


## Create testnet account

If you don't own a NEAR wallet yet, create one. Navigate to NEAR testnet [wallet](https://wallet.testnet.near.org) and click on 'Create Account'.


## Configure CLI

```bash
npm install -g near-cli                                             # Install the NEAR CLI
near                                                                # To see various possible commands run
near login                                                          # Log into your NEAR testnet wallet
near keys <youraccountname>.testnet                                 # Visualize your keys running
```


## Set up Rust

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh      # If you haven't installed Rust yet, install it now using rustup
rustup target add wasm32-unknown-unknown                            # Add Wasm toolchain
```

Some -but not all- Smart Contracts (e.g., SCRT Network, NEAR Protocol, etc.) compile to WebAssembly (Wasm) <br /> and that's why we add the toolchain for Rust :sheep:.


###### Version Management
Over time you'll need to manage rust versions. Notice that often your rustup toolchain manager and your rust compiler run on different versions and this is totally fine. Check the official [rust-lang](https://github.com/rust-lang/rust/blob/master/RELEASES.md) Git repo for new releases. Lastly, keep your toolchain and compilers up to date:
```bash
#check for current version
rustup --version
rustc --version
cargo --version

#update to the newest version
rustup update

#install a specific rust version
rustup install 1.63.0

#target a specific version of Rust
rustup override set 1.63.0
```

## Prep smart contract

This guide assumes you already have a ready-to-deploy smart contract. If you haven't, download from the official NEAR GitHub repo found [here](https://github.com/near-examples/nft-tutorial) a full-fledged smart contract to mint NFTs. Notice that this Repo has many branches. Each branch corresponds to the incremental spet in the smart contract development. Refer to the last branch if you want the most complete version of the contract. 

## Login Near wallet

To log into your Near wallet (testnet account), run in terminal the command below and follow the pop-up prompts
```bash
near login
```

## Deploy

Follow these 3 steps to deploy a Smart Contract on NEAR:
1. :gear: **build** the contract
2. :clapper: **deploy** the contract
3. :airplane: **initialize** the contract

Run in terminal,
```bash
export NFT_CONTRACT_ID=accountname.testnet                                  # Export path to your testnet account name
echo $NFT_CONTRACT_ID      
yarn build                                                                  # Build the contract

# now, ensure you are in the root folder and run
near deploy --wasmFile out/main.wasm $NFT_CONTRACT_ID                       # Deploy the contract
near call $NFT_CONTRACT_ID new_default_meta '{"owner_id": "'$NFT_CONTRACT_ID'"}' --accountId $NFT_CONTRACT_ID   # Initialize the contract
near call $NFT_CONTRACT_ID nft_mint '{"token_id": "nft1", "metadata": {"title": "May 4th", "description": "Star Wars pun", "media": "https://www.rd.com/wp-content/uploads/2020/04/GettyImages-1146900170.jpg"}, "receiver_id": "'$NFT_CONTRACT_ID'"}' --accountId $NFT_CONTRACT_ID --amount 0.1    # Mint the NFT
near view $NFT_CONTRACT_ID nft_metadata                                     # Call view functions
```
Congratulations :raised_hands: :tada: :partying_face: ! You've just minted an NFT on NEAR testnet. <br />
> * `accountname.testnet` is the name of your NEAR testnet account <br />
> * `NFT_CONTRACT_ID` is a path to a self-defined variable, i.e., your testnet account <br />
> * fields like `token_id`, `title`, `description`, etc. are customizable


## Redeploy (patch fixes)

Imagine you have an already-deployed smart contract. Assume you want to upgrade/change/add functionalities to such contract by altering its Rust code. The correct way to do so is by using *deployment patch fixes* (see official doc [here](https://docs.near.org/tutorials/nfts/upgrade-contract)), namely code patches to a contract that had been previously deployed. To upgrade a contract follow the next steps:

* change the current code to add the desired functionality
* run in terminal from the root directory 
   
   ```bash
   yarn build && near deploy --wasmFile out/main.wasm --accountId $NFT_CONTRACT_ID
   ```
* this outputs a warning and will ask if you'd like to proceed. Simply type `y` and hit enter
* once the contract is redeployed, test that the state migrated correctly by running a simple a *view* function of your choice, e.g., `near view $NFT_CONTRACT_ID <my_function_name>`

> :no_entry: :radioactive: :warning: Patch fixes on NEAR require you to run the *yarn build && near deploy* commands **simultaneously**. If you try to execute these commands *consecutively* the operation will fail because the initial contract had already been deployed and the NEAR Runtime doesn't understand what to do with this contradictory request to deploy an already-deployed contract.  

## Interact

You're ready to interact with the smart contract. Use view calls to return viewing data from the smart contract.
```bash
near view $NFT_CONTRACT_ID json_token '{"token_id": "nft1"}'                  # View call 
```

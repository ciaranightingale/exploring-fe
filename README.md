# Fe - Guestbook Example

This is an example contract written using Fe - a new smart contract language for the Ethereum blockchain.
Fe is a very young language so is susceptible to new, breaking changes in the future.
Currently, some features including constant variables and inheritance are not included so work arounds must be used.

## Installation

- To (install)[https://fe-lang.org/] the Fe compiler, download the file on the Fe homepage (currently only for Linux or Mac).
- Make a new directory called `.fe` and add a `bin` directory by running the command:

  `mkdir ~/.fe && mkdir $_/bin`

- Move the file executable file (fe_mac) to this new directory:

  `mv ~/Downloads/fe_mac/ ~/.fe/bin`

- Make the file executable by running

  `chmod +x ~/.fe/bin/fe_mac`

- Append the executable path to your path

  `echo 'export PATH="$PATH:/Users/<user>/.fe/bin"' >> ~/.zshrc && source ~/.zshrc`

- Next run

  `fe_mac -h`

to view the available subcommands

- Create an alias

  `echo ‘alias fe=“~/.fe/bin/fe_mac”’ >> ~/.zshrc && source ~/.zshrc`

- Check the alias works by running

  `fe -h`

- To create a new project, e.g. fe_new run

  `fe new <project_name>`

- Inside the src directory, delete the automatic files and inside a folder named according to you contract name e.g. guest_book, add a file called guest_book.fe
- The file structure should look like:

```
fe_project_name
│
└───src
    │
    └───guest_book
        │   guest_book.fe
```

- Inside `guest_book.fe` insert the following code:

```
use std::context::Context

    contract GuestBook {

        messages: Map<address, String<100>>

        pub fn sign(self, ctx: Context, book_msg: String<100>) {
            self.messages[ctx.msg_sender()] = book_msg
        }

        pub fn get_msg(self, addr: address) -> String<100> {
            return self.messages[addr].to_mem()
        }
    }
```

- `use std::context::Context` this allows us to access the context of a call in order to use e.g. `msg_sender()` or `msg_data()` within our contract
- Firstly we initialise a contract called `GuestBook` and initialise a mapping of address to string (of max length 100 - Fe does not support dynamic types) called `messages` to store the guestbook messages
- The function `sign` is a **public** function as indicated by the use of the keywork `pub` and allows users to write a message in the guestbook by adding their `message` to the mapping along with their wallet address.
- Note the use of the `Context` parameter to allow us to retrieve the sender of the message.
- The function `get_message` allows us to read guestbook entries for a given address. It is also a public function so that we can call the function externally.
- The keywork `self` is used to read data from the conract storage.
- **NOTE**: `self` & `context` parameters are not included in the ABI so we are free to use whilst still conforming to existing interfaces.
- When we try to return a reference type such as an array from the storage of the contract we have to explicitly copy it to memory using the `to_mem()` function.
- In Fe, every method that is defined without the pub keyword is private. Since we want people to interact with our contract and call the `sign method` we have to prefix it with `pub`.

## Building your Contract

- From within your directory, run the command

  `fe build <path_to_contract>`

- You will then be able to view an output directory with a folder inside named according to your contract name. Inside this you will be able to view the file `YourContractName_abi.json` which is a JSON representation that describes the binary interface.
- The file `YourContractName.bin` is the compiled binary contract code written in hexadecimal characters.
- If you need to recompile your code, you have to allow the compiler to overwrite the output directory so we use the `-—overwrite` flag.

  `fe build <path_to_contract> --overwrite`

## Deploying your Contract (to Goerli Testnet)

- To deploy your contract to Goerli testnet, we are going to use Dapptools. Install Dapptools by first installing (Nix)[https://nixos.org/download.html#nix-install-macos]
- Next install Dapptools by running

  `curl https://dapp.tools/install | sh`

- To be able to run the `seth` command from anywhere in the terminal, run

  `echo ‘export PATH="$PATH:/Users/ciaranightingale/.nix-profile/bin”’ >> ~/.zshrc`

- Test with

  `seth -help`

- To import your development wallet run

  `ethsign import`

and copy and paste in your private key to import your wallet (use a developer account).

- Next, use the Alchemy/Paradigm goerli faucet to obtain some GoEth

- Export environmnet variables:

  `export ETH_RPC_URL=https://eth-goerli-rpc.gateway.pokt.network/`

  `export ETH_FROM=<your_address>`

- Finally deploy your contract:

  `ETH_RPC_URL=${ETH_RPC_URL} ETH_FROM=${ETH_FROM} seth send --create src/output/GuestBook/GuestBook.bin -G 20000000`

## Interacting with your Contract

- Export your contract address as an environment variable

  `export CONTRACT_ADDRESS=<contract_address>`

- To add a message to the guestbook run:

  `ETH_RPC_URL=${ETH_RPC_URL} ETH_FROM=${ETH_FROM} seth send ${CONTRACT_ADDRESS} "sign(string)" ”I <3 Fe”`

- View the message associated with your wallet addres

  `ETH_RPC_URL=${ETH_RPC_URL} ETH_FROM=${ETH_FROM} seth call ${CONTRACT_ADDRESS} "get_msg(address)" ${ETH_FROM} | seth --to-ascii`

- Et voila! You have succesfully created an on-chain guestbook using Fe!

## Footnote

I encourage you to play around with Fe yourself. I have created a more complex example of a VRF lottery which you can find inside the `vrf_lottery.fe` file. The VRF file also explains what I discovered while creating the lottery and things to note or watch out for.

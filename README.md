## Blockchain and Ethereum training 101, how to get started?
This Github repository is a great place to get started with blockchain development. We will provide you with a great setup and give you useful pointers to start your blockchain development career with a :boom:.

The first part, part 0, is a short one - in this part 

Let's get started: happy coding! 

## Part 0: Setting up development tools on your Mac
Ok, so not everyone is using a mac for development, but for this guide we assume you are. :smile: If you are using a windows or linux machine, feel free to open an issue for this, or better yet, a pull request to add a section for these operating systems!

We are going to install the basics first, to get started as quick as possible. In following parts we will add new tooling when needed to improve the development workflow. :wrench:

### Installing Node
While it is possible to develop Ethereum smart contracts without using Node, or tooling based on Node, it does make everything *much* easier.
Node (or Node.js) is a runtime framework for Javascript, running locally and pretty much on every common desktop OS.

Download NodeJS from the nodejs.org webpage: https://nodejs.org/en/download/. I recommend picking the current release, rather than the stable version.

### Installing Git tooling
Next step is to install GIT. Chances are, you already have this installed if you are a developer or have developer tools installed, as it is used for pretty much everything. Installing this on a Mac is the easiest using the App Store and installing the Xcode developer tools. (Also nice for building macOS and iOS apps :wink:)

The xcode tooling can be found here: https://developer.apple.com/xcode/.

### Installing the truffle framework
Truffle is the de-facto standard for developing smart contracts. It is actively being maintained and updated by Consensys, one of the larger blockchain software companies, and the open source community.

Truffle can be installed by running ``sudo npm install -g truffle`` in the console. NPM is the package manager installed with Node by default, so it should be available on your system now.

### Installing Visual Studio Code (optional)
I like Visual Studio Code for my text editing, while it has the "Visual Studio" part in the name, it is a completely separate editor based on Electron, a framework written in Node. Visual Studio Code can be found here: https://code.visualstudio.com/.

A neat trick with Visual Studio Code is adding "code" to the path. This enables starting the editor from the terminal by typing "code ." in your current location! Instructions on how to set this up can be found here: https://code.visualstudio.com/docs/setup/mac.

This step is completely optional, but I do recommend an editor with at least syntax highlighting for Javascript and ideally also syntax highlighting (or a plug-in that enables this) for Solidity. Other options, aside from Visual Studio Code, are:
* Sublime (https://www.sublimetext.com/)
* Atom (https://atom.io/)
* Or even Emacs (https://www.gnu.org/software/emacs/)

I will be using Visual Studio Code throughout this walkthrough and will give some tips and tricks for this editor when we touch upon different topics.

### Installing a good terminal emulator (optional)
Coming from a Linux and Windows background, the default Mac terminal emulator felt a bit lacking, so I installed a better one: iTerm2, which can be found here: https://www.iterm2.com/

This step is also completely optional, but listed here to reproduce my results and screenshots if needed. :smile:

## Part 1: Creating your first Ethereum smart contract
So now you have all the tools required to start with your first smart contract! Smart contracts are the core part of any Ethereum-based blockchain solution, and are written (most often) in the Solidity language. 

When you are using the Visual Studio Code editor, I can recommend installing the solidity extension, created by Juan Blanco, which is (for me) the highest result when searching with the keyword ```Solidity``` in the extension tab.

Without further ado, let's get started with Truffle and smart contracts! Actually, we are going to cheat a little bit and let Truffle create our smart contract. :wink:

### Creating a new Truffle project
Thankfully, the Truffle framework has evolved quite a bit since the first release and creating a new truffle project is actually dead simple. Execute the following commands in your console:
```
mkdir MetaCoin
cd MetaCoin
truffle unbox metacoin
```

Which should give you the following outcome:
```
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!

Commands:

  Compile contracts: truffle compile
  Migrate contracts: truffle migrate
  Test contracts:    truffle test
```

MetaCoin is the example smart contract created by the Truffle framework, which we will compile, *migrate*, test - as described in the "commands" section of the feedback, and more in the following sections.

Congratulations on creating your first Ethereum blockchain project, this is a great step! You are now a true blockchain developer! Time to update the LinkedIn page :moneybag::moneybag::moneybag:

Ok, maybe not yet. Let's go see what's in this project and what we can do with it next. :smile:

### Structure of Truffle projects
What do we have now? Well, we have the following folders and files within our MetaCoin folder:
```
.
├── contracts
│   ├── ConvertLib.sol
│   ├── MetaCoin.sol
│   └── Migrations.sol
├── migrations
│   ├── 1_initial_migration.js
│   └── 2_deploy_contracts.js
├── test
│   ├── TestMetacoin.sol
│   └── metacoin.js
├── truffle-config.js
└── truffle.js

3 directories, 9 files
```
(I created this ascii tree, with the ```tree``` command. It's a nice tool, read more about this tool here: https://www.cyberciti.biz/faq/linux-show-directory-structure-command-line/)

So, there are 2 configuration files in the main folder - both empty, save for an export and some comments, 3 ```.sol``` files in the contracts folder: the MetaCoin itself, a simple library and a utility contract used by truffle, 2 migration javascript *scripts* in the migrations folder (we will come back to this later) and 2 different types of tests in the test folder (we will also touch upon these tests later).

There are also a few ".placeholder" files, which you can either ignore or delete. I'm deleting them.

For now, the most important parts are the ```.sol``` files, let's take a look at them next.

### Anatomy of a Solidity (.sol) file

There are 3 different ```.sol``` files in the contracts folder, and one in the test folder. We will take a look at the MetaCoin.sol file inside the contracts folder first:
```javascript
pragma solidity ^0.4.18;

import "./ConvertLib.sol";

// This is just a simple example of a coin-like contract.
// It is not standards compatible and cannot be expected to talk to other
// coin/token contracts. If you want to create a standards-compliant
// token, see: https://github.com/ConsenSys/Tokens. Cheers!

contract MetaCoin {
	mapping (address => uint) balances;

	event Transfer(address indexed _from, address indexed _to, uint256 _value);

	function MetaCoin() public {
		balances[tx.origin] = 10000;
	}

	function sendCoin(address receiver, uint amount) public returns(bool sufficient) {
		if (balances[msg.sender] < amount) return false;
		balances[msg.sender] -= amount;
		balances[receiver] += amount;
		Transfer(msg.sender, receiver, amount);
		return true;
	}

	function getBalanceInEth(address addr) public view returns(uint){
		return ConvertLib.convert(getBalance(addr),2);
	}

	function getBalance(address addr) public view returns(uint) {
		return balances[addr];
	}
}
```

For those familiar with Javascript, this should be quite readable. There are some significant differences between Solidity and Javascript, but the general structure is the same.

It starts off with declaring the version of Solidity by using the ```pragma``` keyword, in this case the file is declared to be written with version ```0.4.18``` in mind. The ```^``` sign in front of the version advises the compiler with a higher version (for example, version 0.5.0) to not compile this source file. The logic used here is the same as used in the NPM package manager, a great blog post explaining this can be found here: http://gunnariauvinen.com/what-do-the-and-mean-in-package-json/

The next command imports the library ```ConvertLib.sol``` from the same source folder. We will look into this library later, it is used in the ```getBalanceInEth``` function.

Then the contract itself is declared with the ```contract``` keyword. This is comparable to the ```class``` keyword in other languages, but here denotes a specific Ethereum smart contract.

Inside the contract, we have:
* a ```mapping```,
* an ```event``` , and,
* 4 ```function``` declarations.

Mappings are almost equal to ```hash tables``` or ```dictionaries``` in other languages, with the big difference being that there is no concept of a key being stored. They are (virtually) initialized with the value for all possible keys set to 0. Mappings can also be set to public, where Solidity will create a getter to access the data (more on this later). Detailed information on mappings can be found in the Solidity documentation: http://solidity.readthedocs.io/en/develop/types.html#mappings.

The private mapping declared in this contract keeps track of the amount of coins per address.

Events are used to write info to the blockchain logs, the event declared in this contract writes all transactions to the log. Included in the log are the sender (_from), the receiver (_to), and the amount of coins (_value). This event is called from the sendCoin function, at which point the information is written to the log.

From the 4 functions in the contract, the first function has the same name as the contract itself. This is called a ```constructor```. The constructor is optional, but there can be only one constructor per contract. In this constructor (which is called once, when the contract is created), the balance for the creator of the contract (```tx.origin```) is set to ```10000```.

The ```sendCoin``` function is the heart of the MetaCoin smart contract, which transfers coins from one user to the other. First it checks if the balance of the sender's account has enough coins to transfer. If this is not the case, the functions returns ```false```. If the account does have enough coins, the coins are first removed from the sender's account and added to the receiver's account. Then the event (created earlier) is called before returning ```true```.

Before this gets too boring, let's compile and run this contract. We will visit the other functions, the library, and tests later on.

### Compiling the MetaCoin :rocket:
Before you can interact or even run a smart contract, you need to compile it. Luckily this is easy with the Truffle framework, just execute the following command: ```truffle compile``` in the terminal. This should give the following output:
```
Compiling ./contracts/ConvertLib.sol...
Compiling ./contracts/MetaCoin.sol...
Compiling ./contracts/Migrations.sol...
Writing artifacts to ./build/contracts
```
There should be no errors (yet) :smile:

The next step is to deploy or "migrate" your newly compiled smart contract. This can be done by executing the following command: ```truffle migrate``` (unsurprising). This, however, gives the following error:
```
Error: No network specified. Cannot determine current network.
    at Object.detect (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:41338:23)
    at /usr/local/lib/node_modules/truffle/build/cli.bundled.js:202239:19
    (...)
```
This is because we need an Ethereum network to deploy our smart contracts to and we need to add this network to the configuration file. Let's go set that up now!

### Truffle built-in blockchain
We will be using the internal Ethereum test server embedded in truffle, which by default listens on port 9545. To start this server, open up a new console window and navigate to the location of your MetaCoin project (for me, this is `/user/johannes/projects/MetaCoin` - you can see your full path with the `pwd` command). In this new window, execute the `truffle develop` command, this should give you the following output (or something close to it):
```
Truffle Develop started at http://localhost:9545/

Accounts:
(0) 0x627306090abab3a6e1400e9345bc60c78a8bef57
(1) 0xf17f52151ebef6c7334fad080c5704d77216b732
(2) (...)

Private Keys:
(0) c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3
(1) (...)

Mnemonic: candy maple cake sugar pudding cream honey rich smooth crumble sweet treat
``` 
So, the Truffle development network is now running and listening on port 9545 for commands. Let's add it to our configuration.

Why are there **two** configuration files?
That's because the `truffle.js` file gives conflicts on Windows systems, which is why on windows systems the `truffle-config.js` is used. We will be using `truffle.js`.

Replace the content of `truffle.js` with:
```javascript
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 9545,
      network_id: "*" // Match any network id
    }
  }
};
```

Time to switch back to your first terminal and run the migrate command again (`truffle migrate`). This should now work and give the following output:
```
Using network 'development'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xd5c14cbf54e24515c0950af100e22949ddccacd0dedf8698c50f405ec8e147dd
  Migrations: 0x8cdaf0cd259887258bc13a92c0a6da92698644c0
Saving successful migration to network...
  ... 0xd7bc86d31bee32fa3988f1c1eabce403a1b5d570340a3a9cdba53a472ee8c956
Saving artifacts...
Running migration: 2_deploy_contracts.js
  Deploying ConvertLib...
  ... 0x417c0187ed5f036675e6e663c05f63a24d263c47b38c7b505a68a1a02fc18ffc
  ConvertLib: 0x345ca3e014aaf5dca488057592ee47305d9b3e10
  Linking ConvertLib to MetaCoin
  Deploying MetaCoin...
  ... 0x1419c3ca0d263c39a925c7300ddcd68e51be70bb292e06fa0e89edfe815fda0d
  MetaCoin: 0xf25186b5081ff5ce73482ad761db0eb0d25abfbf
Saving successful migration to network...
  ... 0x059cf1bbc372b9348ce487de910358801bbbd1c89182853439bec0afaee6c7db
Saving artifacts...
```

Congratulations - your contract is now **live**. :beers: On your local development network, at least. :smile:

Now let's see how we can interact with it from the command line!

### Hello world from MetaCoin
So, what can we do with a live smart contract? Good question! A smart contract is somewhat comparable with a database in this regard, the interaction with it is often through a driver or API. Often, for example with MySQL, there is also a client application to make your life easier. These applications also exist for Ethereum, but we will not be using them (yet). We are going to use the Truffle console. Fire it up using `truffle console` now.

Your prompt should show: `truffle(development)>`.
Copy-paste the following command into it: `MetaCoin.deployed().then(function(instance) {console.log(instance);});`

This should give you a *lot* of output, starting with:
```
TruffleContract {
  constructor:
   { [Function: TruffleContract]
     _static_methods:
      { setProvider: [Function: setProvider],
      (...)
```

And somewhere in the middle some unreadable mumble-jumble which represents the bytecode of the contract, ending with:

```
  Transfer: { [Function: bound ] 'address,address,uint256': [Function: bound ] },
  sendTransaction: [Function],
  send: [Function],
  allEvents: [Function: bound ],
  address: '0xf25186b5081ff5ce73482ad761db0eb0d25abfbf',
  transactionHash: null }
undefined
```

As you can see there are some functions visible from your smart contract, but also some unknown methods (for example: `setProvider` - we will explain this in the next part). This is the full printout of the Javascript object provided by Truffle for interacting from within a Javascript environment (like a webpage or Node console app).

Now, let's look up the balance of our first account (recall the address from starting up our local blockchain) by copy-pasting the following line into the truffle console:

```
MetaCoin.deployed().then(function(instance) {return instance.getBalance.call("0x627306090abab3a6e1400e9345bc60c78a8bef57")});
```
(Note: This address only works if you are using `truffle develop`, as it uses the same addresses every time you start it. If you are using a different ethereum blockchain development framework, fill in your first address - address 0 - here.)

Here we first grab the deployed instance of the MetaCoin contract (called `instance`) and call the getBalance function with the address of the first account on the blockchain. The `truffle migrate` command deploys the defined projects under this address, which is why there should be `10000` coins in this account!

Your output should look like the following:
```
BigNumber { s: 1, e: 4, c: [ 10000 ] }
```
Notice the 1000 at the end there: great success! :+1:
Later on we will see how to unpack the BigNumber type.

### Sending MetaCoins aka Interaction
Now for the true test - let's interact with the deployed smart contract. 

Here some theory is required, as calling functions that change the state of a smart contract (in this case the balance mapping) - cost ***gas***. Gas is the fee payed for executing the contract. Remember: the contract is being executed on the ethereum network by ethereum miners (when run in production). They will prioritize work with higher fees over work with lower fees, and if the amount of gas supplied is less than required for the actual work (computations required for the transaction), the transaction will fail and you lose your Gas.

This might sound a bit daunting, but the mechanism is in place to ensure the network is kept running and the miners are compensated for their work. More info on the how and why of Gas can be found in this excellent stackexchange post: https://ethereum.stackexchange.com/questions/3/what-is-meant-by-the-term-gas. Information on current Gas prices on the public ethereum network can be found here: https://ethgasstation.info/

So, with that theory behind us, let's transfer some coins in our development network! We will again use the truffle console, this time calling a different function:
```
MetaCoin.deployed().then(function(instance) {return instance.sendCoin("0xf17f52151ebef6c7334fad080c5704d77216b732", 10, {from: "0x627306090abab3a6e1400e9345bc60c78a8bef57"})});
```
Which should give you the following output:
```
 tx: '0x5d042f600ec6e7623dfff3879711bb3469c578a35836c8f7f458bfcb12211d38',
  receipt:
   { transactionHash: '0x5d042f600ec6e7623dfff3879711bb3469c578a35836c8f7f458bfcb12211d38',
     transactionIndex: 0,
     blockHash: '0x899c2d155c58442aa0fe6545a4e3618da2c005c0692882d8a9a62abe367a02c5',
     blockNumber: 6,
     gasUsed: 50960,
     cumulativeGasUsed: 50960,
     contractAddress: null,
     logs: [ [Object] ],
     status: 1 },
  logs:
   [ { logIndex: 0,
       transactionIndex: 0,
       transactionHash: '0x5d042f600ec6e7623dfff3879711bb3469c578a35836c8f7f458bfcb12211d38',
       blockHash: '0x899c2d155c58442aa0fe6545a4e3618da2c005c0692882d8a9a62abe367a02c5',
       blockNumber: 6,
       address: '0xf25186b5081ff5ce73482ad761db0eb0d25abfbf',
       type: 'mined',
       event: 'Transfer',
       args: [Object] } ] }
```
Note the ***gasUsed*** and ***cumulativeGasUsed*** figures, while we didn't specify any gas, this is the amount of gas used for the execution of this function.

Let's now check the balance of the 1st address again:
```
MetaCoin.deployed().then(function(instance) {return instance.getBalance.call("0x627306090abab3a6e1400e9345bc60c78a8bef57")});
```
This should now give 10000 - 10... is: `BigNumber { s: 1, e: 3, c: [ 9990 ] }`

And the balance of the 2nd address:
```
MetaCoin.deployed().then(function(instance) {return instance.getBalance.call("0xf17f52151ebef6c7334fad080c5704d77216b732")});
```
This should give us 10... or `BigNumber { s: 1, e: 1, c: [ 10 ] }`

Ow yeah, time to divide the spoils with your friends! :rocket:

But what about that gas? And... wasn't there something about a delay when adding a new transaction to a chain?
***Yes, there is*** - this will not work when interacting with a live ethereum chain. There you need to provide enough Gas (as mentioned earlier) to make sure the transaction is executed *and* the transaction will take some time to execute. The gas can be provided in the same way we provide the "from" address, as parameter in this object. The call would then look like this:
```
MetaCoin.deployed().then(function(instance) {return instance.sendCoin("0xf17f52151ebef6c7334fad080c5704d77216b732", 10, {from: "0x627306090abab3a6e1400e9345bc60c78a8bef57", gas: 80000})});
```
In this command, we explicitly pass ***80000*** gas to the function.

When running this command on a blockchain which does not automatically mine blocks (our `truffle develop` does), it will only give a result when the transaction is mined - which can take a while! For most use cases, it is better to make these calls a-synchronous. This can be done by calling *.then()* and passing a callback. We will come back to this in the next part, in the automated tests and when calling the blockchain from a website.

## Part 2: Tests and interaction from websites
Now we have a running smart contract on a (local) blockchain. But how do we know that it functions correctly? This can be assessed by running tests and interacting with the contract using a User Interface.

For the curious, tests, and specifically automated tests, have become popular due to the *Test Driven Development* paradigm. This way of working has become popular due to the book with the same name written by Kent Beck in 2003. The TDD-cycle is:
1. Add a test
2. Run all tests and see if the new test fails
3. Write the code
4. Run tests
5. Refactor code
... and then repeat!

In contract with *traditional* development work, here the test is added first, then the code. Also, in step 2 ***all*** tests are run, to see if the newly added test fails. Then in step 4 ***all*** tests are run again. This leads to a large demand for automated (and *quick*) tests. We will take a look at how to test smart contracts soon.

Another way to check if a contract is behaving as it should, is to interact with it using a User Interface - we will explore the possibilities to interact with the smart contract from a web browser in the second half of this part. 

### Automated tests within Truffle
How to run automated tests with the Truffle framework?

Well, first make sure the development blockchain network is still running (`truffle develop`) and then run `truffle test` in the MetaCoin folder. You should be getting an output like this:
```
Using network 'development'.

Compiling ./contracts/ConvertLib.sol...
Compiling ./contracts/MetaCoin.sol...
Compiling ./test/TestMetacoin.sol...
Compiling truffle/Assert.sol...
Compiling truffle/DeployedAddresses.sol...


  TestMetacoin
    ✓ testInitialBalanceUsingDeployedContract (55ms)
    ✓ testInitialBalanceWithNewMetaCoin (50ms)

  Contract: MetaCoin
    ✓ should put 10000 MetaCoin in the first account
    ✓ should call a function that depends on a linked library
    ✓ should send coin correctly (75ms)


  5 passing (619ms)
```

Great!

From the first line we can learn that it is using our development network (defined in `truffle.js`). Then it compiles not only the Contract(s), but also the tests and some help files for the tests (`Assert.sol` and `DeployedAddresses.sol`). After all this, it runs two test-suites:
* TestMetacoin
* Contract: MetaCoin

The first has 2 tests, the second has 3.
All the tests are passing, and do this within 1 second. (hurray!)

So where can we find these test-suits?
Both suites are defined in the `./test` folder - let's start with `./test/TestMetacoin.sol`, which has the following content:

```javascript
pragma solidity ^0.4.2;

import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/MetaCoin.sol";

contract TestMetacoin {

  function testInitialBalanceUsingDeployedContract() public {
    MetaCoin meta = MetaCoin(DeployedAddresses.MetaCoin());

    uint expected = 10000;

    Assert.equal(meta.getBalance(tx.origin), expected, "Owner should have 10000 MetaCoin initially");
  }

  function testInitialBalanceWithNewMetaCoin() public {
    MetaCoin meta = new MetaCoin();

    uint expected = 10000;

    Assert.equal(meta.getBalance(tx.origin), expected, "Owner should have 10000 MetaCoin initially");
  }

}
```

This is a smart contract itself! The contract is called `TestMetacoin`, imports the Assert and DeployedAddresses statements (from `truffle/Assert.sol` and `truffle/DeployedAddresses.sol`), imports the MetaCoin smart contract, and has 2 functions (tests):
*testInitialBalanceUsingDeployedContract()
*testInitialBalanceWithNewMetaCoin()

As the name implies, the first of these tests checks (Asserts) that freshly deployed contracts set the owner balance to 10000. The second of these tests checks the same for new contracts. Both tests have the same *Assert* statement, which is also shown in the overview after running the tests.

Let's change this value to 10 for the first test, and see if the test-suite still returns true. (we change the `uint expected = 1000;` to `uint expected = 10;`)

Running our tests then gives the following result:
```
Using network 'development'.

Compiling ./contracts/ConvertLib.sol...
Compiling ./contracts/MetaCoin.sol...
Compiling ./test/TestMetacoin.sol...
Compiling truffle/Assert.sol...
Compiling truffle/DeployedAddresses.sol...


  TestMetacoin
    1) testInitialBalanceUsingDeployedContract

    Events emitted during test:
    ---------------------------

    TestEvent(result: <indexed>, message: Owner should have 10000 MetaCoin initially (Tested: 10000, Against: 10))

    ---------------------------
    ✓ testInitialBalanceWithNewMetaCoin

  Contract: MetaCoin
    ✓ should put 10000 MetaCoin in the first account
    ✓ should call a function that depends on a linked library
    ✓ should send coin correctly (57ms)


  4 passing (644ms)
  1 failing

  1) TestMetacoin testInitialBalanceUsingDeployedContract:
     Error: Owner should have 10000 MetaCoin initially (Tested: 10000, Against: 10)
      at /usr/local/lib/node_modules/truffle/build/cli.bundled.js:319138:17
      at Array.forEach (<anonymous>)
      at processResult (/usr/local/lib/node_modules/truffle/build/cli.bundled.js:319136:19)
      at <anonymous>
      at process._tickCallback (internal/process/next_tick.js:160:7)
```
As expected, one of the tests now fails, showing an expected result of 10, where it finds 10000. Let's change it back to 10000 and make sure it runs without problems again.

The second test-suite is defined in `/test/metacoin.js`. The content of this Javascript file is:

```javascript
var MetaCoin = artifacts.require("./MetaCoin.sol");

contract('MetaCoin', function(accounts) {
  it("should put 10000 MetaCoin in the first account", function() {
    return MetaCoin.deployed().then(function(instance) {
      return instance.getBalance.call(accounts[0]);
    }).then(function(balance) {
      assert.equal(balance.valueOf(), 10000, "10000 wasn't in the first account");
    });
  });
  it("should call a function that depends on a linked library", function() {
    var meta;
    var metaCoinBalance;
    var metaCoinEthBalance;

    return MetaCoin.deployed().then(function(instance) {
      meta = instance;
      return meta.getBalance.call(accounts[0]);
    }).then(function(outCoinBalance) {
      metaCoinBalance = outCoinBalance.toNumber();
      return meta.getBalanceInEth.call(accounts[0]);
    }).then(function(outCoinBalanceEth) {
      metaCoinEthBalance = outCoinBalanceEth.toNumber();
    }).then(function() {
      assert.equal(metaCoinEthBalance, 2 * metaCoinBalance, "Library function returned unexpected function, linkage may be broken");
    });
  });
  it("should send coin correctly", function() {
    var meta;

    // Get initial balances of first and second account.
    var account_one = accounts[0];
    var account_two = accounts[1];

    var account_one_starting_balance;
    var account_two_starting_balance;
    var account_one_ending_balance;
    var account_two_ending_balance;

    var amount = 10;

    return MetaCoin.deployed().then(function(instance) {
      meta = instance;
      return meta.getBalance.call(account_one);
    }).then(function(balance) {
      account_one_starting_balance = balance.toNumber();
      return meta.getBalance.call(account_two);
    }).then(function(balance) {
      account_two_starting_balance = balance.toNumber();
      return meta.sendCoin(account_two, amount, {from: account_one});
    }).then(function() {
      return meta.getBalance.call(account_one);
    }).then(function(balance) {
      account_one_ending_balance = balance.toNumber();
      return meta.getBalance.call(account_two);
    }).then(function(balance) {
      account_two_ending_balance = balance.toNumber();

      assert.equal(account_one_ending_balance, account_one_starting_balance - amount, "Amount wasn't correctly taken from the sender");
      assert.equal(account_two_ending_balance, account_two_starting_balance + amount, "Amount wasn't correctly sent to the receiver");
    });
  });
});
```

Ok, let's take a look at what's happening here. First off, we import the definition of the MetaCoin by calling `artifacts.require()`. We assign this to the local `MetaCoin` variable, which we use in the tests to interact with the contract. The `contract` function has two arguments, a string defining the name of the test-suite (MetaCoin is passed here) and a function that is called with as argument the accounts in the test environment.

The `it` function is called several times here, to defined multiple tests in this test-suite. These tests are executed one-by-one in the order defined. The first test checks (again) if a newly created contract has 10000 coins in it. If this test is placed after that last test, in which 10 coins are transferred to the second account, it will fail.

Interesting to note is the chained actions in the last test here, also reading the balance of an account is an a-sync call when using Javascript, which can make the tests difficult to read.

I have created a pull request on the template repository with a rewritten test, making use of `async` and `await` which greatly increases the readability and maintainability of the tests. You can see it here: https://github.com/truffle-box/metacoin-box/pull/1 - don't forget to vote on it! :smile:

In Part 3 of this guide, we will take a look at automating the tests, first we are going to interact with a smart contract from the browser.

### Interaction with the contract from the browser

All fine, these interactions from the console - but we actually want a working website, right?! Right!

Let's start with creating a new folder called "public" under "MetaCoin" and add "index.html" in it:
```
.
├── build
│   └── contracts
│       ├── ConvertLib.json
│       ├── MetaCoin.json
│       └── Migrations.json
├── contracts
│   ├── ConvertLib.sol
│   ├── MetaCoin.sol
│   └── Migrations.sol
├── migrations
│   ├── 1_initial_migration.js
│   └── 2_deploy_contracts.js
├── public         <---- new
│   └── index.html <---- new
├── test
│   ├── TestMetacoin.sol
│   └── metacoin.js
├── truffle-config.js
└── truffle.js

6 directories, 13 files
```

And just put a basic HTML page in index.html, for now:
```html
<html>
    <body>
        <h1>Hello</h1>
    </body>
</html>
```

Next, install static-server, by running the following command: `sudo npm -g install static-server`. This gives us the ability to start a simple web-server super easy. :unicorn:

Let's try it out with the public folder: `static-server public/`. This should give the following output:
```
options.index is now deprecated please use options.templates.index instead.
* Static server successfully started.
* Serving files at: http://localhost:9080
* Press Ctrl+C to shutdown.
```

We can safely ignore the first line, our server is successfully started and you can find it at http://localhost:9080!

Now it is time to add interaction with our contract, we will be doing this with 'web3.js'. The easiest way to get it into our page, is to download the javascript file and importing it. It can be found at https://github.com/ethereum/web3.js/blob/develop/dist/web3.js, we will download it using `wget`. This step is optional, you can download it using any tool, just make sure it ends up in the `public/` folder ;-)

First, install `wget` using `brew install wget`, if you haven't done this yet. Then navigate to the public folder and run `wget https://raw.githubusercontent.com/ethereum/web3.js/develop/dist/web3.js`. Your output should then be:
```
--2018-01-08 16:35:02--  https://raw.githubusercontent.com/ethereum/web3.js/develop/dist/web3.js
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.0.133, 151.101.64.133, 151.101.128.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|151.101.0.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 88166 (86K) [text/plain]
Saving to: 'web3.js'

web3.js             100%[===================>]  86.10K  --.-KB/s    in 0.04s

2018-01-08 16:35:02 (2.10 MB/s) - 'web3.js' saved [495873]
```

Let's now test if `web3` can connect to our local blockchain, replace the content of index.html with the following:
```html
<html>
    <body>
        <script src="./web3.js"></script>
        <script>
            var provider = new Web3.providers.HttpProvider('http://127.0.0.1:9545');
            var web3 = new Web3(provider);

            web3.eth.getAccounts((error, accounts) => {
                document.write("Address of account 0: " + accounts[0]);
            });
        </script>
    </body>
</html>
```

And refresh the page at http://localhost:9080. It should show:
```
Address of account 0: 0x627306090abab3a6e1400e9345bc60c78a8bef57
```

Well, now we have a connection with our local running ethereum blockchain :rocket:, but not with our MetaCoin smart contract yet. Let's get the balance of MetaCoin on this account next.

To interface with the deployed smart contract, we need the ABI, the Application Binary Interface, of the contract. This is a standard interface, generated from our Solidity file when it was compiled. More information on this can be found in the documentation: https://solidity.readthedocs.io/en/develop/abi-spec.html. For now, we have just included it (and the deployed address) in our html file. We will make this more flexible in less simplistic examples.

Replace the content of `./public/index.html` with:
```html
<html>
<body>
    <script src="./web3.js"></script>
    <script>
        var MetaCoin = [
            {
                "constant": true,
                "inputs": [
                    {
                        "name": "addr",
                        "type": "address"
                    }
                ],
                "name": "getBalanceInEth",
                "outputs": [
                    {
                        "name": "",
                        "type": "uint256"
                    }
                ],
                "payable": false,
                "stateMutability": "view",
                "type": "function"
            },
            {
                "constant": false,
                "inputs": [
                    {
                        "name": "receiver",
                        "type": "address"
                    },
                    {
                        "name": "amount",
                        "type": "uint256"
                    }
                ],
                "name": "sendCoin",
                "outputs": [
                    {
                        "name": "sufficient",
                        "type": "bool"
                    }
                ],
                "payable": false,
                "stateMutability": "nonpayable",
                "type": "function"
            },
            {
                "constant": true,
                "inputs": [
                    {
                        "name": "addr",
                        "type": "address"
                    }
                ],
                "name": "getBalance",
                "outputs": [
                    {
                        "name": "",
                        "type": "uint256"
                    }
                ],
                "payable": false,
                "stateMutability": "view",
                "type": "function"
            },
            {
                "inputs": [],
                "payable": false,
                "stateMutability": "nonpayable",
                "type": "constructor"
            },
            {
                "anonymous": false,
                "inputs": [
                    {
                        "indexed": true,
                        "name": "_from",
                        "type": "address"
                    },
                    {
                        "indexed": true,
                        "name": "_to",
                        "type": "address"
                    },
                    {
                        "indexed": false,
                        "name": "_value",
                        "type": "uint256"
                    }
                ],
                "name": "Transfer",
                "type": "event"
            }
        ]
        var provider = new Web3.providers.HttpProvider('http://127.0.0.1:9545');
        var web3 = new Web3(provider);

        web3.eth.getAccounts((error, accounts) => {
            document.writeln("Address of account 0: " + accounts[0] + "<br />");

            var MetaCoinContract = web3.eth.contract(MetaCoin);
            var myMetaCoinInstance = MetaCoinContract.at('0xf25186b5081ff5ce73482ad761db0eb0d25abfbf');
            var balance = myMetaCoinInstance.getBalance(accounts[0]);
            
            document.writeln("Balance for account 0: " + balance.valueOf());
        });
    </script>
</body>
</html>
```

When you now refresh your page (http://localhost:9080/) you should see the following output:
```
Address of account 0: 0x627306090abab3a6e1400e9345bc60c78a8bef57
Balance for account 0: 10000
```

That concludes part 2, of this guide :tada:

Stay tuned for part 3 and 4, which will show you how to automate this stuff so your tests are being run automatically and how to integrate this is more functional websites (using the Vue and React frameworks).

Also: Let me know if there are any errors in this guide by either:
1. Creating an issue describing the error,
2. Submitting a pull request to fix the error, or,
3. Sending me an email or message on Github

Nr 3 is also usable for other feedback :smile:

## Part 3: Automating everything (coming soon)
## Part 4: Other possibilities with Solidity and Web3 (coming soon)
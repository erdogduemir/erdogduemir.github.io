﻿## How to install and run the front-end:

- Download and install node.js\
node.js reference link: https://nodejs.org/en/download/

- Download and install npm or yarn, you need to install npm to download yarn\
yarn reference link: https://classic.yarnpkg.com/lang/en/docs/install/#windows-stable \
npm reference link: https://docs.npmjs.com/downloading-and-installing-node-js-and-npm

- Install the dependency packages\
in the project folder navigate to directory using
```
cd .\contract_for_front_end\ui_sol_deneme2\ui_sol_deneme\sol_ui
```
install the dependencies using "npm install" or "yarn install"\
install next if needed using "npm install next" or "yarn install next"\
Clone the repo from: https://github.com/Cem-Kaya/SU_Govern

- To run the front-end:\
in the project folder change the directory using
```
cd .\contract_for_front_end\ui_sol_deneme2\ui_sol_deneme\sol_ui
```
run the front end using "npm run dev" or "yarn dev"

- Deploying Contracts on TestNet:\
Remix IDE  was used for this.\
Contracts are compiled in this order: 1) token.sol, 2) creator.sol, 3) newDAO1.sol 4) newFactory1.sol.\
Contracts are deployed in this order: 1) creator.sol, 2) newFactory1.sol.\
While deploying newFactory1.sol use the address of creator.sol.

- Connecting to Front End:\
The files that needs modification are found in the directory below.
```
cd .\contract_for_front_end\ui_sol_deneme2\ui_sol_deneme\sol_ui\pages
```
In dao.js and index.js, the address of daoFactory should be typed in the statement where the daoFactoryContract is defined. In dao.js, line 114, and in index.js, line 180.\
**In addition to this, in order to get YK privileges, the first admin of the Top DAO needs to withdraw 1 YK token from TOP DAO. This can be done through the frontend. This is a one time case during creation. The first contract needs to be deployed using Remix IDE.**

## Contracts

### Creator.sol

The below Solidity contract is named `creator` and it implements the `icreator` interface. The purpose of this contract is to create two tokens and transfer ownership of those tokens to the factory contract.

The `createToken` function is the main function of this contract. It takes four parameters: `yk_token_name`, `yk_token_symbol`, `voter_token_name`, and `voter_token_symbol`. These parameters represent the names and symbols of the two tokens to be created.

Inside the function, the address of the factory contract is obtained from the `msg.sender` variable. Two instances of the `SUToken` contract are created, representing the two tokens. The `SUToken` contract is assumed to be defined in the `token.sol` file.

After the tokens are created, the ownership of each token is transferred to the factory contract using the `transferOwnership` function of the `SUToken` contract.

Finally, the addresses of the created tokens are returned as a tuple.

Please note that some parts of the contract are commented out, such as the import statement for `DAO.sol`. It appears that those parts are not relevant to the current functionality of the contract.

Remember to customize and adapt this documentation according to your specific needs, such as providing more details about the purpose and usage of the contract or explaining any additional functions or dependencies.

### Migrations.sol

The Migrations contract is a simple contract that stores the last completed migration. The contract has a `setCompleted` function that sets the `last_completed_migration` variable to the value passed in as a parameter. However, this function can only be called by the contract owner.

The restricted modifier restricts the function so that only the owner of the contract can call it. If a non-owner account attempts to call the function, an exception is thrown and the transaction is reverted.

The contract also has an `owner` variable that is initialized with the address of the contract deployer. This address is used in the restricted modifier to check that the calling account is the owner of the contract.

This contract can be used as a base contract for contract migrations.

### newDAO1.sol

The MyDAO contract implements a decentralized autonomous organization (DAO) that allows members to create proposals and vote on them. It defines a Proposal struct that contains information about each proposal, including its unique identifier, author, name, description, creation timestamp, options, status, power, and proposal information type.
The contract also includes a private function stringsEquals that compares two strings and returns a boolean indicating whether they are equal.
The public variables of the contract include the name, description, debugging information, image URL, and unique identifier of the DAO. It also defines two enums for voting options and proposal status.
The contract imports the itoken.sol and newFactory1.sol contracts and declares a DAOFactory variable for the newFactory1 contract.
The contract is responsible for creating and managing proposals, and tracking voting shares and tokens for voters and YK token holders.

The contract contains several mappings and constants used for tracking and storing data. \
These include:

- `proposals`: A mapping of proposal IDs to Proposal objects. This is used to store all the proposals created by the contract.

- `votes`: A mapping of voter addresses to proposal IDs to boolean values. This is used to track which addresses have already voted for a given proposal to prevent double voting.

- `tokens_not_refunded`: A mapping of voter addresses to proposal IDs to boolean values. This is used to track whether tokens have been refunded for a proposal to prevent multiple refunds.

- `token_amount_to_be_refunded`: A mapping of voter addresses to proposal IDs to refund amounts. This is used to store the amount of tokens to be refunded for a voter.

- `voter_shares_to_be_given`: A mapping of voter addresses to voter share amounts. This is used to track the number of voter shares to be given to a voter.

- `yk_shares_to_be_given`: A mapping of YK token holder addresses to YK share amounts. This is used to track the number of YK shares to be given to a YK token holder.

- `voter_token`: An instance of the ISUToken contract. This is the contract for the voter token used by the DAO.

- `yk_token`: An instance of the ISUToken contract. This is the contract for the YK token used by the DAO.

- `CREATE_PROPOSAL_MIN_SHARE`: A constant that sets the minimum number of shares required to create a proposal.

- `VOTING_PERIOD`: A constant that sets the duration of the voting period for proposals.

- `nextProposalId`: An integer that tracks the ID for the next proposal to be created.

- `transferLock`: A mapping of addresses to boolean values that tracks the transfer lock status for addresses.

The constructor function initializes the DAO contract with the following parameters:

- `_dao_name`: A string representing the name of the DAO.

- `_imageUrl`: A string representing the URL of the DAO's image.

- `_dao_description`: A string representing the description of the DAO.

- `_dao_id`: An integer representing the ID of the DAO.

- `first_yk`: The address of the initial YK token holder.

- `yk_token_in`: An instance of the ISUToken contract representing the YK token used by the DAO.

- `voter_token_in`: An instance of the ISUToken contract representing the voter token used by the DAO.

- `_factory`: An instance of the DAOFactory contract representing the DAO factory used to create the DAO.

In the constructor, the factory, `dao_name`, `dao_id`, `dao_description`, `yk_token`, `voter_token`, and `imageUrl` variables are set to their corresponding parameter values. The `yk_shares_to_be_given` and `voter_shares_to_be_give`n mappings are then initialized with a share value of 1 * 10 ** 18 for the initial YK token holder.

A brief description of each function:

- `mint_from_DAO_yk_token`: This internal function allows the DAO contract to mint YK tokens. The function takes in a parameter _amount, which specifies the amount of YK tokens to be minted.

- `mint_from_DAO_voter_token`: This internal function allows the DAO contract to mint voter tokens. The function takes in a parameter _amount, which specifies the amount of voter tokens to be minted.

- `iterate_proposals`: This public function iterates through all proposals and emits proposal information for each proposal. It updates the active voter lock if the proposal's status is not pending. The function does not take in any parameters.

- `accept_proposal`: This external function accepts a proposal by changing its status to Status.Accepted. The function takes in a parameter _proposalId, which specifies the ID of the proposal to be accepted.

- `reject_proposal`: This external function rejects a proposal by changing its status to Status.Rejected. The function takes in a parameter _proposalId, which specifies the ID of the proposal to be rejected.

- `pending_proposal`: This external function sets a proposal's status to Status.Pending. The function takes in a parameter _proposalId, which specifies the ID of the proposal to be set as pending.

- `withdraw_voter_tokens`: This external function allows a user to withdraw voter tokens from the DAO. The function takes in a parameter _amount, which specifies the amount of voter tokens to be withdrawn. The function checks whether the user has enough shares and transfers the specified amount of voter tokens from the DAO to the user. It also mints voter tokens for the DAO.

- `withdraw_yk_tokens`: This external function allows a user to withdraw YK tokens from the DAO. The function takes in a parameter _amount, which specifies the amount of YK tokens to be withdrawn. The function checks whether the user has enough shares and transfers the specified amount of YK tokens from the DAO to the user. It also mints YK tokens for the DAO.

- `send_yk_tokens_to_address_yk`: This function sends YK tokens to a specified address. It takes in two parameters: `yk_candidate`, which is the address to send the YK tokens to, and `_amount`, which is the amount of YK tokens to send. It requires the caller to have YK privileges and updates the amount of YK tokens that will be given to the specified address.

- `send_yk_tokens_to_address_yk_directly`: This function sends YK tokens directly to a specified address from the DAO. It takes in two parameters: `yk_candidate`, which is the address to send the YK tokens to, and `_amount`, which is the amount of YK tokens to send. It requires the caller to have YK privileges and updates the amount of YK tokens that will be given to the specified address. In addition to this, it also mints YK tokens from the DAO.

- `send_voter_tokens_to_address_yk`: This function sends voter tokens to a specified address. It takes in two parameters: `voter_candidate`, which is the address to send the voter tokens to, and `_amount`, which is the amount of voter tokens to send. It requires the caller to have YK privileges and updates the amount of voter tokens that will be given to the specified address.

- `send_voter_tokens_to_address_yk_directly`: This function sends voter tokens directly to a specified address from the DAO. It takes in two parameters: `voter_candidate`, which is the address to send the voter tokens to, and `_amount`, which is the amount of voter tokens to send. It requires the caller to have YK privileges and updates the amount of voter tokens that will be given to the specified address. In addition to this, it also mints voter tokens from the DAO.

- `createProposal`: This function creates a new proposal. It takes in six parameters: `name`, which is the name of the proposal; `description`, which is the description of the proposal; `_options`, which is an array of vote options; `_options_num`, which is an array of corresponding vote option numbers; `_power`, which is the maximum vote power for the proposal; and `_type`, which is the type of the proposal (0 for normal, 1 for weighted). It requires the caller to have YK privileges and creates a new proposal with the specified parameters. It also updates the proposal count.

- `vote`: This function performs a non-weighted vote on a proposal. It takes in three parameters: `_proposalId`, which is the ID of the proposal; `_vote`, which is an array of vote options; and `_power`, which is an array of corresponding vote powers. It requires the caller to have voter privileges and updates the vote count for the specified proposal.

The code uses several Solidity concepts, such as function modifiers (`require` statements) and structs (`Proposal`), to ensure that the functions are secure and perform as intended.

- `vote_power`: This function performs a weighted vote on a proposal. It takes three arguments: `_proposalId` (an unsigned integer representing the ID of the proposal), `_vote` (a string array representing the vote options), and `_power` (an unsigned integer array representing the corresponding vote powers). The function first checks if the voter has already voted, if they have enough shares to vote on the proposal, and if the voting period is still open. If all conditions are met, the function calculates the total power of the votes, checks that the total power does not exceed the proposal power, and then adds the vote powers to the corresponding proposal options. Finally, it updates the voter's vote status and active voter lock for the proposal.

- `vote_power_weighted`: This function is similar to `vote_power`, but it includes an additional argument `weight`, which is a uint representing the weight to apply to the vote powers. The weight is used to determine the influence of each voter's shares on the proposal. The function first checks if the voter has already voted, if they have enough shares to vote on the proposal (with the option to change the weight), and if the voting period is still open. If all conditions are met, the function calculates the total power of the votes, checks that the total power does not exceed the proposal power, and then adds the vote powers (multiplied by the weight) to the corresponding proposal options. Finally, it updates the voter's vote status and active voter lock for the proposal.

- `retrieve_proposal_names`: This function simply retrieves the names of all proposals. It returns an array of proposal names.

Note that the code uses some external functions, such as `stringsEquals`, which is a custom implementation of string comparison, and `voter_token.balanceOf`, which retrieves the balance of a voter's tokens.

- `getProposalName()`: This function retrieves an array of proposal names. It loops through all proposals and adds the name of each proposal to an array called `myprops`. The function then returns this array.

- `getDaoid()`: This function retrieves the DAO ID. It simply returns the value of the `dao_id` variable.

- `has_yk_priviliges(address chk)`: This function checks whether the given address has YK (Yieldly Token) privileges. It first checks if the balance of YK tokens of the given address is greater than or equal to 1. If this condition is satisfied, it returns `true`. Otherwise, it retrieves the current DAO using the `factory.getCurrentDAO()` function and checks whether any of the parent DAOs have a YK balance greater than or equal to 1 * 10^18. If this condition is satisfied, it returns `true`. Otherwise, it returns `false`.

- `getProposalDescription()`: This function retrieves an array of proposal descriptions. It loops through all proposals and adds the description of each proposal to an array called `myprops`. The function then returns this array.

- `getProposalVoteNames(uint proposal_idd)`: This function retrieves an array of vote names/options for a specific proposal. It retrieves the `options` array of the proposal with the given ID and adds each option to an array called `mypropvotes`. The function then returns this array.

- `getProposalVoteNumbers(uint proposal_idd)`: This function retrieves an array of vote numbers/totals for a specific proposal. It retrieves the `options_num` array of the proposal with the given ID and adds each vote number to an array called `mypropvotenums`. The function then returns this array.

- `getProposalPower(uint proposal_idd)`: This function retrieves the power of a specific proposal. It retrieves the `power` variable of the proposal with the given ID and returns it.

- `getProposalType(uint proposal_idd)`: This function retrieves the type of a specific proposal. This function is not defined in the provided code.

- `getProposalType(uint proposal_idd)`: This function takes a proposal ID as input and returns a string representing the type of the proposal. The string value is obtained from a mapping called `proposals` which maps a proposal ID to a struct containing information about the proposal. The function is `view` type, meaning it doesn't modify the state of the contract and can be called without incurring any gas cost.

- `dao_delegation_single_getback_amount_voter(address from, uint256 amount)`: This function allows a voter to retrieve a single token that was delegated to another address. The `from` parameter specifies the address from which the token will be retrieved and `amount` specifies the number of tokens to be retrieved. The function checks that the number of tokens being retrieved is less than or equal to the number of debt tokens held by the `from` address. This function can only be called externally.

- `dao_delegation_single_getback_all_voter(address from)`: This function allows a voter to retrieve all tokens that were delegated to another address. The `from` parameter specifies the address from which all tokens will be retrieved. This function can only be called externally.

- `dao_delagation_multiple_getback_all_voter()`: This function allows a voter to retrieve all tokens that were delegated to multiple addresses. This function can only be called externally. This function has no parameters.

- `dao_clawback_single_voter(address from)`: This function allows the DAO to retrieve a single token that was delegated to another address by a voter. The `from` parameter specifies the address from which the token will be retrieved. The function checks that the caller has YK (Yield Keeper) privileges. This function can only be called externally.

- `dao_clawback_all_voter()`: This function allows the DAO to retrieve all tokens that were delegated to another address by voters. The function checks that the caller has YK (Yield Keeper) privileges. This function can only be called externally.

- `dao_delegation_single_getback_amount_yk(address from, uint256 amount`: This function allows the DAO to retrieve a single token that was delegated to another address by a YK. The `from` parameter specifies the address from which the token will be retrieved and `amount` specifies the number of tokens to be retrieved. The function checks that the number of tokens being retrieved is less than or equal to the number of debt tokens held by the `from` address. This function can only be called externally.

- `dao_delegation_single_getback_all_yk(address from)`:
This function allows the DAO to retrieve all tokens that were delegated to another address by a YK. The `from` parameter specifies the address from which all tokens will be retrieved. This function can only be called externally.

- `dao_delagation_multiple_getback_all_yk()`: This function allows the DAO to retrieve all tokens that were delegated to multiple addresses by a YK. The function checks that the caller has YK (Yield Keeper) privileges. This function can only be called externally. This function has no parameters.

- `dao_clawback_single_yk(address to)`: This function allows the DAO to retrieve a single token that was delegated to another address by a YK. The `to` parameter specifies the address to which the token will be clawed back. The function checks that the caller has YK.

### newFactory1.sol

This is a smart contract named `DAOFactory` that creates and manages DAOs (Decentralized Autonomous Organizations). The contract has several mappings that keep track of various properties of DAOs, such as their parent-child relationships, associated token contracts, and existence.

The contract uses the SPDX-License-Identifier to specify the license under which the contract is released. In this case, it is the GNU General Public License version 3.0.

The contract specifies the Solidity version requirement as `>=0.7.0 <0.9.0`, and also imports several other contracts using `import` statements.

The contract defines a constructor that takes an address parameter for the `myCreator` contract, which is used to create tokens.

- `createDAOTop`: Creates a new top-level DAO. It takes several string parameters for the name and description of the DAO, as well as the names and symbols of two associated token contracts. The function first checks that the caller is an approved DAO creator before creating two new token contracts using the `my_creator` contract. It then creates a new `MyDAO` contract, passing in various parameters including the new token contracts and DAOFactory contract itself. The function also assigns allowances of tokens to the DAO contract, assigns the DAO to use the YK and voter tokens, and transfers tokens from the factory to the DAO contract. Finally, the function updates several mappings to keep track of the new DAO.

The child DAOs are created with their own YK and voter tokens, which are used to participate in voting and decision-making processes within the DAO.

The contract contains the following functions:

1. `createChildDAO`: This function is used to create a new child DAO under a parent DAO. It takes the following parameters:

- `parent`: The address of the parent DAO contract.
- `dao_name`: The name of the new DAO.
- `dao_description`: A brief description of the new DAO.
- `yk_token_name`: The name of the YK token to be used by the new DAO.
- `yk_token_symbol`: The symbol of the YK token to be used by the new DAO.
- `voter_token_name`: The name of the voter token to be used by the new DAO.
- `voter_token_symbol`: The symbol of the voter token to be used by the new DAO.

The function first checks if the caller has YK privileges in the parent DAO. If the caller is not a YK of the parent DAO, the function throws an error. It then creates new YK and voter token contracts using a token creator contract. The function then creates a new child DAO contract with the specified parameters and assigns the YK and voter tokens to the new DAO. The function also saves the associations between the DAO and its tokens, marks the child DAO as existing, adds the child DAO to the parent's list, increments the number of children for the parent DAO, and sets the parent DAO for the child. Finally, the function transfers tokens from the factory to the child DAO contract, stores the child DAO contract, and increments the DAO ID for the next DAO creation.

2. `mint_dao_yk`: This function is used to mint new YK tokens for a specified child DAO. It takes the following parameters:

- `to_be_minted`: The child DAO contract to mint the YK tokens for.
- `amount`: The amount of YK tokens to mint.
- `sender`: The address of the YK token holder.

The function first checks if the caller is a YK of the selected DAO. If the caller is not a YK of the selected DAO, the function throws an error. It then checks if the caller is the DAO itself. If the caller is not the DAO, the function throws an error. Finally, the function mints the specified amount of YK tokens for the child DAO.

3. `mint_dao_voter`: This function is used to mint new voter tokens for a specified child DAO. It takes the following parameters:

- `to_be_minted`: The child DAO contract to mint the voter tokens for.
- `amount`: The amount of voter tokens to mint.
- `sender`: The address of the YK token holder.

The function first checks if the caller is a YK of the selected DAO. If the caller is not a YK of the selected DAO, the function throws an error. It then checks if the caller is the DAO itself. If the caller is not the DAO, the function throws an error. Finally, the function mints the specified amount of voter tokens for the child DAO.

4. `addCreator(address input_creator)`: Adds a new address to the list of DAO creators.
- `input_creator`: The address to be added as a DAO creator.
- `public`: The function can be called from outside the contract.
- Returns: None.
- Example usage: `addCreator(0x1234567890123456789012345678901234567890);`

5. `getParentDAO(MyDAO mydao)`: Retrieves the parent DAO of a given child DAO.
- `mydao`: The child DAO contract for which to retrieve the parent DAO.
- `public view`: The function can be called from outside the contract and does not modify any state variables.
- Returns: The parent DAO contract.
- Example usage: `getParentDAO(mydao);`

6. `getCurrentDAO(uint256 id)`: Retrieves the DAO contract based on its ID.
- `id`: The ID of the DAO to retrieve.
- `public view`: The function can be called from outside the contract and does not modify any state variables.
- Returns: The DAO contract.
- Example usage: `getCurrentDAO(123);`

7. `delete_DAO(MyDAO to_be_deleted, address sender)`: Deletes a DAO and its child DAOs recursively.
- `to_be_deleted`: The DAO contract to be deleted, ```sender``` - the address of the sender requesting the deletion.
- `public`: The function can be called from outside the contract.
- Returns: None.
- Example usage: `delete_DAO(mydao, 0x1234567890123456789012345678901234567890);`

### Token.sol
The purpose of this contract is to create two types of SUToken and transfer their ownership to the contract that initiated the creation.

- `transfer(address to, uint256 amount)`: This function is used for transfer processes. The function checks the balance of the owner to make sure there are enough credits and gives an option to have a debt if balance is insufficient and modifies the balance amount accordingly.

- `delagation_multiple_getback_all(address to)`: This function allows the user to take back all of the delegated tokens.

### Adding an Image to a DAO

The constructor of `newdao1.sol` file includes an `imageurl` variable. During the creation of a dao a line like the following can be used to pass the link address of an image to be used for the DAO.

```
MyDAO myDAO = new MyDAO("https://example.com/image.jpg");
```

## How to install and run the front-end:

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

This file includes a createToken function which is used to create new tokens and receive their addresses.

```
//create tokens, and transfer ownership to the factory
    function createToken(string memory yk_token_name, string memory yk_token_symbol, string memory voter_token_name, string memory voter_token_symbol ) external override returns (address, address){
        address my_factory = msg.sender;
        SUToken yk_token = new SUToken(yk_token_name, yk_token_symbol, my_factory);
        SUToken voter_token = new SUToken(voter_token_name, voter_token_symbol, my_factory);
        yk_token.transferOwnership(my_factory);
        voter_token.transferOwnership(my_factory);
        return (address(yk_token), address(voter_token));
    }
```

### Migrations.sol

This file contains the Migrations contract. The contract checks if the migration request is from the owner of the contract and if so completes the task.

```
contract Migrations {
  address public owner = msg.sender;
  uint public last_completed_migration;

  modifier restricted() {
    require(
      msg.sender == owner,
      "This function is restricted to the contract's owner"
    );
    _;
  }

  function setCompleted(uint completed) public restricted {
    last_completed_migration = completed;
  }
```

### newFactory1.sol

This file contains the DAOFactory contract. This contract creates the Top DAO, which is the DAO that supervises all of it’s sub-DAO’s and mints 1000 tokens to this factory. This function also inludes the creation and assignment of the YK tokens to the factory.

```
constructor(address myCreator) {
        my_creator = icreator(myCreator);
        is_a_dao_creator[msg.sender] = true;
        next_dao_id = 0;
    }

    //so first the dao and the token will be created, factory will have the tokens and dao will have the allowance to use
    //these tokens, the yk will be able to withdraw, send and deposit yk_tokens
    function createDAOTop( string memory dao_name,  string memory dao_description, string memory yk_token_name, string memory yk_token_symbol, string memory voter_token_name, string memory voter_token_symbol) public {
        require(is_a_dao_creator[msg.sender] == true, 'Not added as a DAO Creator');
        //line below mints 1000 tokens to this factory, also makes factory the owner of these tokens

        //ISUToken yk_token = new ISUToken(yk_token_name, yk_token_symbol, address(this));
        //ISUToken voter_token = new ISUToken(voter_token_name, voter_token_symbol, address(this));

        (yk_token_address, voter_token_address) = my_creator.createToken(yk_token_name,yk_token_symbol,  voter_token_name,  voter_token_symbol);

        ISUToken yk_token = ISUToken(yk_token_address);
        ISUToken voter_token = ISUToken(voter_token_address);
        MyDAO c = new MyDAO(dao_name, dao_description,next_dao_id, msg.sender, yk_token, voter_token, this);
        //token.mint(address(c), 1000*10**18);
        //with this function my dao can use the tokens my factory has however it wishes, it is currently only 1000, can add mint later
        yk_token.increaseAllowance(address(c), 1000* 10**18);
        yk_token.increaseAllowance(address(this), 1000* 10**18);
        voter_token.increaseAllowance(address(c), 1000* 10**18);
        voter_token.increaseAllowance(address(this), 1000* 10**18);
        yk_token.assignDAO( address(c));
        voter_token.assignDAO( address(c));
        //we save which dao has which yk tokens and which voter tokens

        dao_tokens_yk[c] = yk_token;
        dao_tokens_voter[c] = voter_token;
        dao_first_yk[c] = msg.sender;
        token_first_yk[yk_token] = msg.sender;
        top_dao = address(c);
        dao_exists[c] = true;

        //tokens are minted to factory right, we are sending them to dao so they can utilize it.
        voter_token.transferFrom(address(this), address(c), 1000 * 10**18);
        yk_token.transferFrom(address(this), address(c), (1000 * 10 ** 18));
        all_daos[next_dao_id] = c;



        next_dao_id += 1;
    }
```

The below function is used to create a childDAO under the Top DAO. It also checks if the requesting user has a YK token.

```
function createChildDAO( MyDAO parent, string memory dao_name,  string memory dao_description, string memory yk_token_name, string memory yk_token_symbol, string memory voter_token_name, string memory voter_token_symbol) public {
        require(parent.has_yk_priviliges(msg.sender) == true, 'Not a YK of parent DAO');
        //line below mints 1000 tokens to this factory, also makes factory the owner of these tokens


        (yk_token_address, voter_token_address) = my_creator.createToken(yk_token_name,yk_token_symbol,  voter_token_name,  voter_token_symbol);
        ISUToken yk_token = ISUToken(yk_token_address);
        ISUToken voter_token = ISUToken(voter_token_address);
        MyDAO c = new MyDAO(dao_name, dao_description,next_dao_id, msg.sender, yk_token, voter_token, this);
        //token.mint(address(c), 1000*10**18);
        //with this function my dao can use the tokens my factory has however it wishes, it is currently only 1000, can add mint later
        yk_token.increaseAllowance(address(c), 1000* 10**18);
        yk_token.increaseAllowance(address(this), 1000* 10**18);
        voter_token.increaseAllowance(address(c), 1000* 10**18);
        voter_token.increaseAllowance(address(this), 1000* 10**18);
        //check if assignDAO works with interface
        yk_token.assignDAO( address(c));
        voter_token.assignDAO( address(c));
        //we save which dao has which yk tokens and which voter tokens

        dao_tokens_yk[c] = yk_token;
        dao_tokens_voter[c] = voter_token;
        dao_first_yk[c] = msg.sender;
        token_first_yk[yk_token] = msg.sender;
        //top_dao = address(c);
        dao_exists[c] = true;
        parent_child_daos[parent].push(c);
        num_children[parent] += 1;
        child_parent[c] = parent;



        //tokens are minted to factory right, we are sending them to dao so they can utilize it.
        voter_token.transferFrom(address(this), address(c), 1000 * 10**18);
        yk_token.transferFrom(address(this), address(c), (1000 * 10 ** 18));




        all_daos[next_dao_id] = c;
        next_dao_id += 1;
    }
```

The below function is used to mint YK tokens for a DAO.

```
function mint_dao_yk(MyDAO to_be_minted, uint amount, address sender) public {
        require(to_be_minted.has_yk_priviliges(sender), "Not YK of selected DAO");
        //make sure the one who sends this is dao
        require(msg.sender == address(to_be_minted), "Don't even try");
        dao_tokens_yk[to_be_minted].mint(address(to_be_minted), amount*10**18);
    }
```

The below function is used to give creator privileges.

```
function addCreator(address input_creator) public
    {
        is_a_dao_creator[input_creator] = true;
    }
```

The below function is used to get the address of the parent DAO of a child DAO.

```
function getParentDAO(MyDAO mydao)  public view returns (MyDAO){
        return child_parent[mydao];

    }
```

The below function is used to get the address of the current DAO.

```
function getCurrentDAO(uint256 id)  public view returns (MyDAO){
        return all_daos[id];

    }
```

The below function is used to delete a DAO. The function checks if the request is sent by a YK member and if so continues with deletion and if not throws an error message. The function also checks if the DAO to be deleted has any child DAOs and if they exist deletes them as well.

```
function delete_DAO(MyDAO to_be_deleted, address sender) public {
        require(to_be_deleted.has_yk_priviliges(sender) || msg.sender == address(this) || msg.sender == address(to_be_deleted), "Not YK of selected DAO");
        //make sure the one who sends this is dao
        require(msg.sender == address(to_be_deleted), "Don't even try");


        num_children[child_parent[to_be_deleted]] -= 1;
        for (uint i = 0 ; i < parent_child_daos[to_be_deleted].length; i++){
            parent_child_daos[to_be_deleted][i].delete_this_dao();
        }
        for ( uint i = 0; i < parent_child_daos[child_parent[to_be_deleted]].length; i++){
            if( parent_child_daos[child_parent[to_be_deleted]][i]== to_be_deleted){
                //user_delegations[to][i];
                //string element = myArray[index];
                parent_child_daos[child_parent[to_be_deleted]][i] = parent_child_daos[child_parent[to_be_deleted]][parent_child_daos[child_parent[to_be_deleted]].length - 1];
                parent_child_daos[child_parent[to_be_deleted]].pop();


            }

        }
        dao_exists[to_be_deleted] = false;
        MyDAO c;
        all_daos[ to_be_deleted.getDaoid()] = c;
    }
```

### Token.sol

The below function is used for transfer processes. The function checks the balance of the owner to make sure there are enough credits and gives an option to have a debt if balance is insufficient and modifies the balance amount accordingly.

```
function transfer(address to, uint256 amount) public override (ERC20, ISUToken) returns (bool) {
        address owner = _msgSender();
        require(amount <= balanceOf(owner), "not enough in balance ");
        require(amount <= my_tokens[owner], "cant send debt tokens, you do not have enough tokens ");
        for (uint i = 0; i < proposal_num; i ++ ){
            if(transferLock[owner][i] == true){
                require(false , "have active voting");
            }
        }
        _transfer(owner, to, amount);
        my_tokens[owner] -= amount;
        debt_tokens[to] += amount;
        user_delegations_value[owner][to] += amount;
        user_delegations[owner].push(to);
        return true;
    }
```

The below function allows the user to take back all of the delegated tokens.

```
function delagation_multiple_getback_all(address to) public override returns (bool) {
        require(_msgSender() == myDAO || _msgSender() == address(this) , "sender is not dao or token");
        for (uint i = 0; i < user_delegations[to].length; i++) {
            address delegate = user_delegations[to][i];
            uint256 amount = user_delegations_value[to][delegate];
            _transfer(delegate, to, amount);
            debt_tokens[delegate] -= amount;
            my_tokens[to] += amount;
            user_delegations_value[to][delegate] = 0;
        }
        delete user_delegations[to];
        user_delegations[to] = new address[](0);
        return true;
    }
```

### Adding an Image to a DAO

The constructor of newdao1.sol file includes an imageurl variable. During the creation of a dao a line like the following can be used to pass the link address of an image to be used for the DAO.

```
MyDAO myDAO = new MyDAO("https://example.com/image.jpg");
```

# Smart_Contracts_Solidity

# "Looks like we've made our First Contract!"

![contract](https://camo.githubusercontent.com/5a761721f54f4ce9ca449875f486433faef2e95a1ef91ec2ef24002d268a0c01/68747470733a2f2f696d6167652e7368757474657273746f636b2e636f6d2f7a2f73746f636b2d70686f746f2d74776f2d68616e64732d68616e647368616b652d706f6c79676f6e616c2d6c6f772d706f6c792d6875642d696c6c757374726174696f6e2d736d6172742d636f6e74726163742d61677265656d656e742d626c6f636b636861696e2d616e642d313136313239353632372e6a7067)

## Background

A new startup has created its own Ethereum-compatible blockchain to help connect financial institutions, and the team wants to build smart contracts to automate some company finances to make everyone's lives easier, increase transparency, and to make accounting and auditing practically automatic!

This is a smart contract called `ProfitSplitter` contracts. These contracts will do the following:

* Pay your Associate-level employees quickly and easily.

* Distribute profits to different tiers of employees.

* Distribute company shares for employees in a "deferred equity incentive plan" automatically.

## Files

* AssociateProfitSplitter.sol -- Level 1 starter code.

* Starter-Code/TieredProfitSplitter.sol -- Level 2 starter code.

* Starter-Code/DeferredEquityPlan.sol -- Level 3 starter code.

## Instructions

* **Level One** is an `AssociateProfitSplitter` contract. This will accept Ether into the contract and divide the Ether evenly among the associate level employees. This will allow the Human Resources department to pay employees quickly and efficiently.

* **Level Two** is a `TieredProfitSplitter` that will distribute different percentages of incoming Ether to employees at different tiers/levels. For example, the CEO gets paid 60%, CTO 25%, and Bob gets 15%.

* **Level Three** is a `DeferredEquityPlan` that models traditional company stock plans. This contract will automatically manage 1000 shares with an annual distribution of 250 over 4 years for a single employee.

### Required interfaces
- Make sure that Remix IDE is running correctly
- Make sure that MetaMask is initialized and running correctly
- Make sure that Ganache is installed and is running correctly on 'localhost:8545'

### Level One: The `AssociateProfitSplitter` Contract

Define the following `public` variables:

* `employee_one` -- `address payable` of the first employee.

* `employee_two` -- Another `address payable` that represents the second employee.

* `employee_three` -- The third `address payable` that represents the third employee.

Create a constructor function that accepts:

* `address payable _one`

* `address payable _two`

* `address payable _three`

Within the constructor, set the employee addresses to equal the parameter values. This will allow you to avoid hardcoding the employee addresses.

Next, create the following functions:

* `balance` -- This function should be set to `public view returns(uint)`, and must return the contract's current balance. Since we should always be sending Ether to the beneficiaries, this function should always return `0`. If it does not, the `deposit` function is not handling the remainders properly and should be fixed. This will serve as a test function of sorts.

* `deposit` -- This function should set to `public payable` check, ensuring that only the owner can call the function.

  * In this function, perform the following steps:

    * Set a `uint amount` to equal `msg.value / 3;` in order to calculate the split value of the Ether.

    * Transfer the `amount` to `employee_one`.

    * Repeat the steps for `employee_two` and `employee_three`.

    * Since `uint` only contains positive whole numbers, and Solidity does not fully support float/decimals, we must deal with a potential remainder at the end of this function since `amount` will discard the remainder during division.

    * This may either have `1` or `2` wei leftover, so transfer the `msg.value - amount * 3` back to `msg.sender`. This will re-multiply the `amount` by 3, then subtract it from the `msg.value` to account for any leftover wei, and send it back to Human Resources.

* Create a fallback function using `function() external payable`, and call the `deposit` function from within it. This will ensure that the logic in `deposit` executes if Ether is sent directly to the contract. This is important to prevent Ether from being locked in the contract since we don't have a `withdraw` function in this use-case.

#### Test the contract
![AssociateProfitSplitter](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/Profitsplitter1.PNG)
![AssociateProfitSplitter](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/Profitsplitter2.PNG)
![AssociateProfitSplitter](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/Profitsplitter3.PNG)
![AssociateProfitSplitter](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/Profitsplitter4.PNG)
![AssociateProfitSplitter](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/Profitsplitter5.PNG)

### Level Two: The `TieredProfitSplitter` Contract

In this contract, rather than splitting the profits between Associate-level employees, you will calculate rudimentary percentages for different tiers of employees (CEO, CTO, and Bob).

Using the starter code, within the `deposit` function, perform the following:

* Calculate the number of points/units by dividing `msg.value` by `100`.

  * This will allow us to multiply the points with a number representing a percentage. For example, `points * 60` will output a number that is ~60% of the `msg.value`.

* The `uint amount` variable will be used to store the amount to send each employee temporarily. For each employee, set the `amount` to equal the number of `points` multiplied by the percentage (say, 60 for 60%).

* After calculating the `amount` for the first employee, add the `amount` to the `total` to keep a running total of how much of the `msg.value` we are distributing so far.

* Then, transfer the `amount` to `employee_one`. Repeat the steps for each employee, setting the `amount` to equal the `points` multiplied by their given percentage.

* For example, each transfer should look something like the following for each employee, until after transferring to the third employee:

  * Step 1: `amount = points * 60;`

    * For `employee_one`, distribute `points * 60`.

    * For `employee_two`, distribute `points * 25`.

    * For `employee_three`, distribute `points * 15`.

  * Step 2: `total += amount;`

  * Step 3: `employee_one.transfer(amount);`

* Send the remainder to the employee with the highest percentage by subtracting `total` from `msg.value`, and sending that to an employee.

* Deploy and test the contract functionality by depositing various Ether values (greater than 100 wei).

  * The provided `balance` function can be used as a test to see if the logic you have in the `deposit` function is valid. Since all of the Ether should be transferred to employees, this function should always return `0`, since the contract should never store Ether itself.

  * Note: The 100 wei threshold is due to the way we calculate the points. If we send less than 100 wei, for example, 80 wei, `points` would equal `0` because `80 / 100` equals `0` because the remainder is discarded. We will learn more advanced arbitrary precision division later in the course. In this case, we can disregard the threshold as 100 wei is a significantly smaller value than the Ether or Gwei units that are far more commonly used in the real world (most people aren't sending less than a penny's worth of Ether).

  #### Test the contract
  ![TieredProfitSplitter](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/Tieredprofitsplit1.PNG)
  ![TieredProfitSplitter](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/Tieredprofitsplit2.PNG)
  ![TieredProfitSplitter](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/Tieredprofitsplit3.PNG)
  ![TieredProfitSplitter](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/Tieredprofitsplit4.PNG)

### Level Three: The `DeferredEquityPlan` Contract

In this contract, we will be managing an employee's "deferred equity incentive plan" in which 1000 shares will be distributed over 4 years to the employee. We won't need to work with Ether in this contract, but we will be storing and setting amounts that represent the number of distributed shares the employee owns and enforcing the vetting periods automatically.

* **A two-minute primer on deferred equity incentive plans:** In this set-up, employees receive shares for joining and staying with the firm. They may receive, for example, an award of 1,000 shares when joining, but with a 4 year vesting period for these shares. This means that these shares would stay with the company, with only 250 shares (1,000/4) actually distributed to and owned by the employee each year. If the employee leaves within the first 4 years, he or she would forfeit ownership of any remaining (“unvested”) shares.

  * If, for example, the employee only sticks around for the first two years before moving on, the employee’s account will end up with 500 shares (250 shares * 2 years), with the remaining 500 shares staying with the company. In this above example, only half of the shares (and any distributions of company profit associated with them) actually “vested”, or became fully owned by the employee. The remaining half, which were still “deferred” or “unvested”, ended up fully owned by the company since the employee left midway through the incentive/vesting period.

  * Specific vesting periods, the dollar/crypto value of shares awarded, and the percentage equity stake (the percentage ownership of the company) all tend to vary according to the company, the specialized skills, or seniority of the employee, and the negotiating positions of the employee/company. If you receive an offer from a company offering equity (which is great!), just make sure you can clarify the current dollar value of those shares being offered (based on, perhaps, valuation implied by the most recent outside funding round). In other words, don’t be content with just receiving “X” number of shares without having a credible sense of what amount of dollars that “X” number represents. Be sure to understand your vesting schedule as well, particularly if you think you may not stick around for an extended period of time.

Using the starter code, perform the following:

* Human Resources will be set in the constructor as the `msg.sender`, since HR will be deploying the contract.

* Below the `employee` initialization variables at the top (after `bool active = true;`), set the total shares and annual distribution:

  * Create a `uint` called `total_shares` and set this to `1000`.

  * Create another `uint` called `annual_distribution` and set this to `250`. This equates to a 4 year vesting period for the `total_shares`, as `250` will be distributed per year. Since it is expensive to calculate this in Solidity, we can simply set these values manually. You can tweak them as you see fit, as long as you can divide `total_shares` by `annual_distribution` evenly.

* The `uint start_time = now;` line permanently stores the contract's start date. We'll use this to calculate the vested shares later. Below this variable, set the `unlock_time` to equal `now` plus `365 days`. We will increment each distribution period.

* The `uint public distributed_shares` will track how many vested shares the employee has claimed and was distributed. By default, this is `0`.

* In the `distribute` function:

  * Add the following `require` statements:

    * Require that `unlock_time` is less than or equal to `now`.

    * Require that `distributed_shares` is less than the `total_shares` the employee was set for.

    * Ensure to provide error messages in your `require` statements.

  * After the `require` statements, add `365 days` to the `unlock_time`. This will calculate next year's unlock time before distributing this year's shares. We want to perform all of our calculations like this before distributing the shares.

  * Next, set the new value for `distributed_shares` by calculating how many years have passed since `start_time` multiplied by `annual_distributions`. For example:

    * The `distributed_shares` is equal to `(now - start_time)` divided by `365 days`, multiplied by the annual distribution. If `now - start_time` is less than `365 days`, the output will be `0` since the remainder will be discarded. If it is something like `400` days, the output will equal `1`, meaning `distributed_shares` would equal `250`.

    * Make sure to include the parenthesis around `now - start_time` in your calculation to ensure that the order of operations is followed properly.

  * The final `if` statement provided checks that in case the employee does not cash out until 5+ years after the contract start, the contract does not reward more than the `total_shares` agreed upon in the contract.

* Deploy and test your contract locally.

  * For this contract, test the timelock functionality by adding a new variable called `uint fakenow = now;` as the first line of the contract, then replace every other instance of `now` with `fakenow`. Utilize the following `fastforward` function to manipulate `fakenow` during testing.

  * Add this function to "fast forward" time by 100 days when the contract is deployed (requires setting up `fakenow`):

    ```solidity
    function fastforward() public {
        fakenow += 100 days;
    }
    ```
  * Once you are satisfied with your contract's logic, revert the `fakenow` testing logic.
  
  #### Test the contract
  ![DeferredEquityPlan](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/DefferedEquity1.PNG)
  ![DeferredEquityPlan](https://github.com/Tijaw1/Smart_Contracts_Solidity/blob/main/Screenshots/deferred1.PNG)


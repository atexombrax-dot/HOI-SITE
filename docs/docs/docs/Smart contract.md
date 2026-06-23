#![no_std]

use soroban_sdk::{contract, contractimpl, Env, Address, Symbol, Map};

#[contract]
pub struct HOIToken;

#[contractimpl]
impl HOIToken {
    
    // Initialisation du token
    pub fn initialize(env: Env, admin: Address, initial_supply: i128) {
        let balance_key = Symbol::new(&env, "balance");
        let mut balances: Map<Address, i128> = Map::new(&env);

        balances.set(admin.clone(), initial_supply);

        env.storage().set(&balance_key, &balances);
    }

    // Lire le solde
    pub fn balance(env: Env, user: Address) -> i128 {
        let balance_key = Symbol::new(&env, "balance");
        let balances: Map<Address, i128> = env.storage().get(&balance_key).unwrap();

        balances.get(user).unwrap_or(0)
    }

    // Transfert de tokens
    pub fn transfer(env: Env, from: Address, to: Address, amount: i128) {
        let balance_key = Symbol::new(&env, "balance");
        let mut balances: Map<Address, i128> = env.storage().get(&balance_key).unwrap();

        let from_balance = balances.get(from.clone()).unwrap_or(0);
        let to_balance = balances.get(to.clone()).unwrap_or(0);

        assert!(from_balance >= amount, "Insufficient balance");

        balances.set(from.clone(), from_balance - amount);
        balances.set(to.clone(), to_balance + amount);

        env.storage().set(&balance_key, &balances);
    }
} 
Add HOÏ basic smart contract implementation.
Description: Initial smart contrat for the HOÏ token including initilization, balance management,and transfer functionality.#![no_std]

use soroban_sdk::{contract, contractimpl, Env, Address, Symbol, Map};

#[contract]
pub struct HOIToken;

#[contractimpl]
impl HOIToken {

    // =========================
    // INITIALISATION
    // =========================
    pub fn initialize(env: Env, admin: Address, initial_supply: i128) {
        let balance_key = Symbol::new(&env, "balances");
        let admin_key = Symbol::new(&env, "admin");
        let supply_key = Symbol::new(&env, "total_supply");

        let mut balances: Map<Address, i128> = Map::new(&env);

        balances.set(admin.clone(), initial_supply);

        env.storage().set(&balance_key, &balances);
        env.storage().set(&admin_key, &admin);
        env.storage().set(&supply_key, &initial_supply);
    }

    // =========================
    // TOTAL SUPPLY
    // =========================
    pub fn total_supply(env: Env) -> i128 {
        let supply_key = Symbol::new(&env, "total_supply");
        env.storage().get(&supply_key).unwrap()
    }

    // =========================
    // BALANCE
    // =========================
    pub fn balance(env: Env, user: Address) -> i128 {
        let balance_key = Symbol::new(&env, "balances");
        let balances: Map<Address, i128> = env.storage().get(&balance_key).unwrap();

        balances.get(user).unwrap_or(0)
    }

    // =========================
    // TRANSFER
    // =========================
    pub fn transfer(env: Env, from: Address, to: Address, amount: i128) {
        let balance_key = Symbol::new(&env, "balances");
        let mut balances: Map<Address, i128> = env.storage().get(&balance_key).unwrap();

        let from_balance = balances.get(from.clone()).unwrap_or(0);
        let to_balance = balances.get(to.clone()).unwrap_or(0);

        assert!(from_balance >= amount, "Insufficient balance");

        balances.set(from.clone(), from_balance - amount);
        balances.set(to.clone(), to_balance + amount);

        env.storage().set(&balance_key, &balances);
    }

    // =========================
    // MINT (admin only)
    // =========================
    pub fn mint(env: Env, admin: Address, to: Address, amount: i128) {
        let admin_key = Symbol::new(&env, "admin");
        let stored_admin: Address = env.storage().get(&admin_key).unwrap();

        assert!(admin == stored_admin, "Not authorized");

        let balance_key = Symbol::new(&env, "balances");
        let supply_key = Symbol::new(&env, "total_supply");

        let mut balances: Map<Address, i128> = env.storage().get(&balance_key).unwrap();
        let mut supply: i128 = env.storage().get(&supply_key).unwrap();

        let to_balance = balances.get(to.clone()).unwrap_or(0);

        balances.set(to.clone(), to_balance + amount);
        supply = supply + amount;

        env.storage().set(&balance_key, &balances);
        env.storage().set(&supply_key, &supply);
    }
}

update HOÏ smart contract removing burn and improving token logic.
Description:refactored HOÏ smart contract to remove burn burn functionality, while maintaining mint, transfer, balance tracking,and admin-controlled spply management.

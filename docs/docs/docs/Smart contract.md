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
Description: Initial smart contrat for the HOÏ token including initilization, balance management,and transfer functionality.

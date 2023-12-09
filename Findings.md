### [S-#] Storing the password on-chain makes it visible to anyone, and no longer private

**Description:** All data stored on-chsin is visible to anyone, and can be read directly from the blockchain. Ths `PasswordStore::s_password` variable is intended to be a private varibal and only accessed through the `PasswordStore::getPassword` function, which is intended to be only called by the owner of the contract.

We show one such method of reading any data off chain below.


**Impact:** Anyone can read the private password, severly breaking the functionality of the protocol.


**Proof of Concept:** (Proof of Code)

The below test case shows how anyone can read the password directly from the blockchain.

1. Create a locally running chain
```bash
make anvil
```

2. Deploy the contract to the chain

```bash
make anvil
```

3. Run the storage tool

We use `1` because that's the storage slot of `s_password` in the contract

```bash
cast storage <ADDRESS_HERE> 1 --rpc-url http://127.0.0.1:8545
```
You will get a bytes32 output.

You can then parse that hex to a string with:

```bash
cast --parse-bytes32-string 0x6d7950617373776f726400000000000000000000000000000000000000000014
```
And get an output of:

```
myPassword
```


**Recommeded Mitigation:** Due to this, the overall architecture of the contract should be rethought. One could encrypt the password off-chain, and then store the encrypted password on-chain. This would require the user to remember another password off-chain to decrypt the password. However, you'd also likely want to remove the view function as you wouldn't want the user to accidentally send a transaction with the password decrypts your password.



### [S-0] `PasswordStore::setPassword` has no access controls, meaning a non-owner could change the password

**Description:** The `PasswordStore::setPassword` function is set to be an `external` function, however, the natspec of the function and overall purpose of the smart contract is that `This function allows only owner to set a new password.`


**Impact:**


**Proof of Concept:**


**Recommeded Mitigation:**
Incrementing My Value
===

The final step in our Incrementer contract is to allow each user to update increment their own value.

## Modifying a HashMap

Making changes to the value of a HashMap is just as sensitive as getting the value. If you try to modify some value before it has been initialized, your contract will panic!

But have no fear, we can continue to use the `my_number_or_zero` function we created to protect us from these situations!

```rust
impl MyContract {

    /* --snip-- */

    // Set the value for the calling AccountId
    #[ink(message)]
    pub fn set_my_number(&mut self, value: u32) {
        let caller = self.env().caller();
        self.my_number_map.insert(caller, value);
    }

    // Add a value to the existing value for the calling AccountId
    #[ink(message)]
    pub fn add_my_number(&mut self, value: u32) {
        let caller = self.env().caller();
        let my_number = self.my_number_or_zero(&caller);
        self.my_number_map.insert(caller, my_number + value);
    }

    /// Returns the number for an AccountId or 0 if it is not set.
    fn my_number_or_zero(&self, of: &AccountId) -> u32 {
        *self.my_number_map.get(of).unwrap_or(&0)
    }
}
```

Here we have written two kinds of functions which modify a HashMap. One which simply inserts the value directly into storage, with no need to read the value first, and the other which modifies the existing value. Note how we can always `insert` the value without worry, as that initialized the value in storage, but before you can get or modify anything, we need to call `my_number_or_zero` to make sure we are working with a real value.

## Feel the Pain (Optional)

We will not always have an existing value on our contract's storage. We can take advantage of the Rust `Option<T>` type to help use on this task.
If there's no value on the contract storage we will insert a new one; on the contrary if there is an existing value we will only update it.

ink! HashMaps expose the well-known `entry` API that we can use to achieve this type of "upsert" behavior:

```rust
let caller = self.env().caller();
self.my_number_map
    .entry(caller)
    .and_modify(|old_value| *old_value += by)
    .or_insert(by);
```


## Your Turn!

Follow the `ACTION`s to finish your Incrementer smart contract.

Remember to run `cargo +nightly test` to test your work.

<!-- tabs:start -->

#### ** Template **

[embedded-code](./assets/1.6-template.rs ':include :type=code embed-template')

#### ** Solution **

[embedded-code-final](./assets/1.6-finished-code.rs ':include :type=code embed-final')

#### ** Previous Solution **

[embedded-code-previous](./assets/1.5-finished-code.rs ':include :type=code embed-previous')

<!-- tabs:end -->
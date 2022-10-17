# VRF Lottery - Exploring Fe

This file explains the exploration of Fe by attempting to implement a VRF Lottery in Fe. Currently, this code compiles and can be deployed; however, some function calls fail. Since this language is very new and still contains bugs, this problem is still being explored.

## Findings

While exploring Fe when creating this contract, there were a few main differences and pain points that are worth highlighting.

### 1 . Interfaces & Inheritance

Inheritance does not exist yet in Fe. Therefore, as a workaround, any functions that required need to be included directly in the contract.

Interfaces also do not exist so as a workaround we use contracts with a dummy implementation e.g. `return 0` or `return false`.

### 2. Constant Variables

Constant variables do not yet exist in Fe. Therefore, to set variables in storage, we need to initialize them within the initializer. In Fe, rather than having a constructor as in Solidity, we have a function called `__init__`.

### 3. Self & Context

- In Fe, the `Context` parameter needs to be stated explicitly when accessing the context of a call such as `ctx.msg_sender()` or `ctx.msg_data()`.
- When accessing functions or storage variables in a contract the `self` keyword needs to be used.

### 4. Dynamic Types not Supported

Fe does not allow dynamic types such as bytes, dynamic sized arrays, or dynamic sized strings. This proved to be difficult to navigate as all the array and string sizes had to be pre calculated and bytes data had to be initialized as an array: `Array<u8, <number_of_bytes>>`.
This also meant that data needed to be kept track of in seperate variables to query mappings or find the number of lotteries/players, as e.g. `array.length` could not be used to work out the number of participants. Instead a counter variable had to be used to track the number of participants.

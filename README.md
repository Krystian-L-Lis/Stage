# Stage - A TwinCAT 3.1 Framework for Streamlined PLC Development
**Stage** is a modular framework designed for managing complex PLC systems using state machines, interfaces, and tag-based communication. It emphasizes composition over inheritance, allowing function blocks to implement various behaviors by combining interfaces like `I_Execute`, `I_Call`, and `I_Domain`. Stage uses tags and signals for flexible, decoupled interaction between components, and leverages a simplified `Result` type for consistent error handling. The framework's design promotes clear state transitions, and modular development.

## Libraries Overview

### 1. Debug
The **Debug** library facilitates debugging during development. It uses the `I_Debug` interface as an identification label within the IDE. Internally, it stores a table of debug messages. 

Provides:
+ `I_Debug` - An identification interface.
+ `Param.nMaxDebugArraySize` - Used to change the maximum size of the debug array through library parameters.
+ `DebugLen` - Returns the number of registered debug messages.
+ `DebugMsg` - Registers a new debug message.
+ `DebugList` - Stores the reference to the debug array as a private field.

### 2. Parameters
The **Parameters** library acts as a wrapper around persistent variables. It provides wrappers for `BOOL`, `TIME`, `UDINT`, `DINT`, `REAL`, and `LREAL`.

### 3. Strings
The **Strings** library includes a string builder and formatter.

Provides:
+ `Ch` - Alias for `STRING(1)`.
+ `Str` - Alias for `STRING(255)`.
+ `Strb` - String builder function block. Allows chaining.
+ `Strf` - Basic formatting functions such as padding.

### 4. Tags
The **Tags** library provides an object identification mechanism used throughout the framework. It implements an ownership model, where each tag has exactly one owner at any time, allowing for O(1) access when requested from an owner collection.

Provides:
+ `Tag` - Internally calls reflection and removes self from the path. It provides methods for identification (`GetId`, `Option`), index caching (`Cache`), and ownership management (`Join`, `Leave`).
+ `I_Tag` - Interface implemented by Tag.
+ `TagCmp` - Compares tags based on their interface pointer, ID, partial ID (from the right side of an ID string), and boolean conditions. Allows chaining.
+ `TagOwner` - Used by collections and other parent structures to identify their children. Ownership is transferable.
+ `A_Tag` - Assigns an interface value to an interface variable during `FB_Init`.
+ `I_Tagged` - This interface indicates that an FB has a Tag.
+ `TagBuffer` - Ring buffer of I_Tag interfaces.

### 5. Results
The **Results** library offers a simple and intuitive result type, which is a wrapper around an `INT`. In this system:
- Negative values indicate errors (Base: `Error := -1`),
- Positive values indicate successes (Base: `Ok := 1`),
- Zero represents no result (Base: `None := 0`).

It provides the following functions:
- `IsErr(ResultInput, ResultOutput) : BOOL` - Checks if the result is an error.
- `IsOk(ResultInput, ResultOutput) : BOOL` - Checks if the result is a success.
- `IsSome(ResultInput, ResultOutput) : BOOL` - Checks if the result is either an error or a success.
- `IsNone(ResultInput, ResultOutput) : BOOL` - Checks if the result is none (zero).
- `IsNull(Interface: ANY) : BOOL` - Checks if the interface is null.
- `IsNotNull(Interface: ANY) : BOOL` - Checks if the interface is not null.

### 6. Broadcast
The **Broadcast** library is an implementation of the publish-subscribe pattern. It uses a dynamic (but not dynamically allocated) signal table internally to notify receivers when a signal is emitted. The library provides base collection types for signals and receivers, streamlining event-driven programming.

> **Warning:** The table will create a debug message upon overflow!

Provides:
+ `Param.nChannelSize` - Size of an array storing Signal-Receiver pairs.
+ `Signal` - Can be paired with `Receiver`. Emits an internal tag by default, but can also emit external tags.
+ `Receiver` - Can be paired with `Signal`. Takes `I_Call` upon `FB_Init`. Receives the tag and calls the associated `I_Call`.
+ `I_Call` - Called by `Receiver`. Any FB can implement this interface.
+ `SignalList` - List containing `I_Signal`.
+ `I_SignalList` and `I_MutSignalList` - Immutable and mutable interfaces implemented by `SignalList`.
+ `I_Signal` - An interface used for pairing.
+ `ReceiverList` - List containing `I_Receiver`.
+ `I_ReceiverList` and `I_MutReceiverList` - Immutable and mutable interfaces implemented by `ReceiverList`.
+ `I_Receiver` - An interface used for pairing.
+ `A_Signal` and `A_Receiver` - Automatic insertion mechanism through `FB_Init`.
+ `FindSignal`, `FindRec` - Function used to find Signals and Receivers within their corresponding lists.
+ `FindManySignals`, `FindManyRecs` - Function used to find multiple Signals and Receivers within their corresponding lists. This is an experimental feature.

> **Warning:** Lists will create a debug message upon overflow!

> **Warning:** Immutable interfaces may still lose ownership through the `Release` method during ownership transfer!

### 7. Flags
The **Flags** library is a wrapper around tagged `BOOL` values, allowing for quick identification of the state of a given object.

Provides:
+ `FlagList` - List containing `I_Flag`.
+ `I_FlagList` and `I_MutFlagList` - Immutable and mutable interfaces implemented by `FlagList`.
+ `I_Flag` - An interface used for checking the state of a flag.
+ `A_Flag` - Automatic insertion mechanism through `FB_Init`.
+ `FindFlag` - Function used to find Flag within list.
+ `FindManyFlags` - Function used to find multiple Flags within list. This is an experimental feature.

> **Warning:** Lists will create a debug message upon overflow!

> **Warning:** Immutable interfaces may still lose ownership through the `Release` method during ownership transfer!

### 8. States
The **States** library is an implementation of the state and strategy patterns. It provides state buffers, repositories, and state machines, along with an abstract state class that includes base functionality.

Provides:
+ `State` - Abstract FB containing the basic implementation of the state pattern. It has the following methods: `OnInit`, `OnExit`, `OnExecute`, `CanActivate`, `CanDeactivate`. Implements the `I_Tagged` and `I_State` interfaces. Requires `StateRepo`.
+ `I_State` - State interface.
+ `StateRepo` - A collection of `I_State` interfaces. Has permanent ownership over them.
+ `I_StateRepo` - Interface implemented by `StateRepo`.
+ `StateBuffer` - A ring buffer of `I_State` interfaces. Has no ownership over its content.
+ `I_StateBuffer` - Interface implemented by `StateBuffer`.
+ `StateMachine` - FB responsible for swapping active states and executing their behavior.
+ `I_StateMachine` - Interface implemented by `StateMachine`.
+ `StateManager` - Bundles `StateRepo`, `StateBuffer`, and `StateMachine` into one flow. Emits a signal on state change.
+ `I_StateManager` - Interface implemented by `StateManager`.
+ `FindState` - Function used to find state within state manager.
+ `FindManyStates` - Function used to find multiple states within state manager. This is an experimental feature.

### 9. Rt
The **Rt** library provides runtime management, ensuring that objects implementing the `I_Execute` interface are automatically called through the runtime.

Provides:
+ `Execute` - Delegate FB responsible for executing FBs implementing `I_Execute`.
+ `Runtime` - A function that calls all initialized `Execute` FBs.

### 10. Domain
The **Domain** library is a collection of tagged flags, signals, receivers, and a state manager under a common interface. It allows for browsing through domains using the tagging system.

Provides:
+ `Domain` - Node within the global linked list of all nodes.
+ `I_Domain` - An FB identifier. Domain FBs contain `FlagList`, `SignalList`, `ReceiverList`, `StateManager`, and a `Tag`.
+ `DomainIter` - Allows browsing through the global domain list.
+ `DomainBuffer` - Ring buffer of I_Domain interfaces.

### 10. Utils
Functionality that doesn't have their own separate library yet.

Provides:
+ `GenIter` - A simple iterator over UDINT values. Used for iterating over collections in `WHILE` loops.
+ `UDINT_Array` - A fixed array of UDINTs with `Pop`, `Push`, `Get`, `Set`, `Has` methods. It uses generic for its size.
+ `Color` - RGB color.
+ `I_Color` - `Color` Interface.
+ `GetSystemTime()` - Wrapper over `Tc2_System.F_GetSystemTime()` that shifts the result value to 1970-01-01T00:00:00Z.
+ `Timer` - Universal timer FB with Keep and Pause functionality.

## Example
The repository contains an example project within the solution. The project showcases a scenario where a main tank fills several secondary tanks, and each secondary tank can be drained individually through separate outlets. This simple scenario provides basic application of Stage framework. The example project uses Twincat3 PLC HMI web(TF1810).

## Contributing

Contributions to the Stage framework are welcome! Feel free to fork the repository and submit pull requests. For major changes, please open an issue first to discuss what you would like to change. The framework is in its early *stage* and requires extensive testing!

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.







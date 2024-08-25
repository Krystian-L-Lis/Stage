# Stage - A TwinCAT 3.1 Framework for Streamlined PLC Development

**Stage** is a framework for TwinCAT 3.1, designed to simplify and enhance the development of PLC projects. The framework consists of multiple libraries.

## Libraries Overview

### 1. Debug
The **Debug** library facilitates debugging during development. It uses the `I_Debug` interface as an identification label within the IDE. Internally it stores a table of debug messages.

### 2. Parameters
The **Parameters** library acts as a wrapper around persistent variables.

### 3. Strings
The **Strings** library includes a string builder and formatter.

### 4. Tags
The **Tags** library provides an object identification mechanism used throughout the framework. It implements an ownership model, where each tag has exactly one owner at any time, allowing for O(1) access when requested from an owner collection.

### 5. Result
The **Result** library offers a simple and intuitive result type, which is a wrapper around an `INT`. In this system:
- Negative values indicate errors(Base: `Error := -1`),
- Positive values indicate successes(Base: `Ok := 1`),
- Zero represents no result (Base: `None := 0`).

It also includes helper functions for quick result identification and a set of basic error types.

### 6. Broadcast
The **Broadcast** library is an implementation of the publish-subscribe pattern. It uses a dynamic (but not dynamically allocated) signal table internally to notify receivers when a signal is emitted. The library provides base collection types for signals and receivers, streamlining event-driven programming.

### 7. Flags
The **Flags** library is a wrapper around tagged boolean values, allowing for quick identification of the state of a given object.

### 8. States
The **States** library is an implementation of the state and strategy patterns. It provides state buffers, repositories, and state machines, along with an abstract state class that includes base functionality.

### 9. Rt
The **Rt** library provides runtime management, ensuring that objects implementing the `I_Execute` interface are automatically called through the runtime.

### 10. Domain
The **Domain** library is a collection of tagged flags, signals, receivers, and a state manager under a common interface. It allows for browsing through domains using the tagging system.

## Packages

The solution provides 2 packages. One contains Stage libraries that require qualified access(x.x.x.1) and the other do not(x.x.x.0).

## Contributing

Contributions to the Stage framework are welcome! Feel free to fork the repository and submit pull requests. For major changes, please open an issue first to discuss what you would like to change. The framework is in its early *stage* and requires testing! 

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

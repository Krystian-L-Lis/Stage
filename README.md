# **Stage**  
_A TwinCAT 3.1 Framework for Streamlined PLC Development_

---

## Overview

Stage is a framework designed to simplify PLC programming in TwinCAT 3.1. It promotes a modular approach to developing scalable and maintainable automation software through optional features such as composition-based architecture, event handling, and state management. Stage integrates seamlessly into both simple and complex control applications, offering a structured and flexible development approach without unnecessary complexity.

---

## **Requirements**

Stage requires **Beckhoff TwinCAT 3.1**, specifically:

- **Minimum TwinCAT version:** TwinCAT 3.1 - Build 4026
- For TwinCAT system requirements, visit the official [Beckhoff TwinCAT System Requirements](https://infosys.beckhoff.com/english.php?content=../content/1033/tc3_overview/6162419083.html&id=)

---

## **Installation**

1. **Clone the repository:**  
   ```sh
   git clone https://github.com/Krystian-L-Lis/Stage.git
   ```

2. **Import the library into TwinCAT:**

   ### **Option 1: Build from source**

   1. Open your TwinCAT solution.
   2. Locate the **Core PLC project**, expand it by clicking the arrow next to it.
   3. **Right-click** on the Core PLC project and select **"Save as Library and Install"** to add it to the TwinCAT library repository.
   4. **Right-click** on **References**, select **"Add Library"**, and locate the `Core` and/or `Utils` under `Stage` category to include it in your project.
   5. For more detailed instructions on TwinCAT library installation, refer to the official Beckhoff guide on [Infosys](https://infosys.beckhoff.com/english.php?content=../content/1033/tc3_plc_intro/41891384434359666059.html&id=).

   ### **Option 2: Use precompiled files**

   1. Download the compiled files(`Core.library` and/or `Utils.library`) from the [release page](https://github.com/Krystian-L-Lis/Stage/releases).
   2. Open your TwinCAT PLC project and navigate to **References**, then **right-click** and select **"Library Repository" > "Install"**.  
      Choose the downloaded `.library` file to add Stage to the repository.
   3. After installation, **right-click** on **References**, select **"Add Library"**, and locate the `Core` and/or `Utils` under `Stage` category to include it in your project.
   4. For more detailed instructions on TwinCAT library installation, refer to the official Beckhoff guide on [Infosys](https://infosys.beckhoff.com/english.php?content=../content/1033/tc3_plc_intro/41891384434359666059.html&id=).

---

## **Wiki**

Check the [Wiki](https://github.com/Krystian-L-Lis/Stage/wiki) page for:  
✔️ Detailed guides  
✔️ Example projects  
✔️ API reference  
✔️ Changelog  

---

## **Features**

- 📦 **Modular Architecture** – Compose function blocks with ease.  
- 🔄 **State Management** – Implement flexible state-driven logic.  
- 📡 **Event-Driven Programming** – Easily connect components via signals.  
- 🧩 **Plug & Play Components** – Quick integration with existing TwinCAT projects.  
- 🛠️ **Developer-Friendly** – Clean, readable code with standardized conventions.  

---

## **Quick Start**

This guide provides a brief overview of using the **Stage** library.

---

### **Define a Simple Entity**

```structured-text
FUNCTION_BLOCK Pump IMPLEMENTS I_Callable, I_Execute, I_Init
VAR_INPUT
	bOverload				: BOOL;
END_VAR
VAR_OUTPUT
	bRunning				: BOOL;
END_VAR
VAR
	// Execute on each iteration through the I_Execute.Execute method
	_exe					: Execute(THIS^);

	// Initialize during the first iteration through the I_Init.Init method
	_init					: Init(THIS^);  

	// Create an Entity within this function block
	_this					: Entity; 

	// Create a human-readable identifier and add it to the composition
	_tag					: Tag('Pump');
	_compTag				: Comp(_tag, _this);

	// Create an event emitter and add the signal to the composition
	_stateChangeEvent		: Signal; 
	_compStateChangeEvent	: Comp(_stateChangeEvent, _this);

	// Create an event receiver and add it to the composition
	// Events are received through the I_Callable.Call method
	_onCommand				: Receiver(THIS^); 
	_compOnCommand			: Comp(_onCommand, _this);
END_VAR

	METHOD Init; END_METHOD		// Implemented from I_Init
	METHOD Execute; END_METHOD	// Implemented from I_Execute
	METHOD Call;				// Implemented from I_Callable
		VAR_INPUT
			iArg		: I_Arg;
		END_VAR
	END_METHOD

END_FUNCTION_BLOCK
```

---

### **Define States**

```structured-text
// Create a context structure or interface
TYPE Tank_Ctx:  
STRUCT  
	rSomeSensor				: LREAL;
	iPumpEventCache			: I_Signal;
	iPumpCommandCache		: I_Receiver;
	sHmiStatusText			: Str;
	nHmiStatusColor			: Color;
END_STRUCT  
END_TYPE

// Create an abstract state definition for a given context
FUNCTION_BLOCK ABSTRACT _Tank_State EXTENDS State
VAR
	_ctx	: REFERENCE TO Tank_Ctx;
END_VAR
METHOD FB_Init
VAR_INPUT
	ctx		: REFERENCE TO Tank_Ctx;
END_VAR
	_ctx REF= ctx;
END_METHOD
END_FUNCTION_BLOCK

// Create a concrete state definition
FUNCTION_BLOCK Tank_State_Empty EXTENDS _Tank_State
METHOD OnEntry; END_METHOD 								// Override...
METHOD OnExecute; END_METHOD 							// any or all...
METHOD OnExit; END_METHOD 								// of these...
METHOD CanActivate; CanActivate := TRUE; END_METHOD 	// methods.
METHOD CanDeactivate; CanDeactivate := TRUE; END_METHOD
END_FUNCTION_BLOCK
```

---

### **Define an Entity with a State Manager**

```structured-text
// Create a concrete state definition
FUNCTION_BLOCK Tank_State_Full EXTENDS _Tank_State
END_FUNCTION_BLOCK

// Create a state machine entity definition
FUNCTION_BLOCK Tank EXTENDS State
VAR
	_ctx		: Tank_Ctx;
	_this		: Entity;

	// Create a StateManager which wraps around the StateMachine
	// Apart from managing the state lifecycle, it also handles queues and change requests
	_smgr		: StateManager<2>(_this);

	_empty		: Tank_State_Empty(_this, _ctx);
	_compEmpty	: Comp(_empty, _this);

	_full		: Tank_State_Full(_this, _ctx);
	_compFull	: Comp(_full, _this);
END_VAR
END_FUNCTION_BLOCK
```

---

### **Browse the Entities Through the Registry**

#### **Simple Method**

```structured-text
// Browse entities 
WHILE IsOk(GetEntity(i.Next(), iEntity => _iEntity)) DO
	// Use a helper function to browse and extract
	IF IsOk(GetTag(iEntity, 'Pump', iTag => _iPumpTag)) THEN
		EXIT;
	END_IF
END_WHILE
```

---

#### **Verbose Method**

```structured-text
// Alternatively, browse and cast components manually
// This allows for multiple searches in one iteration
WHILE IsOk(_iEntity.Get(j.Next(), iComp => _iComp)) DO
	IF IsOk(ICompToISignal(_iComp, iSignal => _iSignal)) THEN
		_ctx.iPumpEventCache := _iSignal;
	ELSIF IsOk(ICompToIRec(_iComp, iRec => _iReceiver)) THEN
		_ctx.iPumpCommandCache := _iReceiver;
	END_IF
	
	// Exit early if both components are found
	IF _ctx.iPumpEventCache <> 0 AND _ctx.iPumpCommandCache <> 0 THEN
		EXIT;
	END_IF
END_WHILE
```

---

### **Emit Signals**

```structured-text
VAR
	// Declare an emittable argument
	// Remember: arguments declared in a temporary scope
	// should not be cached or saved, as this will cause an exception!
	_bIsRunning		: BoolArg(TRUE);
END_VAR

// Emit a signal
_stateChangeEvent.Emit(_bIsRunning);

// You should not use _bIsRunning after RETURN
RETURN;
```

---

## **Contributing**

Contributions to the Stage framework are welcome!  
Feel free to fork the repository and submit pull requests.  

- **Fork the repository** and create a feature branch.  
- **Submit pull requests** for review.  
- **Discuss your ideas** by opening an issue before making major changes.  

This framework is in its early *stage* and requires extensive testing!

---

## **License**

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## **Contact & Support**

If you have questions or suggestions, feel free to reach out via:

- **GitHub Issues:** [Submit an issue](https://github.com/Krystian-L-Lis/Stage/issues)  

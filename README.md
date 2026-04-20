# **Stage**
_A framework for writing scalable and maintainable ST code_

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/Krystian-L-Lis/Stage)

---

## Overview

Stage is a framework designed to simplify PLC programming in TwinCAT 3.1. It does not provide pre-made function blocks for analogue inputs or motion components as these you will need to write yourself. It provides scaffolding that allows you to build highly extensible features through automatic registration, discovery and execution.

---

## **Wiki**

Check the [Wiki](https://github.com/Krystian-L-Lis/Stage/wiki) page!  
- Guides  
- API reference  
- Changelog
- Obsidian-compatible

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

2. **Import the framework into TwinCAT:**

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

## **Features**

- Automatic dependency injection - register your function block for others to discover at runtime.
- Automatic execution - register function blocks and execute them on an assigned thread.
- Workers - define jobs, start them, and execute them until completion on an assigned thread.
- Utilities - result helpers, string builders, path identifiers, time helpers, math helpers, and color builders.

---

## **Quick Start**

This guide shows the normal Stage wiring pattern: call `Plant.Run()` once from the main PLC program, register cyclic function blocks with `Execute`, register discoverable function blocks with `Entity`, and register startable procedures with `Worker`.

### **Register A Cyclic Function Block**

```iecst
FUNCTION_BLOCK SystemTime IMPLEMENTS I_Execute, I_SystemTime
VAR
    _exe: Execute(THIS^);
    _ett: Entity(THIS^);
    nUnixTime: ULINT;
END_VAR
```

```iecst
METHOD Execute
nUnixTime := GetUnixTime();
```

`Execute(THIS^)` registers the function block for cyclic execution. `Entity(THIS^)` makes it discoverable through interfaces that extend `I_Generic`.

### **Run Stage From MAIN**

```iecst
PROGRAM MAIN
VAR
    SystemTime: SystemTime;
    Safety: Safety;
END_VAR

Plant.Run();
```

### **Discover Registered Entities**

```iecst
VAR
    iEtt: I_Entity;
    iSystemTime: I_SystemTime;
END_VAR

WHILE IsOk(Plant.NextEtt(iEtt)) DO
    IF __QUERYINTERFACE(iEtt.Raw, iSystemTime) THEN
        // Use iSystemTime here.
    END_IF
END_WHILE
```

### **Create A Startable Procedure**

```iecst
FUNCTION_BLOCK FindBelt IMPLEMENTS I_Job, I_Procedure
VAR
    _worker: Worker(THIS^);
    _ett: Entity(THIS^);
END_VAR
```

```iecst
METHOD JobInit
// Reset procedure state.
```

```iecst
METHOD JobExecute
// Run one step per worker cycle.
IF bComplete THEN
    _worker.Stop();
END_IF
```

```iecst
METHOD JobExit
// Publish final state and clear command bits.
```

Start the procedure from another function block:

```iecst
_result := FindBelt.Start();

IF IsOk(_result) THEN
    // The job was accepted by its thread.
END_IF
```

### **Use A Fast Task Thread**

```iecst
{attribute 'global_init_slot' := '49000'}
PROGRAM FAST
VAR
    Thread: Thread;
END_VAR

Thread.Run();
```

Assign cyclic work to the fast task during initialization:

```iecst
FUNCTION_BLOCK LoadCell IMPLEMENTS I_Execute, I_LoadCell
VAR
    _exe: Execute(THIS^) := (Thread := FAST.Thread);
    _ett: Entity(THIS^);
END_VAR
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

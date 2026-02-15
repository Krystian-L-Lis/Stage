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

- Automatic dependency injection – register your function block for others to discover at runtime.
- Automatic execution – register function blocks and execute them on an assigned thread.
- Workers – define jobs, start them, and execute them until completion on an assigned thread.
- Utilities – string and color builders, and more.

---
## **Tests & Examples**

Tests and Examples can be found here. WIP.

---

## **Quick Start**

This guide provides a brief overview of using the **Stage** framework.

```
   WIP
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


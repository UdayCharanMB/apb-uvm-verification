# apb-uvm-verification
A UVM-based verification environment for the AMBA APB protocol, featuring a reusable agent, memory-mirroring scoreboard, and functional coverage.

# UVM-Based Verification of APB Protocol

## 📌 Project Overview

This project implements a **UVM-based verification environment for the AMBA APB (Advanced Peripheral Bus) protocol** using **SystemVerilog and UVM**.

The main objective is to develop a **reusable, modular, and self-checking verification environment** that generates APB transactions, drives them to the DUT, monitors the interface activity, checks the results using a scoreboard, and measures functional coverage.

APB is a simple, low-bandwidth bus protocol commonly used for accessing peripheral registers such as UART, GPIO, timers, and other control/status peripherals.

---

## 🎯 Project Objectives

* Understand the AMBA APB protocol and its transfer phases.
* Develop a reusable UVM verification environment.
* Verify APB **read and write transactions**.
* Generate constrained-random APB transactions.
* Verify APB protocol timing and signal behavior.
* Implement a self-checking scoreboard.
* Add functional coverage to measure verification completeness.
* Use SystemVerilog Assertions for protocol checking.
* Analyze simulation waveforms and debug failures.

---

## 🔌 APB Protocol

The APB transfer consists mainly of two phases:

### 1. Setup Phase

During the setup phase:

```text
PSEL    = 1
PENABLE = 0
```

The address, direction, and write data are established.

### 2. Access Phase

During the access phase:

```text
PSEL    = 1
PENABLE = 1
```

The transfer completes when the slave asserts `PREADY`.

Typical APB signals include:

| Signal    | Description               |
| --------- | ------------------------- |
| `PCLK`    | APB clock                 |
| `PRESETn` | Active-low reset          |
| `PSEL`    | Peripheral select         |
| `PENABLE` | Access phase indicator    |
| `PWRITE`  | Read/Write control        |
| `PADDR`   | Address                   |
| `PWDATA`  | Write data                |
| `PRDATA`  | Read data                 |
| `PREADY`  | Transfer completion       |
| `PSLVERR` | Transfer error indication |

APB transfers require at least a setup phase followed by an access phase. Wait states can extend the access phase using `PREADY`.

---

## 🏗️ UVM Testbench Architecture

```text
                    +----------------------+
                    |      UVM TEST        |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |      APB SEQUENCE    |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |      APB SEQUENCER   |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |       APB DRIVER      |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |         APB DUT       |
                    +----------+-----------+
                               |
                 +-------------+-------------+
                 |                           |
                 v                           v
        +----------------+          +----------------+
        |  APB MONITOR   |          | APB MONITOR    |
        +-------+--------+          +-------+--------+
                |                           |
                +-------------+-------------+
                              |
                              v
                    +----------------------+
                    |      SCOREBOARD      |
                    +----------------------+

                    +----------------------+
                    | FUNCTIONAL COVERAGE  |
                    +----------------------+

                    +----------------------+
                    |   SVA / PROTOCOL     |
                    |      CHECKERS        |
                    +----------------------+
```

---

## 🧩 UVM Components

### APB Transaction

Represents a single APB read or write transaction.

Typical fields include:

```text
address
write_data
read_data
write_enable
slave_error
ready
```

The transaction can be randomized using SystemVerilog constraints.

---

### APB Sequence

Generates APB stimulus for different scenarios such as:

* Write transactions
* Read transactions
* Consecutive writes
* Consecutive reads
* Read-after-write
* Random address transactions
* Random data transactions
* Error transactions
* Wait-state transactions

---

### APB Sequencer

Transfers randomized transaction objects from the sequence to the driver.

---

### APB Driver

Converts transaction-level information into pin-level APB signals.

The driver controls signals such as:

```text
PSEL
PENABLE
PWRITE
PADDR
PWDATA
```

and responds to signals such as:

```text
PREADY
PRDATA
PSLVERR
```

---

### APB Monitor

Observes APB interface activity and converts signal-level information back into transaction-level objects.

The monitor sends observed transactions to:

* Scoreboard
* Functional Coverage
* Other analysis components

---

### Scoreboard

The scoreboard compares the expected transaction with the actual DUT response.

```text
Expected Transaction
        |
        v
   +----------+
   | Scoreboard|
   +----------+
        ^
        |
Actual Transaction
```

It helps detect:

* Incorrect read data
* Incorrect write behavior
* Address mismatches
* Protocol-related transaction failures

---

### Functional Coverage

Coverage is used to determine whether important APB scenarios have been exercised.

Example coverage points:

* Read / Write
* Different addresses
* Different data values
* Successful transfers
* Error transfers
* Wait-state transfers
* Consecutive transactions

---

### SystemVerilog Assertions

Assertions are used to detect APB protocol violations.

Examples include checking:

* `PENABLE` is asserted only after `PSEL`.
* Control signals remain stable during required phases.
* `PSEL` remains asserted until the transfer completes.
* Access phase follows the setup phase.
* Reset behavior is correct.

---

# 🧪 Verification Test Plan

| Test             | Description                              | Status |
| ---------------- | ---------------------------------------- | ------ |
| Reset Test       | Verify DUT reset behavior                | ⬜      |
| APB Write Test   | Verify basic write transfer              | ⬜      |
| APB Read Test    | Verify basic read transfer               | ⬜      |
| Read After Write | Write and read back data                 | ⬜      |
| Multiple Write   | Verify consecutive writes                | ⬜      |
| Multiple Read    | Verify consecutive reads                 | ⬜      |
| Random Test      | Generate constrained-random transactions | ⬜      |
| Wait-State Test  | Verify `PREADY` based wait states        | ⬜      |
| Error Test       | Verify `PSLVERR` behavior                | ⬜      |
| Protocol Test    | Verify APB timing and protocol rules     | ⬜      |
| Coverage Test    | Measure functional coverage              | ⬜      |

> Update the status column after implementing each test.

---

# 📂 Project Directory Structure

```text
UVM-Based-Verification-of-APB-Protocol/
│
├── rtl/
│   └── apb_dut.s
│
├── interface/
│   └── apb_interface.sv
│
├── sequence_item/
│   └── apb_transaction.sv
│
├── sequences/
│   ├── apb_base_sequence.sv
│   ├── apb_write_sequence.sv
│   ├── apb_read_sequence.sv
│   └── apb_random_sequence.sv
│
├── agent/
│   ├── apb_driver.sv
│   ├── apb_monitor.sv
│   ├── apb_sequencer.sv
│   └── apb_agent.sv
│
├── scoreboard/
│   └── apb_scoreboard.sv
│
├── coverage/
│   └── apb_coverage.sv
│
├── assertions/
│   └── apb_assertions.sv
│
├── env/
│   └── apb_env.sv
│
├── tests/
│   ├── apb_base_test.sv
│   ├── apb_write_test.sv
│   ├── apb_read_test.sv
│   └── apb_random_test.sv
│
├── tb/
│   └── tb_top.sv
│
├── sim/
│   └── simulation_files
│
└── README.md
```

---

# 🛠️ Technologies Used

* **SystemVerilog**
* **UVM**
* **AMBA APB Protocol**
* **Constrained Random Verification**
* **SystemVerilog Assertions (SVA)**
* **Functional Coverage**
* **Scoreboard-based Checking**
* **Simulation & Waveform Debugging**

---

# ▶️ Simulation

The project can be simulated using a SystemVerilog/UVM-compatible simulator such as:

* Questa/ModelSim
* VCS
* Xcelium

Example UVM test execution:

```text
+UVM_TESTNAME=apb_write_test
```

For a random test:

```text
+UVM_TESTNAME=apb_random_test
```

The exact compilation and simulation commands depend on the simulator and project file structure.

---

# 📊 Verification Flow

```text
Test
  ↓
Sequence
  ↓
Sequencer
  ↓
Driver
  ↓
APB DUT
  ↓
Monitor
  ↓
Scoreboard
  ↓
PASS / FAIL
```

At the same time:

```text
APB Signals
     ↓
Assertions
     ↓
Protocol Violations
```

and:

```text
APB Transactions
     ↓
Functional Coverage
     ↓
Coverage Analysis
```

---

# ✅ Expected Outcome

The verification environment is intended to demonstrate:

* Correct APB read/write functionality.
* Correct APB setup and access phases.
* Correct handling of `PREADY`.
* Correct handling of APB error responses.
* Automated transaction checking.
* Protocol violation detection using assertions.
* Functional coverage of important APB scenarios.
* Reusable UVM testbench architecture.

---

# 🚀 Future Enhancements

Possible extensions include:

* APB4 support with `PSTRB`.
* Multiple APB slave verification.
* APB master and slave agents.
* Register Abstraction Layer (RAL).
* More extensive constrained-random testing.
* Assertion-based verification.
* Coverage-driven verification.
* Regression testing.
* APB-to-AXI/AHB bridge verification.

---

# 📚 Learning Outcomes

Through this project, the following concepts can be practiced:

* APB protocol
* SystemVerilog OOP
* UVM architecture
* UVM agents
* Sequences and sequence items
* Driver and monitor implementation
* Scoreboard development
* Constrained randomization
* Functional coverage
* SystemVerilog Assertions
* Simulation debugging
* Verification planning

---

# 👨‍💻 Author

**MBUTRONICS**

VLSI | Design Verification | SystemVerilog | UVM

---

## ⭐ Project Highlights

```text
✔ AMBA APB Protocol
✔ SystemVerilog
✔ UVM-Based Testbench
✔ Constrained Random Verification
✔ Scoreboard
✔ Functional Coverage
✔ SystemVerilog Assertions
✔ Read / Write Verification
✔ Protocol Checking
✔ Reusable Verification Architecture
```

---

## 📌 Keywords

`APB` `AMBA` `UVM` `SystemVerilog` `VLSI` `Design Verification` `ASIC` `FPGA` `Functional Coverage` `SVA` `Constrained Randomization` `Scoreboard` `Verification`


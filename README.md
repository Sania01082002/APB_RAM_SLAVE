# APB_RAM_SLAVE
# Abstract

The AMBA APB bus is widely used in SoC designs for low-power, low-complexity peripheral communication. This project implements an APB RAM Slave and verifies it using UVM. The verification environment consists of a driver, sequencer, monitor, and scoreboard to validate functional behavior. Various directed and random test scenarios were applied, including multiple resets, write-read data consistency, and error response handling. The scoreboard checked correctness by comparing actual DUT outputs with reference model predictions. Simulation logs confirmed that the DUT passed all test cases without any errors, demonstrating a working verification flow.
# Objective

To design a 32x32 RAM-based APB Slave module.

To build a UVM-based testbench for APB protocol verification.

To verify functional correctness of read/write operations under randomized scenarios.

To check system response during multiple resets and invalid transactions.

To achieve self-checking verification using scoreboard and coverage.

# Working
APB Protocol Overview

The AMBA APB (Advanced Peripheral Bus) is a simple low-power bus used for connecting peripherals. Its operation is based on a two-phase transfer:

Setup phase (PSEL = 1, PENABLE = 0)
Master asserts PSEL along with PADDR, PWRITE, and PWDATA (if write).
Slave sees the address/control signals and prepares for transfer.
Enable phase (PSEL = 1, PENABLE = 1)
Transfer is executed.
For write: slave captures PWDATA into memory at address PADDR.
For read: slave drives PRDATA with the stored value at address PADDR.
Slave asserts PREADY = 1 to indicate transfer completion.

If the slave detects an invalid access, it can assert PSLVERR = 1.


In this project, the DUT is a simple RAM mapped to APB bus.
Memory Array: mem[0:31] stores 32 words of 32-bit data.

Write Operation:

When PWRITE=1, and valid transfer occurs (PSEL & PENABLE),
PWDATA is written into mem[PADDR].
PREADY=1 is asserted after the write.

Read Operation:

When PWRITE=0, and valid transfer occurs,
PRDATA is driven with mem[PADDR].
PREADY=1 signals valid read data.

Error Handling:

If PADDR is outside valid range (0–31), PSLVERR=1.


Step-by-Step Example of Working
Reset Phase:
Initially, PRESETn=0 clears PREADY, PSLVERR, and resets memory.
After reset deasserts, slave is ready for transactions.

Write Cycle(Example: write 0xABCD1234 to address 0x05)

Setup:
PSEL=1, PENABLE=0, PWRITE=1
PADDR=5, PWDATA=0xABCD1234

Enable:
PSEL=1, PENABLE=1
Data 0xABCD1234 is stored at mem[5].
PREADY=1 signals completion.

Read Cycle (Example: read from address 0x05)

Setup:
PSEL=1, PENABLE=0, PWRITE=0
PADDR=5

Enable:
PSEL=1, PENABLE=1
Slave drives PRDATA=0xABCD1234.
PREADY=1 indicates valid read data.

Invalid Access Example
If PADDR=40 (beyond 0–31):
Slave asserts PSLVERR=1.
Data transfer is aborted.

# Result Explanation

Simulation log shows:
Multiple reset detections by driver, monitor, and scoreboard.
Writes correctly reflected in memory.
Reads return correct values if previously written, or 0 if unwritten.
No protocol violations or scoreboard mismatches.

Final UVM summary:
UVM_INFO : 108
UVM_WARNING : 0
UVM_ERROR : 0
UVM_FATAL : 0
→ Clean pass, DUT verified successfully.

# Conclusion
The project successfully implemented and verified an AMBA APB RAM Slave using UVM. The design supported both read and write operations, with correct handling of ready and error signals. The UVM environment, including driver, monitor, sequencer, and scoreboard, was able to generate randomized transactions, apply multiple resets, and validate DUT responses against a reference model. Simulation results showed all operations executed correctly with no functional mismatches, confirming the design’s correctness and robustness.

## Files
- design.sv : Design code
- testbench.sv : Testbench code
- Result_Console1_APB.png : console output
- Result_Console2_APB.png : console output
- Result_Console3_APB.png : console output
- Result_waveform_APB.png : waveform output

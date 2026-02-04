# optoML-Task-
Implementation of a single-stage pipeline register in SystemVerilog using a standard valid/ready handshake. The module stores input data when in_valid and in_ready are asserted, presents stored data at the output with out_valid, and handles backpressure correctly without data loss or duplication. Fully synthesizable and resettable to an empty state

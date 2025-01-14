# RISC-V Processor SMBIOS Tables
## RISC-V SMBIOS Type 4 Processor Information
The information in SMBIOS Type 4 defines the attributes of a single RISC-V processor. Below fields in SMBIOS Type 4 contain RISC-V processor-specific information which is introduced in SMBIOS v3.3.0. All other fields SMBIOS Type 4 are built depends on RISC-V capability accordingly.                                                     

### Offset 06h Processor Family
----------------------------------------------------------
RISC-V processor is cataloged into three families. Due to the width of this filed is in 8-bit length which can't fit RISC-V processor family value, RISC-V processor family is declared in *offset 28h* ***Processor Family 2*** with *offset 06h* ***Processor Family*** set to FEh according to SMBIOS spec.

### Offset 08h Processor ID
----------------------------------------------------------
For RISC-V class CPUs, the processor ID contains a QWORD Machine **Vendor ID CSR (mvendorid)** of RISC-V processor hart 0. More information of RISC-V class CPU feature is described in RISC-V processor additional information (SMBIOS Type 44)
### Offset 26h Processor Characteristics
----------------------------------------------------------
***Processor Characteristics*** is set according to RISC-V processor capability accordingly.
- Both Bit 2 and Bit 8 must be 0 if RISC-V processor only support RV32.
- Bit 2 set to 1 if RISC-V processor supports RV64.
- Bit 8 set to 1 if RISC-V processor supports RV128.
  
| **WORD Bit Position** | **Meaning if Set**|
|---------------|-------------------|
|Bit 0|Reserved|
|Bit 1|Unknown|
|Bit 2|64-bit Capable |
|Bit 3|Multi-Core  |
|Bit 4|Hardware Thread  |
|Bit 5|Execute Protection  |
|Bit 6|Enhanced Virtualization  |
|Bit 7|Power/Performance Control  |
|Bit 8|128-bit Capable |
|Bit 9:15|Reserved |

### Offset 28h Processor Family 2
----------------------------------------------------------
RISC-V processor family is declared in this filed with *offset 06h* ***Processor Family*** set to FEh.

| **Hex Value** | **Decimal Value** | **Meaning**|
|---------------|-------------------|------------|
|200h|512|RISC-V RV32|
|201h|513|RISC-V RV64|
|202h|514|RISC-V RV128|

## RISC-V SMBIOS Type 44 Processor Additional Information
The information in this structure defines the processor additional information in case SMBIOS type 4 is not sufficient to describe processor characteristics. The SMBIOS type 44 record has a reference handle field to link back to the related SMBIOS type 4 record. There may be one or multiple SMBIOS type 44 records linked to the same SMBIOS type 4. For example, where the SMBIOS type 44 records describe each core specific information in a processor. SMBIOS type 44 defines the standard header for the processor-specific block (see section 7.45.1), while the content of processor-specific data is maintained by processor architecture workgroups or vendors in a separate document (see 7.45.2).

### Standard Processor Additional Information (Type 44) structure
-------------------------------------------------------------

Below is the standard header of SMBIOS type 44 defined in SMBIOS spec v3.3.0, the total length of entire SMBIOS type 44 is indicated in *offset 01h* and the value must be less than or equal to 255 in bytes according to section 6.1.2 in <a href="https://www.dmtf.org/standards/smbios">SMBIOS spec 3.2.0 spec</a>.

| **Offset** | **Name**                                            | **Length** | **Value** | **Description**  |
|------------|-----------------------------------------------------|------------|-----------|------------------|
| 00h        | Type                                                | BYTE       | 44        | Processor Additional Information.|
| 01h        | Length                                              | BYTE       | 6+Y       | Length of the structure. Y is the length of Processor-specific Block specified at offset 06h.|
| 02h        | Handle                                              | WORD       | Varies    | Handle, or instance number, associated with the structure.|
| 04h        | Referenced Handle                                   | WORD       | Varies    | Handle, or instance number, associated with the processor (SMBIOS type 4) which the processor additional information describes.|
| 06h        | Processor-specific Block                            | Varies (Y) | Varies    | [Processor-specific block](#processor-specific_block)|

### <a name="processor-specific_block">Processor-specific Block</a>
----------------------------------------------------------

Processor-specific block is the standard header of processor-specific data as defined in SMBIOS section 7.45.1.

| **Offset** |**Name**| **Length** | **Value** | **Description**|
|------------|--------|------------|-----------|---------------|
|00h|Structure Length|BYTE|N|Length of Processor-specific Data
|01h|Processor Type|BYTE|Varies|The processor architecture delineated by this Processor-specific Block. (see [processor architectures types](#processor_architectures_types))
|02h|Processor-specific Data|N BYTEs|Varies|[ RISC-V Processor-specific Data](#risc-v_processor-specific_data)


### <a name="processor_architectures_types">Processor Architecture Types</a>
| **Byte value** |**Meaing**| **Reference** |
|----------------|----------|---------------|
|01h|IA32(x86)|None|
|02h|x64(x86064, Intel64, AMD64, EM64T)|None|
|03h|Intel&reg; Itanium&reg; architecture|None|
|04h|32-bit ARM(Aarch32)|None|
|05h|64-bit ARM(Aarch64)|None|
|06h|32-bit RISC-V(RV32)|see [ RISC-V Processor-specific Data](#risc-v_processor-specific_data)|
|07h|64-bit RISC-V(RV64)|see [ RISC-V Processor-specific Data](#risc-v_processor-specific_data)|
|08h|128-bit RISCV(RV64)|see [ RISC-V Processor-specific Data](#risc-v_processor-specific_data)|

### <a name="risc-v_processor-specific_data">RISC-V Processor-specific Data</a>
----------------------------------------------------------

RISC-V processor-specific additional information is constructed by RISC-V boot loader such as uboot, coreboot, UEFI edk2 firmware, etc. SMBIOS Type 44 is referred by RISC-V software for recognizing RISC-V hart capability. RISC-V SMBIOS Type 44 Processor-specific Block is version controlled which use major and minor versioning in 16-bit value at *offset 0*. The new fields must be added to the end of this table for the backward compatible.

| **Offset** | **Additional Info. Version** | **Name**| **Length** | **Value** | **Description**|
|------------|------------------------------|-----------------------------------------------------------------------|------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 00h        | 0100h (v1.0)| Revision of RISC-V Processor-specific Block Structure         | WORD       | 0100h    | Bit 15:8 Major revision Bit 7:0 Minor revision The newer revision of RISC-V Processor-specific Block Structure is backward compatible with older version of this structure.|
| 02h        | 0100h (v1.0)                 | Structure Length | Byte       | 110         | Length of Processor-specific Data |
| 03h        | 0100h (v1.0)                 | Hart ID          | DQWORD     | Varies    | The ID of this RISC-V Hart        |
| 13h        | 0100h (v1.0)                 | Boot Hart        | BYTE       | Boolean   | 1: This is boot hart to boot system <br>0: This is not the boot hart |
| 14h        | 0100h (v1.0)                 | Machine Vendor ID | DQWORD     | Varies    | The vendor ID of this RISC-V Hart  |
| 24h        | 0100h (v1.0)                 | Machine Architecture ID  | DQWORD     | Varies    | Base microarchitecture of the hart. Value of 0 is possible to indicate the ﬁeld is not implemented. The combination of Machine Architecture ID and Machine Vendor ID should uniquely identify the type of hart microarchitecture that is implemented.  |
| 34h        | 0100h (v1.0)                 | Machine Implementation ID                                             | DQWORD     | Varies    | Unique encoding of the version of the processor implementation. Value of 0 is possible to indicate the ﬁeld is not implemented. The Implementation value should reﬂect the design of the RISC-V Hart. |
| 44h        | 0100h (v1.0)                 | Instruction set supported                                             | DWORD      | Bit-field | Bits [25:0] encodes the presence of RISC-V standard extensions, which is equivalent to bits [25:0] in RISC-V Machine ISA Register (**misa** CSR). Bits set to one mean the certain extensions of instruction set are supported on this hart. |
| 48h        | 0100h (v1.0)                 | Privilege Level Supported                                             | BYTE       | Varies    | The privilege levels supported by this RISC-V Hart. Bit 0 Machine Mode<br>BIT 1 Reserved<br>BIT 2 Supervisor Mode<br>Bit 3 User Mode<br>BIT 6:4 Reserved<br>BIT 7 Debug Mode |
| 49H        | 0100h (v1.0)                 | Machine Exception Trap Delegation Information                         | DQWORD     | Varies    | Bit set to one means the corresponding exception is delegated to supervisor execution environment. Otherwise, supervisor execution environment must register the event handler in Machine-Mode for the certain exceptions through environment call.|
| 59H        | 0100h (v1.0)                 | Machine Interrupt Trap Delegation Information                         | DQWORD     | Varies    | Bit set to one means the corresponding interrupt is delegated to supervisor execution environment. Otherwise, supervisor execution environment must register the event handler in Machine-Mode for the certain interrupts through environment. |
| 69h        | 0100h (v1.0)                 | The register width (XLEN)                                             | BYTE       | ENUM      | The width of register supported by this RISC-V Hart  |
| 6Ah        | 0100h (v1.0)                 | Machine Mode native base integer ISA width (M-XLEN)                   | BYTE       | ENUM      | The width (See below) of Machine Mode native base integer ISA supported by this RISC-V Hart   |
| 6Bh        | 0100h (v1.0)                 | Reserved                                                              | BYTE       | ENUM      | Placeholder for Hypervisor Mode  |
| 6Ch        | 0100h (v1.0)                 | Supervisor Mode native base integer ISA width (S-XLEN)                | BYTE       | ENUM      | The width (See below) of Supervisor Mode native base integer ISA supported by this RISC-V Hart  |
| 6Dh        | 0100h (v1.0)                 | User Mode native base integer ISA width (U-XLEN)                      | BYTE       | ENUM      | The width (See below) of the User Mode native base integer ISA supported by this RISC-V Hart   |

### Encoding of RISC-V Native Base Integer ISA Width
----------------------------------------------------------

| **Byte Value** | **Meaning** |
|----------------|-------------|
| 00h            | Unsupported |
| 01h            | 32-bit      |
| 02h            | 64-bit      |
| 03h            | 128-bit     |

### External Reference
----------------------------------------------------------
SMBIOS Spec -  https://www.dmtf.org/standards/smbios<br>
SMBIOS Type 44 is a new structure introduced to SMBIOS spec. This new definition will be published in SMBIOS spec v3.3.0 which is expected to release in the end of July 2019.

### Contributing
----------------------------------------------------------
#### All contributors are listed as below,
Abner Chang <abner.chang@hpe.com>, **Hewlett Packard Enterprise (HPE)**<br>Gilbert Chen <gilbert.chen@hpe.com>, **Hewlett Packard Enterprise (HPE)**


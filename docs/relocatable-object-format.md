We need some kind of object file format

This is based very loosely on the (`o65` format)[http://6502.org/users/andre/o65/fileformat.html] described by André Fachat.

a module consists of 
    a header ; describes the total static memory required by this module
    an import table ; contains undefined references
    an export table ; contains references defined in the segments 
    up to 65,535 segments ; contain code and data

    ; A fat binary may contain code for different ISAs and platforms.
    ; In some cases, imports may be defined that are not required by 
    ; all cpu/platform combinations
    ; Each segment may contain a relocation table that refers to ordinals
    ; defined in the import and export tables.
    ; A module may define up to 65,535 ordinals.


cpuid: 
    family: 1
        8080: 1
        6800: 2
        6500: 3
        z80: 4
        8086: 5
        56000: 6
        68000: 7
    stepping: 1
        
    stepping: 8-bits

;for version 1, all sizes are 2 bytes, little endian
; minimum stack and heap sizes represent what is required for minimal functionality - if you don't have this much space, don't bother loading 
; requested stack and heap sizes represent what is required for normal operation - competent end users may override these values to match their intended use of the module. The OS should monitor stack and heap usage to tune the system for optimum performance.
header:
    magic: 6
    hlen: 2
    flags: 4
    textlen: size ; size of the main code segment
    overlen: size ; size of the largest code overlay 
    zplen: size ; bytes of zeropage data used
    bsslen: size; bytes of uninitialized static data
    datalen: size; bytes of initialized static data
    rodatalen: size; bytes of read-only static data
    minimum stack size: size
    requested stack size: size
    minimum heap size: size
    requested heap size: size
    option:
        id: 1
        length: 1
        data: varies

options:
    extended cpuid
    platform/operating system/abi

: If the first bit of the flags field is set, and the loader does not understand the type, the load MUST fail with an error.
section:
    length: size
    type: 2
    flags: 2
    data...

; The import table defines ordinals that must be resolved from other modules
import-table:
    length: size
    type: 2 import
    flags: 2
    entries: 2
    entry: *repeats
        ord: 2
        type: byte|wyde|tetra|octa|string|procedure
        name: len:utf8

; The export table defines ordinals intended for public use by other modules
export-table:
    length: size
    type: 2 export
    flags: 2
    entries: 2
    entry: *repeats
        segment: 2   ;the segment id where this is defined
        ord: 2
        offset: size ;the offset within the segment
        type: byte|wyde|tetra|octa|string|procedure
        name: len:utf8

segment:
    header-length: size
    type: text|data|rodata|zpdata|text-overlay (2)
    flags: 2  
        required: 0=ignorable 1=fail if you can't understand this segment
        byte-order: 0=little-endian 1=big-endian
        origin-present:
        name-present:
        cpuid-present:
        payload-present:
        reloc-present:
        
    id: 2
    origin: ptr
    name: utf-8-zero padded to 4 bytes
    cpuid: 2 
    payload-length: size
    payload...
    ; there is potentially a reloc table for each segment, but usually only for code segments
    reloc-table:
        length: size
        type: 2 reloc
        flags: 2
        segment: 2
        entries: 2
        entry:
            ord: 2 ; the target symbol
            count:
                reloc:
                    type: byte|wyde|tetra|octa|pointer|displacement
                    offset: disp ; the offset within the segment the reference occurs

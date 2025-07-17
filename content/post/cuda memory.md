cuda memory model: Hierarchies and bringing it together

What is memory?
-> A storage of information for a certain period of time.

What is Hierarchies?
-> a sequence of storage units spaced at different lenghts with varying dimensions and manufactured in different technologies.

Thread Memory correspondence
-> There is memory that is set private to a certain thread and not shared between the other threads in the same block and grid.
But there is also a memory block that is shared between the threads in the blocks but not with the other blocks in the grid.
After that there is the Global memory that is available to all blocks & the threads within it but not the other grids.


Memory located on the SM is called "On-chip" device memory.

Streaming Multiproccessor==> SM

Each SM has control units and 



ScratchPad memory=> Highly coupled data memory that sits closer to the cpu.



encyclopedia of parallel computiting


SISD-> Single instruction, Single data.
Eg: CPU's

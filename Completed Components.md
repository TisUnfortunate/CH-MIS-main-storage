#Completed Components

## CH MIS Slice
![Component 1](./Component%20Renders/render_CH%20MIS%20slice.png)
**Description**: this is the slice the system will be primarily composed of. Each half consists of 2 single-item allocation chests(CH), and then the MIS atop them.
The MIS is divided into a chest for loose items, and a chest for full boxes. The in-slice logic redirects loose items to the top chest, and boxes to the bottom,
while also providing overflow protection for both. if loose items overflow, then all non full boxes are temped, thus no more loose items are unloaded. full boxes are 
still sent to the slice. if the boxes overflow, non-full boxes continue to be temped, and full boxes are sent to overflow. if the top chest overflows while a box is still
unloading, the box is broken and temped. 
---
## MIS Global Unload
![Component 2](./Component%20Renders/render%20MIS%20global%20unload.png)
**Description**: This system determines what slice, if any, are to be unloaded into for MIS items, and full CH boxes. if a slice is selected, and it is not overflowing, then the SS of the box will be measured.
1 to N-1 is unloaded, N to 14 is temped, and 15 is sent to the slice. If a slice is overflowing, the box is broken and temped unless it is full. after the box task is complete, 
The system relocks the slice and calls for the next box. 
---
## CH matrix unloader
![Component 3](./Component%20Renders/render_nonencoded_matrix_unloader.png)
**Description**:  This system will unload one item type into each row of the CH filters. Since the MS layout is intended to be made of two CH MIS halls, each with 4 lanes of CH, the number of unloaders required is 8.
Each unloader comes equipt with an overflow trigger, which, upon trigger, will break the box and send it down its unloading water stream. The reason for such a procedure is to detect overflow,
collect the overflowing items, and then put them back into the box they were unloaded from. These boxes can then be sent to a temp. 
---
## Variable Merging Compactor
![Component 3](./Component%20Renders/render_variable%20merging%20compactor.png)
**Description**: This component is intended to deal with items that can be compacted, and subsequently reversed into their pre-compacted materials
(e.g. redstone -> redstone blocks, bonemeal -> bone blocks). This process can be slow and space-consuming. This system takes as input full boxes of resources, then a small in-house temp for
the ingredient it will be compacted into. If it finds such a box, then it simultaneously compacts and merges the full box's contents into the partial box, saving on merging operations. If it does not find a box,
then it compacts into an empty box. Any non-full boxes of compacted items are added to the temp. Full boxes are output. can handle all 9-1 compacting recipes. 

## Welcome

Hello, and welcome to my repository for my main storage project. Here you will find a basic premise of the storage, a component gallery, a more detailed explanation of how I am achieving the premises, and much more!

---
## System Premise

Below is a brief summary of this system, and its intended functions. Linked even further down is a whole essay explaining why, and a more detailed summary of the execution.

The primary differences between this system and any standard MS are twofold:
### 1. Hybrid Halls
The Chest halls are a hybrid Box MIS and chest hall. there are two single item allocations and one MIS per halfslice. this allows for denser storage of low qty item types, while still allowing for quick access to higher supply & demand items. the Box MIS bypasses full boxes to keep the loose item chest clear of dominant item types, and sorts them into a separate chest. Further, full boxes of CH allocation items are sent to their corresponding MIS slice instead of being unloaded into the hall to improve item storage density. 
### 2. Advanced Overflow Protection
This system detects overflow from the MIS and the CH, and handles each in detail. If a CH slice overflows, the remaining items are kept in a single-item type box and temped. Once merged, if they are still partial, they will attempt to unload again. If they form a full box, they will be sorted into the MIS box chest. If the MIS loose item chest overflows, any non-full boxes are temped. full boxes continue to be sorted into the slice. if Full Boxes overflow, full boxes are sent to overflow. The MIS temps are local per half hall and accessible to the player. 

### Other Notes:
This system will also have 72 bulk slices(~1mil per half slice), which are fed by box sorters. 
There will be compacting support, and it will run on var sorting to produce single-item type boxes. 

[Essay Here](./System_Explanation_V2.md)

---
## Resources
Below are some things that will be useful in attempting to understand the MS. it can be overwhelming to look at a fully wired Main Storage even as an ST veteran. As such, included here are the components and a flow chart. 

---

[**Components**](./Completed%20Components.md)


[**Interactive Flow Chart**](https://tisunfortunate.github.io/CH-MIS-main-storage/Flow/flowchart.html)

[**Component Checklist**](https://tisunfortunate.github.io/CH-MIS-main-storage/checklist.html)



By TisUnfortunate

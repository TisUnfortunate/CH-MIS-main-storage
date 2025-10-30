## Introduction

The following essay will be laid out into a handful of parts. These parts will cover the primary components of the storage, split into UI and non-UI components.
The purpose of this will be to detail the primary design goals of each component and detail how they accomplish those goals. 
#### Primary UI:
- [Input](#input)
- [CH-MIS hall](#ch-mis-hall)
- [Accessible Temp](#accessible-temp)
- [Bulk Storage](#bulk-hall)
- [Unstackable Storage](#unstackable-storage)

### Primary non-UI:
- [Cart Cycvar](#cart-cycvar)
- [Whitelister](#whitelister)
- [Matrix Unloader](#matrix-unloader)
- [Temp Storage](#temp)
- [Merger](#Merger)
- [Variable Merging Compactor](#variable-merging-compactor)

There are obviously many other components in this project; however, this list should account for all elements that dominate the user interface, and the components that organize items for the
user interface. 

I am assuming that readers have general storage tech knowledge, like what CH, MIS, temp, etc., mean. That being said, if you hover over the name of the component at the top of each section,
I will tooltip a definition. 

--- 
## UI Components
<h3 id="input" title="The input, as the name would imply, is where you give items to the system to be sorted. in more advanced systems, at least in the west, it is the meta to have exclusively boxes be input into the system. Further, there are separate inputs for bulk items, which you have presorted into full single-item type boxes, and mixed boxes, which the system handles more thoroughly.">Input</h3>
I have not started this component yet. I will come back once I have designed it.

<h3 id="ch-mis-hall" title="CH:Chest Hall. Typically refers to stacked single-item allocation filters and chests
MIS: Multi-Item sorter. Typically refers to a chest or multiple chests configured to filter and store up to 54 item types.">CH-MIS hall</h3>
This component is the primary interface for the player. The slice UI will be made of four single-item type allocations and two MIS slices. The single item allocations(SIA)
will each have a double chest capacity. The MIS slice has box support, and as such, full boxes are sorted into the bottom chest, and loose items are sorted into the top double chest. 

#### Design Goals
- Keep regularly demanded items quickly accessible with no search effort
- support full box sorting for every item type
- keep the loose MIS chest free of single dominant item types
- no partial boxes in the box chest
- keep chests quickly searchable
- If loose items overflow, keep everything in boxes, and send full boxes to slice
- if boxes overflow, send to overflow
#### Implementation
The slice itself is fairly straightforward, as it really only needs to accomplish four things: unlock when a matching item type is detected(lime), bifurcate box and loose item inputs(yellow),
detect loose item overflow(blue), and detect box overflow(orange). The logic that does the more complex tasks all happens before the slice. The logic present in the unloader remerges
the key item & box, and checks its signal strength. If it's full, it breaks it and sends it to the slice. If it is partial above a certain signal strength(likely 5), then it breaks the box
and sends it to the accessible temp. Finally, if it's below that certain signal strength, it unloads it into the slice. 

<img src="./Component Renders/CHMIS_Slice_Guide.png" width="300">

#### Meeting Goals:
Within the slice, we have met the overflow goals, as well as preventing single item types from clogging the chest, by preventing boxes above a certain signal strength from unloading. Further,
non-full boxes that are too large to unload are temped and wait for a pair to merge. In order to keep the chests quickly searchable, we only have 1 MIS chest, so you can quickly ascertain if 
you have the item or not. The accessible temp silo will indicate if it has items or not, to let the player decide if they want to search for there item there or not. Full boxes of SIA items, 
instead of being unloaded, will be colored uniquely based on their lane and then sent to the MIS to be sorted into the full box chest, in order to make all full boxes sortable. 
due to the different coloring, they should be easily locatable by simply flicking the chest open. Overflow logic accomplishes exactly what I describe. Regularly searched for items will be kept
in the SIA chests to ease access and increase storage. 

The only real compromise being made is the accessible temp. Having a silo full of partial boxes is not particularly exciting to search, and as such, makes the items less accessible. That being said, I believe it
a worthwhile compromise to keep the box chest less cluttered.

<h3 id = "accessible-temp" title="Temp: a structure meant to store partial single-item type boxes, and when it receives an item, it searches its contents for any box with the same item type. If there is a matching box, it outputs it. If not, it inputs the box it received.
Accessible: the boxes being stored in the temp are available for the player to take from the hall UI. Typically, it refers to a more complex interface. This is a simpler implementation of a similar goal.">Accessible Temp</h3>
There will be four accessible temps in the storage, one per half hall. These exist, as mentioned previously, to keep the Box MIS chests clear of larger partial boxes, since it wouldn't make sense
to unload them.

#### Design Goals:
- Make items as easily accessible to the player
- Auto search for pairs
- Make clear to the player if they should search the temp for their item
- ease search effort for the player
#### Implementation:
When the unloader accesses this component to add a partial, it enters a grouper first, which searches the existing inventory of the silo for a pair. After a search is completed, 
if there was a pair, it is outputted to go to merging, and if not, the box is added to the silo. The silo is made accessible to the player at the end of the hall. 

#### Meeting Goals:
This system, unfortunately, struggles to meet goals. It successfully makes the items accessible to the player and auto-searches for pairs. However, I have struggled to implement any
means to indicate to a player if they should search, or any quick search features. This is largely due to the complexity of implementing such features.
A few solutions I have considered and dismissed:
- dyeing boxes from different slices, different colors
- having per slice indicators of items in temp
- having a per-slice temp system.
This system is the least user-friendly in the whole storage, simply due to the lengthy search time and effort. However, it will likely be sparsely populated and infrequently accessed,
Due to the nature of MIS items in this system. 

<h3 id="bulk-hall" title="Bulk hall: a portion of your storage system dedicated to storing items in massive quantities, typically 1 million or more per item type. It does this by creating single-item type boxes and sorting them into single-item type silos.">Bulk Hall</h3>
This system has 72 Bulk slices, thus making a 36 block long hall. Each will have just over 1 million in storage, since I will likely never need more than that. The hall right now is a modification of Skyzy & Kizus' smart restock bulk. 

#### Design Goals:
- Make full boxes and loose items of bulk items readily accessible to the player
- efficient UI
- sort full boxes
#### Implementation
Bulk halls, in my opinion, are becoming harder and harder to improve. This design has very clean wiring, accepts full boxes as input, keeps all boxes accessible to the player and keeps the slice locked
whenever it can. I don't think I can do better, and so I am content using their design
#### Meeting Goals:
The design goals are very simple here, and as such are met very easily. The hall is a feat of engineering, and I am happy to use someone else's component. 
#### Future Considerations:
I have been playing with the idea of designing a remote bulk for this system. In the case that I do, I will make a separate version that reinvents the bulk system around those considerations,
which will allow for some very exciting features. 
[Remote Bulk Details]()

<h3 id="unstackable-storage" title="Unstackable Storage: usually comprised of an unstackable sorter, which will sort various unstackables by unique detectable interactions, and some storage for those items.">Unstackable Storage</h3>
I have yet to design my unstackable storage. I will use a premade unstackable sorter, and will have box loading of some variety, deciding between accessible loading and a hybrid interface. 

#### Design Goals:
- have ready access to every sorted unstackable
- Have box storage for every unstackable, including unsorted.
- Color boxes differently so that in overflow, they are easy to distinguish

#### Implementation:
N/A
#### Meeting Goals:
N/A

## Non-UI Components

The Following components are responsible for the sorting, unloading, recombining, and non-accessible storage of all the items in the system. They make the system run the way it is supposed to,
and allow the UI to have the features it does. 

<h3 id="cart-cycvar" title="Cycvar: a system that takes as input boxes, and then runs them through a number of slices. Each slice grabs an item type from the first box it receives and sets it as a filter. Every box in the set then checks against that filter, and any of those items are removed and merged into box(es) in that slice. In this way, you guarantee that all items of any item type within an input end up in the same place.
  Cart Cycvar: a cycvar system that uses carts to accelerate the unloading and loading processes.">Cart Cycvar</h3>
Cart Cycvar is an ideal output variable sorting system optimized to deal with large inputs with high item type quantities.

#### Design Goals:
- Improve the cycvar weakness of high cycle latency
- fast cycle reset
- compact slices
- ideal output
#### Implementation:
The system uses a grouper-based cycle handler, which effectively means every box checks every grouper at 8gt, rather than every slice. This significantly reduces cycle time, as boxes do not ever wait for a box to unload to check if they need to be unloaded. all boxes not necessary for the cycle are recycled to the input, and wait for the next cycle. the slice is a 3wt cart cycvar, which has a 7gt cart reset, which translates to ~7.2x HS. At the end of every cycle, all slices are reset and output partial boxes. The next cycle begins during the reset to reduce downtime. The reset logic works in such a way to guarantee all boxes check all groupers, to guarantee ideal output. Unstackables are removed by two LUF systems, one before the buffer and one that every box is processed through after leaving a slice. 
#### Meeting Goals:
The system meets the goals satisfactorily, with the caveat that global LUF is objectively suboptimal. it adds unnecessary travel time to the boxes, making the reset unnecessarily long. There is currently an unresolved bug that happens rarely, where an extra partial is output with large inputs. The slice is 3wt, as is the grouper that feeds it. That being said, the slice is very tall, which is not my favorite.

<h3 id="whitelister" title="Whitelister: A structure that filters for a certain subset of items. 
high resolution whitelister: A whitelister which is made of a series of whitelisters, each which perform different checks. These checks are serialized, and once a check is successful, the rest of the checks are bypassed.">High Resolution Whitelister</h3>
The whitelister routes carts to their various destinations. 

#### Design Goals:
- include SS check
- small footprint
- individual routing for each whitelist(High Resolution)
- separate output for full boxes
- empty cart protection
#### Implementation:
When a box arrives, it is placed, and an isfull check is run. The box is then broken and placed in a cart along with a key. Upon being accepted to a slice, the box and key are recombined, and after recombination, if the box is full, the signal will have propogated to its slice, and spit the box out of the dropper instead of sending it to the barrel. if a cart is spawned, and no key or box are picked up, it will receive 5 blockers and pass harmlessly through the system. The unwhitelisted slice at the end is locked to prevent and blocker loss. The cart and blockers are returned to the input. 
#### Meeting Goals:
All goals are met. Empty cart protection is thorough, the whitelister is very small, as is recombination. Each slice has its own routing and separates full and partial boxes. it is a fairly long whitelister, as each whitelister is 2w, but the front end is reasonably compact. 
#### Further Notes:
1wt slice is possible, though it would not work with my current matrix unloader, and would make a global isfull check more difficult. 

<h3 id="matrix-unloader" title="Matrix Unloading: a method of parallelizing the sorting of items, usually found in encoded tech. it parallelizes by unloading one item type per row of filters, referred to as a lane here. Sorting is used to determine where an item needs to be unloaded. ">Matrix Unloader</h3>
This system is made of 8 unloaders, one for each lane of items. The unloaders are each fed by their own whitelister to ensure all items end up in the correct location. 
#### Design Goals:
- parallelize unloading
- have overflow end up in single item type boxes
- batch items to reduce lag
- reasonable in size

#### Implementation:
This system attaches directly to a 2wt whitelister and can theoretically accept boxes at hopper speed. each slice can buffer over 70 boxes, not that any slice will ever deal with this number of boxes. since one box unloads into one lane, if overflow from a lane is detected, its source is known. each slice has an overflow signal input, which breaks the box the is currently unloading and outputs the box into the item output, which allows it to be sent to a simple device at the end of its lane that will recombine its overflow into it, and then output the box. further, the system will not place boxes for a cycle after the box is broken, to give the overflow time to filter back into the box.
#### Meeting Goals:
This system works in a reasonable footprint and deals with overflow exactly as I had intended. it parallelizes up to 8x depending on the item input, and while on average will be slower than a set based or svar based unloader, its simplicity and overflow features more than make up for its expected speed loss. 
#### Future Considerations:
A 1wt slice may be possible, and worth looking into. could then reduce the size of the whitelister to 1wt as well. However, that would make routing items incredibly difficult. 

<h3 id="temp" title="Temp: a structure meant to store partial single-item type boxes, and when it receives an item, it searches its contents for any box with the same item type. If there is a matching box, it outputs it. If not, it inputs the box it received.">Temp Storage</h3>
Currently intending to use my non-encoded disk drive, which stores up to 390 unique item types. a search process and output takes 400-800gt

#### Design Goals:
- Reasonable Search time
- High Item type capacity
- reasonable footprint
#### Implementation:
N/A
#### Meeting Goals:
N/A

<h3 id="Merger" title="Merger: A structure which takes as input two partial single-item type boxes and removes items from the smaller of the two, and puts them into the larger of the two. the output will be one of three things: a full box, a partial box, or a full and a partial. ">Merger</h3>
Haven't decided if I will use 77s 4x Merger, or design my own 8x merger.  

#### Design Goals:
N/A
#### Implementation:
N/A
#### Meeting Goals:
N/A

<h3 id="variable-merging-compactor" title="Compactable: A Minecraft resource which has an item recipe that condenses 9 of itself into a new variant, which has a reciprocal recipe.
compactor: a component which uses an autocrafter to turn a compactable resource into its compacted variant.">Variable Merging Compactor</h3>
This component takes as input full boxes of a compactable resource, and then checks to see if it has any partial boxes in storage of its compacted variant. It then compacts that full box into its compacted variant at 8x HS,
either into the partial box of the compacted variant, effectively compacting and merging in the same operation, or into a new empty box. Any partial boxes outputted from the compactor are stored to be compacted into, and any full boxes are output.

#### Design Goals:
N/A
#### Implementation:
N/A
#### Meeting Goals:
N/A

## Full System Functioning
These components come together as dictated in the flow chart linked [here](https://tisunfortunate.github.io/CH-MIS-main-storage/Flow/flowchart.html). 
Items flow from the mixed item input to the cycvar, where they are defragmented and combined for ideal output. they then go through the whitelister, which will redirect bulk, compacting, and
SIA items. If the boxes are full, they are sent to bulk storage, the compactor, or the box colorers, respectively. If they are partial, they go to the Temp for the first two, and to the matrix unloader for the SIA.

The Full SIA Boxes rejoin the unwhitelisted boxes at the MIS whitelisters, being unloaded if low qty, temped if medium qty, or sent to slice if they are full. If an item is not sorted, it will end up in the unsorted section.

### Bulk Functioning: 
The Bulk runs on an 8gt box sorter, and due to whitelisting, will only receive boxes it can sort. boxes will all end up in their respective slices. if boxes overflow, they will end up in overflow
### Temp Functioning:
The Temp has its own whitelist, which is checked to direct partials it receives to the correct slice. It will have slots set for bulk items, compactables, and all SIAs. SIAs will occupy the later slots for slower recall, as they will be rarely processed.
### SIA Pathing:
SIA items have a few possible destinations. If there is a full box, they are sent to be recolored, and then into the MIS slices, where they will either end up in their corresponding MIS slice, 
or if its boxes are overflowing, in overflow. If it is a partial, it will be sent to its respective Matrix unloader, which will unload it into the correct lane of filters. If, while unloading, its slice runs out of storage,
the items will overflow to the end of the lane, which is detected. Upon detection, the box is broken and sent to the end of the lane, where it is remerged with the items that overflowed. It is then sent to the temp. After another box of the same type overflows, it will be sent to the temp as well, and the two boxes will be merged and resorted. If they turn into a full box, they will be redirected to the MIS.

### Non-Whitelisted Box Pathing:
All unwhitelisted boxes are sent through the MIS one section sequentially. if they do not belong to a segment, they are sent to the next segment to check there. boxes are unloaded at 1x per segment, and until a box has been completely processed, the next box will not 
be processed. This means the MIS functions at hopper speed if it receives a box to unload in the first slice. However, boxes can unload concurrently in all four segments, assuming ideal ordering.
### Empty Box Routing
Empty boxes come from all over the system. they will all be sent to empty box storage immediately, which has an isbox and is empty check. if it passed those, the box will enter empty box storage. Systems that need empty boxes will call for 
them on demand, and the box storage will send the boxes, dyeing them and then 4D aligning them. This will continue until the request line turns off. Calls will be based on an empty double chest feeding some component, and the call line 
will turn off when the double chest is full. 




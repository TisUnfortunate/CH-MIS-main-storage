_Note this is a first draft, if you somehow find this or I sent it to you please read on with the expectation to be confused_

## Introduction

Storage tech, over the years, has arrived at two visions for dealing with the massive and ever-increasing item diversity in this game. The first is derived from maybe the oldest piece of storage tech,
The Impulse filter. it sorts one item type. if you line enough of these up, you can sort every item in the game. however, having a 700 block long hall is not great. thus, the chest hall was born.
Stacking several single-item filters on top of one another cleverly allows for significantly higher density - 8-10 items sorted per slice. all of a sudden, instead of 700 block of filters, its just 140-170.
much more manageable. however, some of the most primitive storage systems were already doing something more advanced. 
  MIS systems like the Compact Categorizer have also been around for a very long time. These systems sacrificed the storage space in favor of a much smaller footprint, by categorizing many different
  items into each column of chests. This increased density, while leaving the chests a bit messier and giving each item less storage space, has been the choice of many players over the years. 
  
  However, the world of TMC, is not full of normal players. As the game continues to get more features, and redstone continues to develop, the size of projects and the rates of farms have only continued to rise.
  as such, for many endgame players, MIS simply does not cut it, and for that matter, nor do chest halls for many items. This, is where bulk is born. storing a one to two million of a single item types, bulk storages 
  are a keystone of any Technical Player's main storage. Bulk storage, however, is still not suitable for most items in the game. and just because you do not expect to have 1  million of an item or more doesn't mean
  you don't want it sorted. A vast majority of the items in the game will still need to be sorted into something designed to deal with item diversity over item quantity. That being said, the most numerous item types
  have been dealt with. 

  The question then becomes, once again, how do we deal with the sheer amount of item diversity in the game? what do we want out of our UI? storage systems should be reasonable in size, while offering as many features as possible.
  For most players, the selection becomes fairly apparent. either MIS sounds like a nightmare to deal with(redstone complexity, storage capacity, or item search time all suffer). for other players, chesthalls
  dont feel worth it, are too big for what they want, etc. Thus, the tradeoffs are presented. 

## Tradeoffs

### MIS

**Strengths**
1. Small footprint, maximum item type density
2. easy to parallelize
3. can sort boxes
**Weaknesses**
1. lower storage capacity per item type(generally)
2. higher search time, dominant item types can jam up the slice

### Chest Halls

**Strengths**
1. easy searchability
2. higher storage capacity
3. simple redstone

**Weaknesses**
1. more difficult to parallelize
2. significantly lower item density

With these in mind, players choose what they want and what they are willing to give up. There are, of course , things people have done to deal with the weaknesses of either.
Using box sorting, you can significantly increase the storage capacity of MIS(at the detriment of UI), and it is possible to parallelize chest halls to very high speeds
(at the cost of a significant increase in complexity). 

**My Take**

I have always preferred MIS. The idea of massive chest halls has never suited me. I, until recently, didn't really even understand the advantages,
especially with MIS being capable of box sorting. However, with some help(Christone), I realized the issues with the MIS UI. I concluded
(after effectively finishing a massive MIS-based storage) that pure MIS was unsuited for a proper endgame storage. It makes tradeoffs on the account of size 
that I felt were no longer worth it. And so, I began looking at Chest halls.

Unfortunately, I still hated them. The UI bothered me less than it had in the past, but it just felt wasteful. it was like a less aggravating version
of Cubic's accessible loader for every item. I will never need a double chest for wood buttons. at that point, I began thinking about what would birth this project. 


### A solution


The goal of the system I developed was to keep these principles in mind, to maximize the utility of ideal output var sorting, and to maximize flexibility and longevity. with these in mind, I present 
The CH-MIS hall and the TISMS concept. 
  The primary hall in this case is a CH-MIS hybrid. There are 2 CH allocations and 1 box MIS per half-slice, the MIS being the top two chests and the single-item allocations being the bottom two. 
  The MIS is further divided. The bottom chest exclusively receives boxes, and the top only receives loose items. 
  **Box MIS**
  By far the most interesting portion of this hall is the box MIS. As previously mentioned, the MIS slice is subdivided into box storage and loose item storage. This is because full boxes
  are inserted directly into the slice. Thus, if you have a lot of wood buttons left from a project, they do not clog up the loose item chest. Further, there is a temp for each half hall.
  These temps receive MIS boxes that have a medium fill level, and will merge box pairs to return to the system. This further guarantees that there will be no clogging in the MIS slice, either
  of partial boxes or loose items. The temp silo is accessible to the player if they want to check for an item there. If a full box of a chesthall item is detected, 
  it is sent to the corresponding MIS slice and stored as a full box. This significantly expands the storage capacity of single-item allocation items, without sacrificing searchability or item density.
  if loose items fill up, boxes stop unloading, and everything but full boxes go to the temp. If boxes fill up in a slice, full boxes are sent to overflow. Everything else is temped. 

  **CH**
  The CH, by all accounts, is fairly standard. The only point worth noting is that the parallel unloading is 1x per lane, in order to allow for better overflow handling. if overflow is detected,
  The unloading box is broken, and the overflow is recombined with the box. The box is then sent to the system's general temp.

  The overflow features weren't considered until I began wiring the logic, though it works out cleanly. It significantly improves the system's longevity and flexibility since it handles all cases.
  Further, overflow will exclusively be full boxes, which will be colored differently based on the hall of origin for improved searchability. 

  The rest of the system is unrelated to the primary innovation, being the box MIS + CH slice. 



